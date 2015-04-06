# How to play almost all video files in nw.js (node-webkit)

Playing video files using nw.js out-of-the-box is very limited. The list of supported codecs

```
theora,vorbis,vp8,pcm_u8,pcm_s16le,pcm_s24le,pcm_f32le,pcm_s16be,pcm_s24be
```
seems quite impressive, but when you start playing videos with nw you quickly realize that hardly any videos are actually working. Especially H.264 encoded files, one of the most popular codecs, is not supported. nw offers [a wiki page](https://github.com/nwjs/nw.js/wiki/Using-MP3-&-MP4-%28H.264%29-using-the--video--&--audio--tags.) explaining how to make H.264 work, but most video files still fail on nw. Also, using the techniques described, you will need to license your application as `GPL`, i.e. make your project open-source. Using WebChimera you don't have to do that.

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

## Licensing
// -> https://github.com/RSATom/WebChimera/issues/104


## Links

WebChimera homepage
http://www.webchimera.org/

WebChimera on GitHub
https://github.com/RSATom/WebChimera/
