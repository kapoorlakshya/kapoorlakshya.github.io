---
layout: post
title: Execute WebdriverIO browser tests on AWS Device Farm
date: 2020-3-10
permalink: /executing-webdriverio-browser-tests-aws-device-farm
categories:
  - nodejs
  - webdriverio
  - automated_testing
tags:
  - nodejs
  - webdriverio
  - aws
  - device farm
---

AWS Device Farm recently [announced](https://aws.amazon.com/about-aws/whats-new/2020/01/aws-device-farm-announces-desktop-browser-testing-using-selenium/)
support for desktop browser testing using Selenium on January 14, 2020. The desktop browser service appears to be 
very basic [[1](https://docs.aws.amazon.com/devicefarm/latest/testgrid/techref.html#techref-limitations)]
[[2](https://docs.aws.amazon.com/devicefarm/latest/testgrid/techref-support.html)] when compared to [SauceLabs](https://saucelabs.com/)
or [CrossBrowserTesting](https://saucelabs.com/), the other two services that I have been evaluating as part of my role
at [Wambi](https://wambi.org/).

Since the desktop browser service is fairly new, there is no [service](https://webdriver.io/docs/customservices.html) 
available for it in WebdriverIO. So to make it work, you have to use the `aws-sdk` to generate the remote hub URL and then
use a custom test runner ([launcher](https://webdriver.io/docs/clioptions.html#run-the-test-runner-programmatically))
to use it. As part of my journey to learn Node.js, I have figured it all out and documented it in this post.

I am hoping to create a `wdio-device-farm-service` soon, but for now the following works well :).

 <!--more-->

### Steps

1. Create a Device Farm project
2. Generate a WebDriver (remote) Hub URL
3. Configure WebdriverIO for remote execution
4. Create Grunt task
5. Execute your tests

> Note: This guide assumes WebdriverIO v5 ([instructions](https://webdriver.io/docs/gettingstarted.html)) is already setup with tests ready 
> to be executed.

Okay! Let's do this!

## 1. Create a Device Farm project

The first thing we need to do is create a new desktop browser project on Device Farm. This project is where our tests 
will be executed and tracked. When we're done creating the project, we want to take note of the 
Project [ARN](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html). The Project ARN is what 
we'll use to create a WebDriver (remote) Hub URL in the next step so that our tests are executed under the correct
project.

Log into your [AWS Device Farm console](https://console.aws.amazon.com/devicefarm) and click on "**Desktop browser testing projects**".
Now click on "**Create a new project**", fill in the project name, and click the "**Create project**" button.

Once the project is created, you'll see a unique **Project Arn**:

![../images/aws-project-arn.png](../images/aws-project-arn.png)

> Note: The ARN in the image is invalid and for this example only. Notice `fake` and `test` in it ;)

Copy this ARN and store it in a new environment variable of your choice. I am using `AWS_DF_ARN` for this demo.

## 2. Generate a WebDriver (remote) Hub URL

In this step, we want to use the `aws-sdk` to create a WebDriver Hub URL using the Project ARN for your project. The 
Device Farm API calls this the test grid URL and has the handy `createTestGridUrl()` function to generate it.

If you have not already, then install the `aws-sdk` using NPM or a package manager of your choice:

```bash
npm install aws-sdk
```

Now, either configure the AWS credentials by following these 
[instructions](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/getting-started-nodejs.html#getting-started-nodejs-credentials)
or simply pass them as parameters to `createTestGridUrl()`, like I do in the code below.

Next, we want to create an `async` function called `getTestGridInfo(awsParams)` which will accept parameters, like the
AWS credentials and Project ARN, and return an object (key-value pair) with all the information required by WebdriverIO to 
execute the tests.

Before we get to the function, these are the parameters that we need to provide to successfully connect to the API 
and generate a URL:

```js
// These will be in our custom test runner (step 3)
const awsParams = {
      region: "us-west-2", // Only one available as of March 2020
      accessKeyId: process.env.AWS_ACCESS_KEY_ID,
      secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
      projectArn: process.env.AWS_DF_ARN,
      expiresInSeconds: 300 // URL expires in 5 minutes
    }
```

Click on these links to know what they mean: [`region`](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#region),
[`accessKeyID`](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#accesskeyID),
[`secretAccessKey`](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#SecretAccessKey),
[`projectArn`](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#ARN)

> Note: As of March, 2020 only `us-west-2` region is available.

The `expiresInSeconds` value tells the Device Farm how long you want the Hub URL to stay active. You 
should configure this to be slightly higher than however long your local test suite execution takes. Also note that
every test suite execution will generate its own URL. This ensures that an older, possibly expired URL is never used
to avoid unexpected connection failure.

Once you have the parameters ready, the following function will consume it all and spit out the test grid information:

```js
// ./utils/aws-test-grid-helper.js
const AWS = require('aws-sdk')

async function getTestGridInfo(awsParams) {
  try {
    let testGrid = {
      hostname: `testgrid-devicefarm.${awsParams.region}.amazonaws.com`,
      port: 443,
      protocol: "https",
      path: ""
    }

    const deviceFarm = new AWS.DeviceFarm(awsParams)
    const params = { expiresInSeconds: awsParams.expiresInSeconds, projectArn: awsParams.projectArn }
    const data = await deviceFarm.createTestGridUrl(params).promise()
    testGrid.path = data.url.match(`${testGrid.hostname}(.*)`)[1]
    return testGrid
  } catch (error) {
    console.log(`ERROR: {error}`)
    return
  }
}

module.exports = { getTestGridInfo }
```

Let's store this function in a file called `aws-test-grid-helper.js` under the `utils` directory: 
`./utils/aws-test-grid-helper.js`. We will `require` this in our custom test runner in the next step.

## 3. Configure WebdriverIO

In this step, we'll create a custom test runner or a launcher to programmatically launch our tests as opposed to 
using the `wdio` command. The launcher will live in a file called `aws-launcher.js` inside the `config` directory or 
wherever your WebdriverIO config file lives.

```js
const Launcher = require('@wdio/cli').default
const { getTestGridInfo } = require('../utils/aws-test-grid-helper');

// Retrieves test grid info and kicks off the tests
(async function main() {
    const awsParams = {
      region: "us-west-2", // Only one available as of 02/2020
      accessKeyId: process.env.AWS_ACCESS_KEY_ID,
      secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
      projectArn: process.env.AWS_DF_ARN,
      expiresInSeconds: 86400 // 24 hours
    }
    let testGridInfo = await getTestGridInfo(awsParams);
    const wdio = new Launcher("config/aws-device-farm.conf.js", testGridInfo)

    wdio.run().then((code) => {
      process.exit(code)
    }, (error) => {
      console.error('Launcher failed to start the test', error.stacktrace)
      process.exit(1)
    })
})();
```

Notice this line here: `const wdio = new Launcher("config/aws-device-farm.conf.js", testGridInfo)`

The `config/aws-device-farm.conf.js` points to the configuration file for our test suite. If you're not sure what this
is, read the [Testrunner Configuration](https://webdriver.io/docs/configurationfile.html) documentation on the 
WebdriverIO website. 

And `testGridInfo` is the object (key-value pair) that the `getTestGridInfo(awsParams)` function 
returns with the following information:

```js
hostname:"testgrid-devicefarm.us-west-2.amazonaws.com"
path:"/AQICAHiRxhO-PGIRztfuyZnE-<redacted>-2Q=/wd/hub"
port:443
protocol:"https"
```

When we instantiate the `Launcher`, it will merge the remote Hub information with the configuration from the 
`aws-device-farm.conf.js` file and launch our tests on the Device Farm. This is same as setting the `hostname` and the
rest of the parameters directly in the config file. You can read more on this 
[here](https://webdriver.io/docs/clioptions.html#run-the-test-runner-programmatically).

## 4. Create Grunt task

#### 5. Execute tests