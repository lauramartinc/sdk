---
layout: page
permalink: /software/guides/drive-addins/
title: Drive Add-Ins
---

> Geotab Drive Add-Ins are in preview release and subject to change_

## Overview

The MyGeotab Add-In structure can be applied to the Geotab Drive application as well, providing you the ability to extend the functionality for drivers in an environment with sensors (e.g. geolocation and acceleration) and actuators (e.g. voice alerts and notifications). This environment must also be completely operable in an offline state — so your Add-In must be able to handle having no internet connection, or sleeping in the background of the mobile device.

Please read the [Developing Add-Ins](../developing-addins) guide first before attempting a Geotab Drive Add-In.

All Add-Ins that have been designed to work with MyGeotab will work on the Geotab Drive app as well. Your Add-In will be completely downloaded for all referenced links, images, and scripts upon user login. This way, as the user is authenticated over the internet — they will have your Add-In with them as they travel or disconnect from the network. If your Add-In requires dynamic loading of CSS, images, or JavaScript — these requests will fail if the user does not have a network connection. As such you should either: include all dependencies on creation of the Add-In, explicitly link to them, or provide a fallback if **state.online** returns False. Geotab Drive Add-Ins will also display differently, on both the dashboard and the menu.

 ![]({{site.baseurl}}/software/guides/drive-addins_0.png)

To make an Add-In on the Geotab Drive app, the **item** in your configuration file must have a **path** that equals " **DriveAppLink/**" (including a trailing forward slash). The menuName, url, and version will remain unchanged. For example:

### Listing 1 — Geotab Drive "item" configuration

```json
"items": [{
 "version": "1.0",
 "url": "addinFile.html",
 "path": "DriveAppLink/",
 "menuName": {
  "en": "English Label",
  "fr": "French Label"
 }
}]
```

> The array of **items** also allows you to have one link item in MyGeotab, and another link item to Geotab Drive

## API and State Documentation

Inside the Geotab Drive app, we provide the same _api_ and _state_ properties for your initialize method that we do for our normal Add-Ins. In addition to this, we provide you with properties and methods to allow access to mobile device sensors/actuators. See Table 1 below for a list of the properties and methods provided.

### Table 1 — Geotab Drive additional properties and methods

|   | **Description** | **Parameters** | **Return Type** |
| --- | --- | --- | --- |
| api.mobile.exists() | Returns `true` if Geotab Drive is running within a native application, and `false` if just as a HTML5 web application | None | Boolean |
| api.mobile.getVersion() | If `api.mobile.exists()`, gets the Geotab Drive version from the native application | None | String |
| api.mobile.speak() | If `api.mobile.exists()`, uses the text to speech functionality on the mobile device | String | Void |
| api.mobile.notify() | If `api.mobile.exists()`, will add a notification to the top bar of a native operating system Example: `api.mobile.notify("Fill up your vehicle", "Low on gas")` | String[Message], String[Title], String[Id], [String[JsonData]], [Boolean[Permanent]] | Void |
| api.mobile.geolocation | A navigator object that is similar to HTML5 `navigator.geolocation` Example: `api.mobile.geolocation.getCurrentPosition(function (position) { }, function (error) { }, { enableHighAccuracy: true })` | None | None |
| state.device | Get the current vehicle that is being connected to the mobile device | None | String |
| state.driving | Mobile device is detected as driving with the current vehicle | None | Boolean |
| state.charging | Mobile device is being powered | None | Boolean |
| state.background | Geotab Drive application is running in the background | None | Boolean |
| state.online | Mobile device has internet access | None | Boolean |
| state.deviceCommunicating | Telematics device is communicating to the server | None | Boolean |
| state.gpsConnected | Mobile device has GPS enabled | None | Boolean |

## Opening third-party applications using URI schema

> Drive app v4.1.0+

It's possible to open different applications like prontoforms or native calendar from add-ins. To do so, it's important to construct correct URI schema string and pass it to `window.open. For example:

```javascript
window.open(uriSchemaString, "_system")
```

Make sure to read carefully documentation of the app you're trying to open to use correct schema. For example, to open twitter application from addin you should use:

```javascript
window.open("twitter://messages", "_system")
```

You can't use just `twitter://` as it's not correct and app won't open. You need to specify which page you want to open: `messages`, `account` etc.

To open webpage you need to use the same method, but with this notation:

```javascript
window.open("https://google.com", "_blank")
```

> `_blank` is important, especially for iOS devices


## Opening Geotab Drive from third-party applications

On Android and iOS devices with the Geotab Drive app installed, a URL handler is registered which can:

* Launch Drive,
* Navigate to a specified page in Drive, and
* Automatically log into Drive with a token (session ID, user name, and database name) retrieved by authenticating against MyGeotab’s API in a third-party app

Deep linking is used to provide a seamless link from a third-party app into the Geotab Drive app.

On the most basic level, launching Geotab Drive to the main screen, can be executed by creating a link to:

    geotabdrive://

From there, it’s possible to automatically login and/or link to specific modules or pages of the Geotab Drive app.

For more information on how to **Automatic Login, Single Sign-on** and **Navigate to Desired Page** on Geotab Drive app, please refer to [Geotab Drive Single Sign-on and Deep Linking](https://docs.google.com/document/d/1RwIaVmQ6VEYF9BIMlM4Bp2zWP5ulDX5Xh4NVINPDYzA)
