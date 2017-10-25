# Silvermine VideoJS Chromecast Plugin

[![Build Status](https://travis-ci.org/silvermine/videojs-chromecast.png?branch=master)](https://travis-ci.org/silvermine/videojs-chromecast)
[![Coverage Status](https://coveralls.io/repos/github/silvermine/videojs-chromecast/badge.svg?branch=master)](https://coveralls.io/github/silvermine/videojs-chromecast?branch=master)
[![Dependency Status](https://david-dm.org/silvermine/videojs-chromecast.png)](https://david-dm.org/silvermine/videojs-chromecast)
[![Dev Dependency Status](https://david-dm.org/silvermine/videojs-chromecast/dev-status.png)](https://david-dm.org/silvermine/videojs-chromecast#info=devDependencies&view=table)


## What is it?

A plugin for [videojs](http://videojs.com/) versions 6+ that adds a button to the control
bar which will cast videos to a Chromecast.


## How do I use it?

The `silvermine-videojs-chromecast` plugin includes 3 types of assets: javascript, CSS, and
images.

You can either build the plugin locally and use the assets that are output from the build
process directly, or you can install the plugin as an npm module, include the
javascript and SCSS source in your project using a Common-JS module loader and SASS build
process, and copy the images from the image source folder to your project.

### Building the plugin locally

   1. Either clone this repository or install the `silvermine-videojs-chromecast` module
      using `npm install silvermine-videojs-chromecast`.
   2. Ensure that `silvermine-videojs-chromecast`'s `devDependencies` are installed by
      running `npm install` from within the `silvermine-videojs-chromecast` folder.
   3. Run `grunt build` to build and copy the javascript, CSS and image files to the
      `silvermine-videojs-chromecast/dist` folder.
   4. Copy the plugin's files from the `dist` folder into your project as needed.
   5. Ensure that the images in the `dist/images` folder are accessible at `./images/`,
      relative to where the plugin's CSS is located. If, for example, your CSS is located
      at `https://example.com/plugins/silvermine-videojs-chromecast.css`, then the plugin's
      images should be located at `https://example.com/plugins/images/`.
   6. Follow the steps in the "Configuration" section below.

Note: when adding the plugin's javascript to your web page, include the
`silvermine-videojs-chromecast.min.js` javascript file in your HTML *after* loading
Video.js. The plugin's built javascript file expects there to be a reference to Video.js
at `window.videojs` and will throw an error if it does not exist.


### Configuration

Once the plugin has been loaded and registered, configure it and add it to your Video.js
player using Video.js' plugin configuration option (see the section under the heading
"Setting up a Plugin" on [Video.js' plugin documentation page][videojs-docs].

**Important: In addition to defining plugin configuration, you are required to define the
player's `techOrder` option, setting `'chromecast'` as the first Tech in the list.** Below
is an example of the minimum required configuration for the Chromecast plugin to function:

```js
var options;

options = {
   controls: true,
   techOrder: [ 'chromecast', 'html5' ], // You may have more Tech, such as Flash or HLS
   plugins: {
      chromecast: {}
   }
};

videojs(document.getElementById('myVideoElement'), options);
```

Please note that even if you choose not to use any of the configuration options, you must
either provide a `chromecast` entry in the `plugins` option for Video.js to initialize the
plugin for you:

```js
options = {
   plugins: {
      chromecast: {}
   }
};
```

or you must initialize the plugin manually:

```js
var player = videojs(document.getElementById('myVideoElement'));

player.chromecast(); // initializes the Chromecast plugin
```

#### Configuration options

##### Plugin configuration
* **`plugins.chromecast.receiverAppID`** - the string ID of a custom [Chromecast receiver
  app][cast-receiver] to use. Defaults to the [default Media Receiver ID][def-cast-id].

##### Chromecast Tech configuration
* **`chromecast.requestTitleFn`** - a function that this plugin calls when it needs a
  string that will be the title shown in the UI that is shown when a Chromecast session is
  active and connected. When the this plugin calls the `requestTitleFn`, it passes it the
  [current `source` object][player-source] and expects a string in return. If nothing is
  returned or if this option is not defined, no title will be shown.
* **`chromecast.requestSubtitleFn`** - a function that this plugin calls when it needs a
  string that will be the sub-title shown in the UI that is shown when a Chromecast
  session is active and connected. When the this plugin calls the `requestSubtitleFn`, it
  passes it the [current `source` object][player-source] and expects a string in return.
  If nothing is returned or if this option is not defined, no sub-title will be shown.

Here is an example configuration object that makes full use of all required and optional
configuration:

```js
var titles, subtitles, options;

titles = {
   'https://example.com/videos/video-1.mp4': 'Example Title',
   'https://example.com/videos/video-2.mp4': 'Example Title2',
};

subtitles = {
   'https://example.com/videos/video-1.mp4': 'Subtitle',
   'https://example.com/videos/video-2.mp4': 'Subtitle2',
};

options = {
   // Must specify the 'chromecast' Tech first
   techOrder: [ 'chromecast', 'html5' ], // Required
   // Configuration for the Chromecast Tech
   chromecast: {
      requestTitleFn: function(source) { // Not required
         return titles[source.url];
      },
      requestSubtitleFn: function(source) { // Not required
         return subtitles[source.url];
      }
   },
   plugins: {
      chromecast: {
         receiverAppID: '1234' // Not required
      },
   }
};
```

##### Localization

The `ChromecastButton` component has one translated string: "Open Chromecast menu". The
"Open Chromecast menu" string appears in both of the standard places for Button component
accessibility text: inside the `.vjs-control-text` span and as the `<button>` element's
`title` attribute.

To localize the Chromecast button text, follow the steps in the
[Video.js Languages tutorial][videojs-translation] to add an `"Open Chromecast menu"` key
to the map of translation strings.

### Using the npm module

If you are using a module loader such as Browserify or Webpack, first install
`silvermine-videojs-chromecast` using `npm install`. Then, use
`require('silvermine-videojs-chromecast')` to require `silvermine-videojs-chromecast` into your
project's source code. `require('silvermine-videojs-chromecast')` returns a function that
you can use to register the plugin with videojs by passing in a reference to `videojs`:

```js
var videojs = require('video.js');

// Initialize the Chromecast plugin
require('silvermine-videojs-chromecast')(videojs);
```

Then, follow the steps in the "Configuration" section above.

### Using the CSS and images

If you are using SCSS in your project, you can simply reference the plugin's main SCSS
file in your project's SCSS:

```scss
@import "path/to/node_modules/silvermine-videojs-chromecast/src/scss/videojs-chromecast";
```

Optionally, you can override the SCSS variables that contain the paths to the icon
image files:

* **`$icon-chromecast--default`** - the path to the icon image that is displayed when the
  Chromecast button is in its normal, default state. Defaults to
  `"images/ic_cast_white_24dp.png"`.
* **`$icon-chromecast--hover`** - the path to the icon image that is displayed when the
  user hovers over the Chromecast button when it is in its normal, default state. Defaults
  to `"images/ic_cast_white_24dp.png"`.
* **`$icon-chromecast-casting`** - the path to the icon image that is displayed when the
  Chromecast button is in the "casting" state (when a Chromecast session is active and
  connected). Defaults to `"images/ic_cast_connected_white_24dp.png"`.
* **`$icon-chromecast-casting--hover`** - the path to the icon image that is displayed
  when the user hovers over the Chromecast button when it is in the "casting" state (when
  a Chromecast session is active and connected). Defaults to
  `"images/ic_cast_connected_white_24dp.png"`.
* **`$chromecast-icon-size`** - the width and height of the icon (the button and icon is a
  square). Defaults to `12px`.
* **`$chromecast-title-font-size`** - the font size of the title on the screen that is
  shown while a Chromecast session is active and connected. Defaults to `22px`.
* **`$chromecast-subtitle-font-size`** - the font size of the sub-title on the screen
  that is shown while a Chromecast session is active and connected. Defaults to `18px`.
* **`$chromecast-poster-width`** - the width of the poster image on the screen that that
  is shown while a Chromecast session is active and connected. Defaults to `100px`.
* **`$chromecast-poster-max-height`** - the maximum height of the poster image on the
  screen that is shown while a Chromecast session is active and connected. Defaults to
  `180px`.


#### Images

The plugin's images are located at `silvermine-videojs-chromecast/src/images`. If you have
not overridden the icon image path variables in the SCSS, then copy the images from the
`src/images` folder to a folder that is accessible at `./images/`, relative to where the
plugin's CSS is located. If, for example, your CSS is located at
`https://example.com/plugins/silvermine-videojs-chromecast.css`, then the plugin's images
should be located at `https://example.com/plugins/images/`.

In addition the the `ic_cast_white_24dp.png` icon image that is used as the default icon for
all four button states ("default", "default + hover", "casting", "casting + hover"), the `images`
folder contains grey, black, and blue versions of the icons.


## How do I contribute?

We genuinely appreciate external contributions. See [our extensive
documentation][contributing] on how to contribute.


## License

This software is released under the MIT license. See [the license file](LICENSE) for more
details.

[videojs-docs]: http://docs.videojs.com/tutorial-plugins.html
[videojs-translation]: http://docs.videojs.com/tutorial-languages.html
[cast-receiver]: https://developers.google.com/cast/docs/receiver_apps
[def-cast-id]: https://developers.google.com/cast/docs/receiver_apps#default
[player-source]: http://docs.videojs.com/Player.html#currentSource
[contributing]: https://github.com/silvermine/silvermine-info#contributing
