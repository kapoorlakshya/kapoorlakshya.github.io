---
layout: post
title: I created a browser extension to open YouTube links in YouTube Music
date: 2019-5-31
permalink: /chrome-extension-to-open-youtube-links-in-youtube-music
categories:
    - javascript
    - chrome extensions
tags:
    - javascript
    - chrome extension
    - youtube
    - youtube music
---

**TL;DR** - [https://github.com/kapoorlakshya/youtube2music](https://github.com/kapoorlakshya/youtube2music)

I recently switched from [YouTube](https://www.youtube.com/) to 
[YouTube Music](https://music.youtube.com) for my music needs. I made this
decision after realizing how much better the user experience on YouTube 
Music is, and also that I do not care for comments or any of other non-music
related features that YouTube has. 

The only drawback to this decision was that it is not as easy to find newly
published Indian songs on YouTube Music as it is on YouTube. I can't just go to the
channels of the popular Indian music studios and see what's new - channels 
only exist on YouTube. I still have to go to YouTube to get that information, 
and then manually copy-paste the song or album name over to YouTube Music. 
I can't use Spotify either as it doesn't always get the latest songs or 
albums as fast as YouTube does.

**Solution**: Create a Chrome extension to redirect YouTube links to YouTube Music.

**Update** (06/06/2019): The extension supports Chrome, Firefox, and Edge as of v0.2.0.
<!--more-->

To solve this problem, I looked into creating a Chrome extension. The first
hurdle was JavaScript - I only know the very basics of it. The second hurdle
was figuring out the project structure and how to get the required info 
from the YouTube URL and parse it to create and open the target YouTube 
Music URL. 

After about six
hours of reading through the [documentation](https://developer.chrome.com/extensions/getstarted)
and various posts on StackOverflow, I am relieved to present the 
"[YouTube to YouTube Music](https://github.com/kapoorlakshya/youtube2music)" 
extension for Chrome and Chromium based browsers.

### Installation

Chrome - [https://chrome.google.com/webstore/detail/youtube-to-youtube-music/cjcafjnfjeldocjljhejfemlgfogbcjk](https://chrome.google.com/webstore/detail/youtube-to-youtube-music/cjcafjnfjeldocjljhejfemlgfogbcjk)

Firefox - [https://addons.mozilla.org/en-US/firefox/addon/youtube-to-youtube-music/](https://addons.mozilla.org/en-US/firefox/addon/youtube-to-youtube-music/)

Edge - See the [official guide](https://docs.microsoft.com/en-us/microsoft-edge/extensions/guides/adding-and-removing-extensions) for instructions. Microsoft Store link coming soon.

### Usage

Navigate to YouTube, right click any music video, and click on 
**Open in YouTube Music** in the context menu.

![../images/open-video-in-ytm.gif](../images/open-video-in-ytm.gif)

Additionally, you can open all `youtube.com` or `youtu.be` links from 
any other website. The extension enables the link in the context 
menu (right click menu) only if the URL you clicked on has `youtube.com` 
or `youtu.be` in it. Otherwise, the extension stays hidden.

![../images/open-video-in-ytm.gif](../images/open-video-in-ytm-2.gif)

## "This video is not available" Error

Any video which is not categorized as "**Music**" is not available in YouTube Music, and will show a 
"**This video is not available**" error.

![../images/open-video-in-ytm-3.gif](../images/open-video-in-ytm-3.gif)

Feel free to create bug reports and request any features on 
[GitHub](https://github.com/kapoorlakshya/youtube2music/issues).

Thank you for reading!