---
layout: post
title: Record videos of your UI based automated tests
date: 2019-1-20
permalink: /introducing-ffmpeg-screenrecorder
tags:
    - ruby record screen
    - test recorder
    - ffmpeg-screenrecorder
    - selenium
    - watir
    - cucumber
    - capybara
---

My latest project is a Ruby gem that allows you to record your desktop
or a specific application window, primarily geared towards browser based
automated tests using [Selenium](https://github.com/SeleniumHQ/selenium),
 [Watir](https://github.com/watir/watir), or [Capybara](https://github.com/teamcapybara/capybara).
 However, any Ruby based project should be able to use it.
 <!--more-->

If you are familiar with [SauceLabs](https://saucelabs.com) or
[BrowserStack](https://www.browserstack.com/), you may be
aware of their video recording feature. In
addition to providing screenshots and a log, these services provide an option
 to record the test execution which could be a painkiller while
debugging those UI tests failures. You are able to see the test execution
in action and see what happened before, during, and after the test failure
or an application stack trace. This makes debugging and documenting test
cases or application bugs (edge cases anyone?) less painful than they can be.

However, if you are not using these services and are interested in
recording your test executions, check out the
[ffmpeg-screenrecorder](https://github.com/kapoorlakshya/ffmpeg-screenrecorder)
gem. Here is a quick overview of the features:


## Record your desktop

This mode records the whole screen and is best suited if your tests launch
multiple windows or if they resize the browser/GUI during the execution.

```ruby
opts      = { input:     'desktop',
              output:    'screenrecorder-desktop.mp4',
              framerate: 15 }
@recorder = FFMPEG::ScreenRecorder.new(opts)
@recorder.start

# Run your test

# Stops FFmpeg and writes video file
@recorder.stop #=> #<FFMPEG::Movie...>
```

<div class="video-responsive">
    <iframe src="https://player.vimeo.com/video/311132029" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen>
    </iframe>
</div>

## Record a specific window

This mode records a specific application window with the given
window title. This keeps the focus limited your application and
keeps the recording size relatively smaller than the desktop mode,
unless of course you maximize the window and record fullscreen.

```ruby
require 'watir'

browser = Watir::Browser.new :firefox

# FFMPEG::RecordingRegions.fetch('firefox') # Provide name of exe
#=> ["Mozilla Firefox"]

opts      = { input:     FFMPEG::RecordingRegions.fetch('firefox').first,
              output:    'screenrecorder-firefox.mp4',
              framerate: 15 }
@recorder = FFMPEG::ScreenRecorder.new(opts)
@recorder.start

# Run tests
browser.goto 'watir.com'
browser.link(text: 'News').wait_until_present.click

# Using rspec-expectations gem
expect(@browser.h2(text: 'News).present?).to be(true)

@recorder.stop
@browser.quit
```

<div class="video-responsive">
    <iframe src="https://player.vimeo.com/video/311132161" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen>
    </iframe>
</div>

<b>Note</b>: This feature is not supported on Linux due to a limitation in
`x11grab`. However, you can set the `x` and `y` coordinates along with
the `video_size` to limit the recording region to the target window.
See example [here](https://trac.ffmpeg.org/wiki/Capture/Desktop).

## Advanced Usage

You can further configure FFmpeg through the `:advanced` key in
your `opts` Hash.

```ruby
opts = { input:     'desktop',
         output:    'recorder-test.mp4',
         framerate: 30,
         log:       'recorder.log',
         log_level: Logger::DEBUG, # For gem
         advanced: { loglevel: 'level+debug', # For FFmpeg
                     video_size:  '640x480',
                     show_region: '1' }
}
```

## Supports Windows, Linux, and macOS

macOS support coming soon. Need to dust off my wife's 2010 MacBook
Pro and finalize the code :)

## Planned features

<b>Transcode recorded videos</b>

Transcode video to GIF, MKV, or whatever your reporting tool supports.

<b>Record audio</b>

Use DirectShow (Windows), ALSA (Linux) or avfoundation (macOS) to
record system audio.

<b>Discard recording</b>

If your test execution passes, you can easily discard the recording
through `@recorder.discard`.

<b>Live stream</b>

For purposes outside of automated testing, this could be a very basic
live streaming option if you operate your own RTMP server. See the
possibilities [here](https://trac.ffmpeg.org/wiki/StreamingGuide).

## Demo

There is a [Cucumber](https://github.com/cucumber/cucumber) +
[Watir](https://github.com/watir/watir) based demo available at
[kapoorlakshya/cucumber-watir-test-recorder-example](https://github.com/kapoorlakshya/cucumber-watir-test-recorder-example).

## Please note

This project is my attempt to give back to the open source
community. It is still in the early stages of development, so please
feel free to report any bugs or request a feature through the
[Issues page](https://github.com/kapoorlakshya/ffmpeg-screenrecorder/issues) on GitHub.