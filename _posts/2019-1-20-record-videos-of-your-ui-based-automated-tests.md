---
layout: post
title: Record videos of your UI based automated tests
date: 2019-1-20
permalink: /introducing-screen-recorder-ruby-gem
categories:
    - ruby
    - gems
    - automated testing
tags:
    - screen-recorder
    - ruby
    - selenium
    - watir
    - cucumber
    - capybara
    - automated-testing
    - test-recorder
---

My latest project, [screen-recorder](https://github.com/kapoorlakshya/screen-recorder), 
is a Ruby gem that allows you to record your desktop
or a specific application window. The gem is primarily geared towards browser
based automated tests using [Selenium](https://github.com/SeleniumHQ/selenium),
[Watir](https://github.com/watir/watir), or [Capybara](https://github.com/teamcapybara/capybara).
However, any Ruby based project should be able to use it. It works on
Windows, Linux, and macOS.
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
recording your test executions, check out the gem on
[GitHub](https://github.com/kapoorlakshya/screen-recorder). Here is a quick overview of the features:


## Record your desktop

This mode records the whole screen and is best suited if your tests launch
multiple windows or if they resize the browser/GUI during the execution.

```ruby
@recorder = ScreenRecorder::Desktop.new(output: 'recording.mkv')
@recorder.start

# Run tests or whatever you want to record

@recorder.stop
```

<div class="video-responsive">
    <iframe src="https://player.vimeo.com/video/311132029" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen>
    </iframe>
</div>

## Record a specific window (Microsoft Windows only)

This mode records a specific application window with the given
window title. This keeps the focus limited to your application and
keeps the recording size relatively smaller than the desktop mode,
unless of course you maximize the window and record fullscreen.

```ruby
require 'watir'

browser   = Watir::Browser.new :firefox
@recorder = ScreenRecorder::Window.new(title: 'Mozilla Firefox', output: 'recording.mkv')
@recorder.start

# Run tests or whatever you want to record

@recorder.stop
browser.quit 
```

<div class="video-responsive">
    <iframe src="https://player.vimeo.com/video/311132161" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen>
    </iframe>
</div>

<b>Fetch Window Title</b>

A helper method is available to fetch the title of the active window
for the given process name.

```ruby
ScreenRecorder::Titles.fetch('firefox') # Name of exe
#=> ["Mozilla Firefox"]

ScreenRecorder::Titles.fetch('chrome')
#=> ["New Tab - Google Chrome"]
```

This mode has limited capabilities. Read more about it in the wiki [here](https://github.com/kapoorlakshya/screen-recorder/wiki/Window-Capture-Limitations).

## Output

Once the recorder is stopped, you can view the video metadata or transcode it if desired.

```ruby
@recorder.video
=> #<FFMPEG::Movie:0x0000000004327900 
        @path="recording.mkv", 
        @metadata={:streams=>[{:index=>0, :codec_name=>"h264", :codec_long_name=>"H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10", 
        :profile=>"High", 
        :codec_type=>"video"} 
        @video_codec="h264", 
        @colorspace="yuv420p", 
        ... >

@recorder.video.transcode("movie.mp4") { |progress| puts progress } # 0.2 ... 0.5 ... 1.0
```

## Discard Recoding

If your test execution passes, you can easily discard the recording
through `@recorder.discard` or `@recorder.delete`.

## Advanced Options

You can provide additional parameters to FFmpeg using the advanced parameter. 
You can specify input/output specific parameters using the `input: {}` 
and `output: {}` within the `advanced` Hash.

```ruby
advanced = {
  input:    {
    framerate:  30,
    pix_fmt:    'yuv420p',
    video_size: '1280x720'
  },
  output:   {
    r:       15, # Framerate
    pix_fmt: 'yuv420p'
  },
  log:      'recorder.log',
  loglevel: 'level+debug', # For FFmpeg
}
ScreenRecorder::Desktop.new(output: 'recording.mkv', advanced: advanced)
```

This will be parsed as:

```bash
ffmpeg -y -f gdigrab -framerate 30 -pix_fmt yuv420p -video_size 1280x720 -i desktop -r 15 pix_fmt yuv420p -loglevel level+debug recording.mkv
```

## Use with Cucumber

A Cucumber + Watir based example is available 
[here](https://github.com/kapoorlakshya/cucumber-watir-test-recorder-example).

## More Information

A list of planned features and bugs can be found on the [Issues](https://github.com/kapoorlakshya/screen-recorder/issues)
 page.

Thank you for reading!