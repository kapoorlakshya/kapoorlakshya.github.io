---
layout: post
title: Record videos of your browser based (Selenium) automated tests
---

My latest project is a Ruby gem that allows you to record your desktop
or a specific application window, primarily geared towards browser
(or any GUI) based automated tests.
<!--more-->

If you are familiar with [SauceLabs](https://saucelabs.com) or
[BrowserStack](https://www.browserstack.com/), you may be
aware of their video recording feature. In
addition to providing screenshots and a log, these services provide a
feature to record the test execution which could be a painkiller while
debugging those UI tests failures. You are able to see the test execution
in action and see what happened before, during, and after the test failure
or an application stack trace. This makes debugging and documenting test
cases or application bugs (edge cases anyone?) less painful than they can be.

However, if you are not using these services and are interested in
recording your test executions, check out the
[ffmpeg-screenrecorder](https://github.com/kapoorlakshya/ffmpeg-screenrecorder)
gem.

## Capabilities

<b>Record your desktop</b>

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

# Video ready at given output path.
```

<b>Record a specific window</b>

This mode records a specific application window with the given
window title. This keeps the focus limited your application and
keeps the recording size relatively smaller than the desktop mode,
unless of course you maximize the window and record fullscreen.

```ruby
require 'watir'

browser = Watir::Browser.new :firefox

FFMPEG::RecordingRegions.fetch('firefox') # Name of exe
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

There a few caveats when using this mode. Read more about this
on the [GitHub page](https://github.com/kapoorlakshya/ffmpeg-screenrecorder).

<b>Advanced Usage</b>

You can further configured FFmpeg through the `:advanced` key in
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

<b>Supports Windows, Linux, and macOS</b>

macOS support coming soon. Need to dust off my wife's 2010 MacBook
Pro and finalize the code :)

## Planned features

<b>Transcode recorded videos</b>

Transcode video to GIF, MKV, or whatever your reporting tool supports.

<b>Record audio</b>

Use DirectShow to record system audio.

<b>Discard recording</b>

If your test execution passes, you can easily discard the recording.

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
feel free to report any bugs or request a feature on the
[Issues page](https://github.com/kapoorlakshya/ffmpeg-screenrecorder/issues) on GitHub.