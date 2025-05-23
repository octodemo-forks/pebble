---
# Copyright 2025 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

title: App Metadata
description: |
  Details of the metadata that describes the app, such as its name,
  resources and capabilities.
guide_group: tools-and-resources
order: 0
---

{% alert notice %}
This page applies only to developers using the SDK on their local machine;
CloudPebble allows you to manages this part of development in 'Settings'.
{% endalert %}

## Project Defaults

After creating a new local SDK Pebble project, additional setup of the project
behavior and metadata can be performed in the `package.json` file. By default,
the `pebble new-project` command will create a template `package.json` metadata
file, which can then be modified as required.

The generated `package.json` metadata looks like this:

```js
{
  "name": "example-app",
  "author": "MakeAwesomeHappen",
  "version": "1.0.0",
  "keywords": ["pebble-app"],
  "private": true,
  "dependencies": {},
  "pebble": {
    "displayName": "example-app",
    "uuid": "5e5b3966-60b3-453a-a83b-591a13ae47d5",
    "sdkVersion": "3",
    "enableMultiJS": true,
    "targetPlatforms": [
      "aplite",
      "basalt",
      "chalk"
    ],
    "watchapp": {
      "watchface": false
    },
    "messageKeys": [
      "dummy"
    ],
    "resources": {
      "media": []
    }
  }
}

```

{% alert notice %}
Older projects might have an `appinfo.json` file instead of a `package.json`.
You can run `pebble convert-project` to update these projects.
{% endalert %}

> Note: The `uuid` (Universally Unique IDentifier) field should be unique (as
> the name implies) and always properly generated. To do this on Mac OS X or
> Linux, use the `uuidgen` tool or a web service. Due to format requirements,
> these should never be manually modified.

## Available Properties

Options defined in `package.json` (Required fields shown in **bold**) are
listed in the table below:

| Property | Type | Default Value |Description |
|:---------|:----:|:--------------|:------------|
| **`pebble.uuid`** | UUID | Developer-defined | Unique identifier [(UUID v4)](http://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_.28random.29) for the app. Generated by `pebble new-project`. |
| **`name`** | String | Developer-defined | App's short name. This is only used by npm and can't contain any non-URL-safe characters. |
| **`pebble.displayName`** | String | Developer-defined | App's long name. This is what the app will appear as on the appstore, phone and watch. |
| **`author`** | String | `"MakeAwesomeHappen"` | Name of the app's developer. |
| **`version`** | String | `"1.0.0"` | Version label for the app. Must use the format `major.minor.0`. |
| **`pebble.sdkVersion`** | String | `"3"` | The major version of the SDK that this app is being written for. |
| `pebble.targetPlatforms` | Array of strings | `["aplite", "basalt", "chalk"]` | Specify which platforms to build this app for. Read {% guide_link tools-and-resources/hardware-information %} for a list of available platforms. Defaults to all if omitted. |
| `pebble.enableMultiJS` | Boolean | `true` | Enables [use of multiple JS files](/blog/2016/01/29/Multiple-JavaScript-Files/) in a project with a CommonJS-style `require` syntax. |
| **`pebble.watchapp`** | Object | N/A | Used to configure the app behavior on the watch. See below for an example object. |
| **`.watchface`** | Boolean | `false` | Field of `watchapp`. Set to `false` to behave as a watchapp. |
| `.hiddenApp` | Boolean | `false` | Field of `watchapp`. Set to `true` to prevent the app from appearing in the system menu. |
| `.onlyShownOnCommunication` | Boolean | `false` | Field of `watchapp`. Set to `true` to hide the app unless communicated with from a companion app. |
| `pebble.capabilities` | Array of strings | None | List of capabilities that the app requires. The supported capabilities are `location`, `configurable` (detailed in {% guide_link communication/using-pebblekit-js %}), and `health` (detailed in {% guide_link events-and-services/health %}). |
| `pebble.messageKeys` | Object | `["dummy"]` | Keys used for ``AppMessage`` and ``AppSync``. This is either a list of mapping of ``AppMessage`` keys. See {% guide_link communication/using-pebblekit-js %} for more information. |
| `pebble.resources.media` | Object | `[]` | Contains an array of all of the media resources to be bundled with the app (Maximum 256 per app). See {% guide_link app-resources %} for more information. |
| `pebble.resources.publishedMedia` | Object |  | Used for {% guide_link user-interfaces/appglance-c "AppGlance Slices" %} and {% guide_link pebble-timeline/pin-structure "Timeline Pins" %}. See [Published Media](#published-media) for more information.
> Note: `hiddenApp` and `onlyShownOnCommunication` are mutually exclusive.
> `hiddenApp` will always take preference.

## Hidden Watchapp

The example below shows an example `watchapp` object for a watchapp that is
hidden until communicated with from a companion app.

```js
"watchapp": {
  "watchface": false,
  "hiddenApp": false,
  "onlyShownOnCommunication": true
}
```
## Published Media

Introduced in SDK 4.0, `publishedMedia` provides a mechanism for applications to
specify image resources which can be used within
{% guide_link user-interfaces/appglance-c "AppGlance Slices" %} or
{% guide_link pebble-timeline/pin-structure "Timeline Pins" %}.

The `publishedMedia` object allows your watchapp, Pebble mobile application and
Pebble web services to determine which image resources to use, based on a static
ID. Without this lookup table, the components would not be able to establish the
correct resources to use after an update to the watchapp.

Let's take a look at the structure of `publishedMedia` in more detail.

We'll start by declaring some image resources which we can reference in
`publishedMedia` later. In this example we have 'hot' and 'cold' icons, in 3
different sizes.

```js
"resources": {
  "media": [
    {
      "name": "WEATHER_HOT_ICON_TINY",
      "type": "bitmap",
      "file": "hot_tiny.png"
    },
    {
      "name": "WEATHER_HOT_ICON_SMALL",
      "type": "bitmap",
      "file": "hot_small.png"
    },
    {
      "name": "WEATHER_HOT_ICON_LARGE",
      "type": "bitmap",
      "file": "hot_large.png"
    },
    {
      "name": "WEATHER_COLD_ICON_TINY",
      "type": "bitmap",
      "file": "cold_tiny.png"
    },
    {
      "name": "WEATHER_COLD_ICON_SMALL",
      "type": "bitmap",
      "file": "cold_small.png"
    },
    {
      "name": "WEATHER_COLD_ICON_LARGE",
      "type": "bitmap",
      "file": "cold_large.png"
    }
  ]
}
```

Next we declare the `publishedMedia`:

```js
"resources": {
  // .....
  "publishedMedia": [
    {
      "name": "WEATHER_HOT",
      "id": 1,
      "glance": "WEATHER_HOT_ICON_TINY",
      "timeline": {
        "tiny": "WEATHER_HOT_ICON_TINY",
        "small": "WEATHER_HOT_ICON_SMALL",
        "large": "WEATHER_HOT_ICON_LARGE"
      }
    }, {
      "name": "WEATHER_COLD",
      "id": 2,
      "glance": "WEATHER_COLD_ICON_TINY",
      "timeline": {
        "tiny": "WEATHER_COLD_ICON_TINY",
        "small": "WEATHER_COLD_ICON_SMALL",
        "large": "WEATHER_COLD_ICON_LARGE"
      }
    }
  ]
}
 ```
As you can see above, we can declare multiple entries in `publishedMedia`.

Let's look at the properties in more detail:

* `name` - This must be a unique alias to reference each entry of the
`publishedMedia` object. We use the `name` for the AppGlanceSlice
icon or Timeline Pin icon, and the system will load the appropriate resource
when required. The naming convention varies depending on where it's being used.
For example, the AppGlance C API prefixes the name: `PUBLISHED_ID_WEATHER_HOT`
but the other APIs use: `app://images/WEATHER_HOT`.
* `id` - This must be a unique number within the set of `publishedMedia`. It
must be greater than 0.
* `glance` - Optional. This references the `resource` image which will be
displayed if this `publishedMedia` is used as the `icon` of an
``AppGlanceSlice``. Size: 25x25.
* `timeline` - Optional.  This references the `resource` images which will be
displayed if this `publishedMedia` is used for the various sizes of a timeline
icon. All three sizes must be specified. Sizes: Tiny 25x25, Small 50x50, Large
80x80.

> Your `publishedMedia` entry must include either `glance`, `timeline`, or both.
> If you specify `glance` and `timeline`, the `glance` must be the same resource
> as `timeline.tiny`.
> If you remove a `publishedMedia` entry, you must not re-use the ID.
