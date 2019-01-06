# Record your browser based automated tests

If you are an automated tester who does browser based testing
using tools like Selenium or Watir, you know how fragile these tests can
be. And debugging them is a strong pain in the butt, especially
when they fail unexpectedly - pass once, fail once, and then pass
again.

You might be now thinking that that's why we invented automated screenshots
and are trained to look at executions logs. While that is true, screenshots
only capture a teeny moment during the failure. It may not be apparent what caused the failure
and how the application or the test behaved before and after that moment. In this case, a
video could make debugging of test failures almost painless. You can
see exactly what happened before the failure, when and possibly why the
failure happened, and what happened after the failure.

In addition to this, here are a few more use cases:

* You can document the test steps to reproduce stack traces or edge cases
and share with your developers.
* In addition to written test steps, you can record the end-to-end
executions for cross-training purpose. Especially useful for those visual
learners on your team.

### Tell me more...

My latest project is a Ruby gem that allows you to record your desktop
or a specific application window, primarily geared towards GUI based
automated tests.

If you have used [SauceLabs](https://saucelabs.com) or
[BrowserStack](https://www.browserstack.com/) before, you may be
familiar with their video recording feature. In addition to providing
screenshots and a log, this feature records the test execution and
could be a pain-killer while debugging those UI tests failures.
However, if you are not using these services and are interested
in recording your test executions, check out the [ffmpeg-screenrecorder](https://github.com/kapoorlakshya/ffmpeg-screenrecorder)
gem.

### Dependencies

The only external dependency is [FFmpeg](https://www.ffmpeg.org/). You
do not need anything else like [screen_capture_recorder](https://sourceforge.net/projects/screencapturer/)
or any paid software.

### Capabilities

###### Supports Windows, Linux, and macOS

macOS support coming soon. Need to dust off my wife's 2010 MacBook
Pro and finalize the code :)

###### Record your desktop

```
opts      = { input:     'desktop',
              output:    '../recordings/screenrecorder-desktop.mp4',
              framerate: 15 }
@recorder = FFMPEG::ScreenRecorder.new(opts)
@recorder.start

# Run your test

@recorder.stop #=> #<FFMPEG::Movie...>

# Video ready at given output path.
```

###### Record a specific window

```
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

# Stop recording
@recorder.stop

@browser.quit
```

There a few caveats when using this mode. Read more about this
on the GitHub page.

### Planned features:

###### Transcode recorded videos

Transcode video to GIF, MKV, or whatever your reporting tool supports.

###### Record audio

Use DirectShow to record system audio.

###### Live stream

For purposes outside of automated testing, this could be a very basic
live streaming option if you operate your own RTMP server. See the
possibilities [here](https://trac.ffmpeg.org/wiki/StreamingGuide).

### Demo

There is a [Cucumber](https://github.com/cucumber/cucumber) +
[Watir](https://github.com/watir/watir) based demo available at
[kapoorlakshya/cucumber-watir-test-recorder-example](https://github.com/kapoorlakshya/cucumber-watir-test-recorder-example).

### Disclaimer

This project is my attempt to give back to the open source
community. It is still in the early stages of development, so please
feel free to report any bugs or request a feature on the
[Issues page](https://github.com/kapoorlakshya/ffmpeg-screenrecorder/issues) on GitHub.