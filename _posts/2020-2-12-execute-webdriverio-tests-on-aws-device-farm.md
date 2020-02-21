---
layout: post
title: Executing WebdriverIO browser tests on AWS Device Farm
date: 2020-2-12
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

AWS Device Farm recently rolled out support for desktop browser testing using Selenium on January 14, 2020 ([announcement](https://aws.amazon.com/about-aws/whats-new/2020/01/aws-device-farm-announces-desktop-browser-testing-using-selenium/)). The desktop browser service appears to be very basic when compared to SauceLabs or CrossBrowserTesting, the other two services that I have been evaluating as part of my new role at [Wambi](https://wambi.org/).

Since the desktop browser service is fairly new, there is no [service](https://webdriver.io/docs/customservices.html) available for it in WebdriverIO. So to make it work, you have do a bunch of manual steps that I have figured out, partially automated, and documented in this post.

 <!--more-->

### Steps

1. Create a new project
2. Generate a remote URL
3. Configure WebdriverIO for remote execution
4. Execute your tests

Okay! Let's do this!

#### 1. Create a new project

#### 2. Generate a remote URL

#### 3. Configure WebdriverIO for remote execution

#### 4. Execute your tests
