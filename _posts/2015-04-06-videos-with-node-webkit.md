---
title: How to play almost all video files in nw.js (node-webkit)
layout: post
---
# How to play almost all video files in nw.js (node-webkit)

Playing video files using nw.js out-of-the-box is very limited. The list of supported codecs

```
theora,vorbis,vp8,pcm_u8,pcm_s16le,pcm_s24le,pcm_f32le,pcm_s16be,pcm_s24be
```
seems quite impressive, but when you start playing videos with nw you quickly realize that hardly any videos are actually working. Especially H.264 encoded files, one of the most popular codecs, is not supported. nw offers [a wiki page](https://github.com/nwjs/nw.js/wiki/Using-MP3-&-MP4-%28H.264%29-using-the--video--&--audio--tags.) explaining how to make H.264 work, but most video files still fail on nw. Also, using the techniques described, you will need to license your application as `GPL`, i.e. make your project open-source. Using WebChimera you don't have to do that.

## Download / Demo / tl;dr

You can download the final project from GitHub, the code is quite simple:

https://github.com/LukasBombach/nw-webchimera-demos

## WebChimera

WebChimera is a plugin that uses [Firebreath](http://www.firebreath.org/) to run [VLC](http://www.videolan.org/) in your browser—and it works quite well. The best part is, you can use this plugin together with nw.js to fill the gap of unsupported videos and actually play almost all videos in your nw application.

Once WebChimera is installed, you can simply add an html-tag for your video, provide it with a video source, local or remote, and play the video embedded in your page. You can either write interfaces for it using HTML, CSS & JavaScript—or use QML, the language to write interfaces for QT applications.

Since WebChimera runs VLC you will also be able to play almost any audio files, like mp3, with it.

## Install nw.js and WebChimera

To get started, first [download the latest nw.js](https://github.com/nwjs/nw.js#downloads), `x64` for OS X, `ia32` for Windows, and extract it. Then [download WebChimera](http://www.webchimera.org/download) for your platform.

### For OS X
In your nw root directory (where you will place your `package.json`) create a folder called `plugins`. Open the WebChimera `.dmg` you just downloaded and copy the `WebChimera.plugin` file to that folder.

### For Windows
Simply use the WebChimera installer you just downloaded and install WebChimera globally. nw.js automatically has access to all plugins installed for your browser, so installing WebChimera like this will also make any nw application able to use it. Don't worry, when you ship your nw application, you can ship WebChimera with it *without* requiring your users to install anything. [@jaruba](https://github.com/jaruba) has set up [a repository](https://github.com/jaruba/WebChimeraPlayerNW) to help you do that. But more on that later.

## Set up your project

Once you have extracted nw.js and installed WebChimera you can start creating your actual application. First, create a `package.json` in you project root as follows:

```javascript
{
  "name": "nw-webchimera",
  "main": "app://host/index.html",
  "webkit": {
    "plugin": true
  }
}
```

This contains 2 important settings. Let's look at

```javascript
"webkit": {
  "plugin": true
}
```

first. This setting will make `nw` look for plugins in the `plugins` folder of your project and ultimately load `WebChimera`. This is not required for Windows applications at this point, but if you want to ship cross-plattform applications you should do this.

Secondly

```javascript
"main": "app://host/index.html",
```

will enable `nw` load files locally. This will be important when we implement the interface for the video player using QML, since we wil put the QML code in a separate file that `nw` needs to load from the file system.

## Instantiate video player

Create a file called `index.html` in your root directory:

```html
<!DOCTYPE html>
<html>
<body>
<object type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
</object>
</body>
</html>
```

Open your `nw` app. Boom. You already have a video player application that supports all files that are supported by VLC.

## Implementing an interface

Interfaces for WebChimera can be written in 2 ways:

 * Using QML
 * Using HTML, CSS & JavaScript

## Implementing an interface with QML

QML (Qt Meta Language or Qt Modeling Language) is a user interface markup language for QT applications. It is similar to CSS pimped with dynamics of JavaScript and you can write user interfaces for WebChimera with it. Let's look at a basic example. Add a file called `player.qml` to your project's root directory with the following code:

```qml
import QtQuick 2.1
import QmlVlc 0.1

Rectangle {

    color: bgcolor

    VlcVideoSurface {

        source: vlcPlayer
        anchors.fill: parent

        Rectangle {

            color: '#BFFFFFFF'
            radius: 5
            width: 220
            height: 50
            anchors.horizontalCenter: parent.horizontalCenter
            anchors.bottom: parent.bottom
            anchors.bottomMargin: 20

            MouseArea {
                anchors.fill: parent
                onClicked: vlcPlayer.togglePause()
            }
        }
    }
}
```

Then add a param with the name `qmlsrc` and the value `player.qml` to your `<object>` tag like this:

```html
<object type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
    <param name="qmlsrc" value="player.qml" />
</object>
```

This will add a rounded rectangle at the bottom of your player, click it to pause and unpause the video. This tutorial should not be about how to write QML, but the basics are that QML contains of nested objects, in this case a `VlcVideoSurface` containing a `Rectangle` containing a `MouseArea`. Each object can reference its parent and using the `anchor` attributes position itself relative to it. You can find a very nice article explaining the basics of writing QML on WebChimera's wiki on GitHub: https://github.com/RSATom/WebChimera/wiki/Getting-started-with-QML

## Implementing an interface using HTML, CSS & JavaScript

Implementing an interface for WebChimera using HTML, CSS & JavaScript is as straight forward as creating the interface elements using HTML & CSS and using JavaScript to make WebChimera pause and play when the user performs a click. WebChimera [has a JavaScript API](https://github.com/RSATom/WebChimera/wiki/JavaScript-API) that gives you full control over the video.

If you have just modified your `<object>` code, undo your changes. So your `<object>` tag should look like this again:

```html
<object type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
</object>
```

Right after your `<object>`, create a div with the id `interface`:

```html
<object type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
</object>
<div id="interface"></div>
```

Add the following CSS to your page:

```html
<style>
#interface {
    position: absolute;
    top: 268px;
    left: 190px;
    width: 220px;
    height: 50px;
    background: rgba(255, 255, 255, 0.75);
    border-radius: 5px;
}
</style>
```
And *at the bottom* of your `<body>`:

```html
<script type="text/javascript">
    var player = document.getElementById('player');
    document.getElementById('interface').addEventListener('click', function() {
        player.togglePause();
    });
</script>
```

which should give you the following HTML page

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <style>
        #interface {
            position: absolute;
            top: 268px;
            left: 190px;
            width: 220px;
            height: 50px;
            background: rgba(255, 255, 255, 0.75);
            border-radius: 5px;
        }
    </style>
</head>
<body>

<object id="player" type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
</object>
<div id="interface"></div>

<script type="text/javascript">
    var player = document.getElementById('player');
    document.getElementById('interface').addEventListener('click', function() {
        player.togglePause();
    });
</script>

</body>
</html>
```

So simply put, we added a `<div>` to our markup, styled it with CSS to lay it over our video and called the `togglePause()` method of the video player when the user clicks it. You should see a white, slightly transparent rounded rectangle overlaying the video and pause/unpause it by clicking on it. This way you can build any interface you want using the web-technlogy you are familiar with.

## Shipping your application

### OS X

Distributing your application for OS X is quite simple. Just include the `WebChimera.plugin` in you `plugins` folder and everything works out-of-the-box.

### Windows

If you are using Windows, you had to run the WebChimera installer to make WebChimera work. Of course you don't want your users to install your application—and WebChimera. Don't worry, this can be done. Jaruba has set up a GitHub repository explaining how to ship your application as "standalone", including all WebChimera files that are required. [You can find the repository here](https://github.com/jaruba/WebChimeraPlayerNW). I am pretty sure this will be easier in the future.

## Licensing

As mentioned in the introduction, using the techniques proposed on the nw.js wiki, enabling H.264 support will require you to release your application licensed as GPL too. I.e. you must make your entire source code open-source. Luckily, using WebChimera, you don't have to do that. WebChimera itself is licensed as [LGPL-2.1](https://tldrlegal.com/license/gnu-lesser-general-public-license-v2.1-(lgpl-2.1)), but since this is a standalone library, only the library needs to be public. Your code can stay closed using the license that you choose.

## Links

WebChimera homepage
http://www.webchimera.org/

WebChimera on GitHub
https://github.com/RSATom/WebChimera/

WebChimera JavaScript API
https://github.com/RSATom/WebChimera/wiki/JavaScript-API
