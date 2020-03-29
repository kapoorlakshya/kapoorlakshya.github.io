---
layout: page
title: Open Source Contributions
permalink: /contributions/
---
## Author & Maintainer :muscle:

- ### screen-recorder (Author)
A Ruby gem to video record and take screenshots of your desktop or specific application window. Works on Windows, 
Linux, and macOS. [View on GitHub](https://github.com/kapoorlakshya/screen-recorder)

- ### Webdrivers gem (Co-maintainer)
Run Selenium tests more easily with automatic installation and updates 
for all supported webdrivers. **Now bundled with Ruby on Rails 6!**
[View on GitHub](https://github.com/titusfortner/webdrivers)

- ### youtube2music (Author)
Browser extension to open YouTube links in YouTube Music. Works on Chrome,
Firefox, and Edge. [View on GitHub](https://github.com/kapoorlakshya/youtube2music)

<br />
## Bug Fixes & Enhancements :+1:

- ### WebdriverIO
Next-gen browser automation test framework for Node.js
    - Enhancement: [Forward service defined path (driver server endpoint) for Selenium server](https://github.com/webdriverio/webdriverio/pull/5145)

- ### Watir
An open source Ruby library for automating tests. Watir interacts with a 
browser the same way people do: clicking links, filling out forms and 
validating text.

    - Enhancement: [Element#right_click now accepts modifiers.](https://github.com/watir/watir/pull/861)
    - Enhancement: [Add Watir::Element#attribute_values and #attribute_list which return 
    attributes from an element.](https://github.com/watir/watir/pull/775)
    - Bug fix: [Rescue from Selenium::WebDriver::Error::NoSuchAlertError.](https://github.com/watir/watir/pull/680)

- ### heroku/heroku-buildpack-google-chrome
This buildpack downloads and installs (headless) Google Chrome on Heroku.
    - Enhancement: [GOOGLE_CHROME_SHIM now responds to --version and --product-version](https://github.com/heroku/heroku-buildpack-google-chrome/pull/73)