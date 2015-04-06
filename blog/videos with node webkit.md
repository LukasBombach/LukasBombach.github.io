# How to play almost all video files in nw.js (node-webkit)

Playing video files using nw.js out-of-the-box is very limited. The list of supported codecs

```
theora,vorbis,vp8,pcm_u8,pcm_s16le,pcm_s24le,pcm_f32le,pcm_s16be,pcm_s24be
```
seems quite impressive, but when you start playing videos with nw you quickly realize that hardly any videos are actually working. Especially H.264 encoded files, one of the most popular codecs, is not supported. nw offers [a wiki page](https://github.com/nwjs/nw.js/wiki/Using-MP3-&-MP4-%28H.264%29-using-the--video--&--audio--tags.) explaining how to make H.264 work, but most video files still fail on nw.

## WebChimera

WebChimera is a plugin that uses [Firebreath](http://www.firebreath.org/) to run [VLC](http://www.videolan.org/) in your browser—and it works quite well. The best part is, you can use this plugin together with nw.js to fill the gap of unsupported videos and actually play almost all videos in your nw application.

Once WebChimera is installed, you can simply add an html-tag for your video, provide it with a video source, local or remote, and play the video embedded in your page. You can either write interfaces for it using HTML, CSS & JavaScript—or use QML, the language to write interfaces for QT applications.

Since WebChimera runs VLC you will also be able to play almost any audio files, like mp3, with it.

## Set up

# Mac version

HOW TO EMBED
 * https://github.com/RSATom/WebChimera/issues/103#issuecomment-89377401
 * https://github.com/jaruba/WebChimeraPlayerNW

nw.js is very limited when it comes to playing video (and audio) files. [There is a tutorial](https://github.com/nwjs/nw.js/wiki/Using-MP3-&-MP4-%28H.264%29-using-the--video--&--audio--tags.) that helps you to play slightly more audio and video files, but the support is still very limited. You can use `WebChimera` to play videos in nw.js. WebChimera is a plugin for Chrome (and others) that uses LibVLC (?) to play videos in the browser. Cool thing you can use it in nw too! Yay. That way you can play all video formats supported in VLC in your nw application.

// Research on licensing issues.
// -> https://github.com/RSATom/WebChimera/issues/104

## Download stuff

1. [Download the latest nw.js](https://github.com/nwjs/nw.js/) (formerly known as node-webkit) * 32 / 64 bit überprüfen
2. [Download the latest WebChimera](http://www.webchimera.org/download) (GitHub [here](https://github.com/RSATom/WebChimera/))

## Set up project

1. Extract nw.js
2. In your nw root folder create a folder called "plugins".
3. Open your WebChimera download and put `WebChimera.plugin` in the "plugins" folder you just created.
4. In your project root, create a `package.json` as follows

```javascript
{
  "name": "nw-videoplayer",
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

first. This setting will make `nw` look for plugins in the `plugins` folder and ultamately load `WebChimera`. Second of all

```javascript
"main": "app://host/index.html",
```

will make `nw` load local files. This will be important because we will implement the interface for the video player in a separate file that `nw` needs to load from the local file system.

## Instantiate video player

Create a file called `index.html`

```html
<!DOCTYPE html>
<html>
<body>
<object id="plugin_inst_1" type="application/x-chimera-plugin" width="600" height="338">
    <param name="mrl" value="http://download.blender.org/peach/bigbuckbunny_movies/big_buck_bunny_480p_stereo.avi" />
</object>
</body>
</html>
```

Open your `nw` app. Boom. You already have a video player application that supports all files that are supported by VLC.

## Implementing an interface

Interfaces for WebChimera can be written in 2 ways:

 * Using QML
 * Using HTML / CSS / JavaScript

## Implementing an interface with QML

QML (Qt Meta Language or Qt Modeling Language) is a user interface markup language for QT applications. It is similar to CSS pimped with dynamics of JavaScript and you can write user interfaces for WebChimera with it. Let's look at a basic example. Add a file called `player.qml` to your project's root directory with the following code:

```qml
import QtQuick 2.1
import QtQuick.Layouts 1.0
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
This will add



## Links

WebChimera homepage
http://www.webchimera.org/

WebChimera on GitHub
https://github.com/RSATom/WebChimera/
