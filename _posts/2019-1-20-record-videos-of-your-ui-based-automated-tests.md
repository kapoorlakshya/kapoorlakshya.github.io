---
layout: post
title: Record videos of your UI based automated tests
date: 2019-1-20
permalink: /introducing-ffmpeg-screenrecorder
categories:
    - ruby
    - gems
    - automated testing
tags:
    - ruby record screen
    - test recorder
    - ffmpeg-screenrecorder
    - selenium
    - watir
    - cucumber
    - capybara
---

My latest project, [ffmpeg-screenrecorder](https://github.com/kapoorlakshya/ffmpeg-screenrecorder), is a Ruby gem that allows you to record your desktop
or a specific application window. The gem is primarily geared towards browser
based automated tests using [Selenium](https://github.com/SeleniumHQ/selenium),
[Watir](https://github.com/watir/watir), or [Capybara](https://github.com/teamcapybara/capybara).
However, any Ruby based project should be able to use it.
 <!--more-->

If you are familiar with [SauceLabs](https://saucelabs.com) or
[BrowserStack](https://www.browserstack.com/), you may be
aware of their video recording feature. In
addition to providing screenshots and a log, these services provide an option
to record the test execution which could help you easily debug
those UI tests failures. You are able to see the test execution
in action and see what happened before, during, and after a test failure
or an application stack trace. This aids in debugging and documenting test
cases or application bugs.

However, if you are not using these services and are interested in
recording your test executions, check out my gem on
[GitHub](https://github.com/kapoorlakshya/ffmpeg-screenrecorder). Here is a quick overview of the features:


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
@recorder.stop
#=> #<FFMPEG::Movie:0x00000000067e0a08
    @path="screenrecorder-desktop.mp4",
    @container="mov,mp4,m4a,3gp,3g2,mj2",
    @duration=5.0,
    @time=0.0,
    @creation_time=nil,
    @bitrate=1051,
    @rotation=nil,
    @video_stream="h264 (High 4:4:4 Predictive) (avc1 / 0x31637661), yuv444p, 2560x1440, 1048 kb/s, 15 fps, 15 tbr, 15360 tbn, 30 tbc (default)",
    @audio_stream=nil,
    @video_codec="h264 (High 4:4:4 Predictive) (avc1 / 0x31637661)", @colorspace="yuv444p",
    @video_bitrate=1048,
    @resolution="2560x1440">
```

<div class="video-responsive">
    <iframe src="https://player.vimeo.com/video/311132029" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen>
    </iframe>
</div>

## Record a specific window

This mode records a specific application window with the given
window title. This keeps the focus limited to your application and
keeps the recording size relatively smaller than the desktop mode,
unless of course you maximize the window and record fullscreen.

```ruby
require 'watir'

browser = Watir::Browser.new :firefox

# FFMPEG::WindowTitles.fetch('firefox') # Provide name of exe
#=> ["Mozilla Firefox"]

opts      = { input:     FFMPEG::WindowTitles.fetch('firefox').first,
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

<b>Note</b>: This feature is not supported by `x11grab` on Linux. However,
you can set the `x` and `y` coordinates along with
the `video_size` to limit the recording region to the target window.
See example [here](https://trac.ffmpeg.org/wiki/Capture/Desktop).

## Advanced Usage

You can further configure FFmpeg through the `:advanced` key in
your `opts` Hash.

```ruby
opts      = { input:     'desktop',
             output:    'recorder-test.mp4',
             framerate: 15,
             log:       'recorder.log',
             log_level: Logger::DEBUG, # For gem
             advanced: { loglevel: 'level+debug', # For FFmpeg
                         video_size:  '640x480',
                         show_region: '1' }
 }

#
# Command to FFmpeg:
#
# ffmpeg -y -f gdigrab -r 15 -loglevel level+debug -video_size 640x480
#   -show_region 1 -i desktop recorder-test.mp4 2> recorder.log
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
through `@recorder.discard` or `@recorder.delete`.

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