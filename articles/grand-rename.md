---
title: TelemetryDeck's Grand Rename
tags: setup
description: To avoid ambiguity and clean up things for the long term, we have decided to rename quite a few things in all the TelemetryDeck SDKs. We have laid out a migration path, here's all you need to know about it.
lead: To avoid ambiguity and clean up things for the long term, we have decided to rename quite a few things in all the TelemetryDeck SDKs. We have laid out a migration path, here's all you need to know about it.
---

## Motivation

When we started TelemetryDeck, it was clearly scoped to the iOS platform and we naturally started with a Swift client first. To get to speed quickly like one does, we decided to send a defualt signal & a bunch of parameters automatically. To keep things simple, we named the signal `newSessionBegan` and the parameters `appVersion` and `locale`, for example.

But over time, we added more and more SDKs and now we have 5, but only 4 of them are also called "SDK": The initial Swift client was simply called "SwiftClient". We also used the platform-specific terminology for things, leading to different names in some places. And we also learned the power of grouping signal names and event parameters as documented in our [naming guideline](https://telemetrydeck.com/docs/articles/signal-type-naming/). Because we sort everything alphabetically in our UI, it's a natural help in finding things more quickly if related things have the same prefix. Also, users could accidentally use the same signal or parameter name and override ours, causing confusion in the insight data.

After more than 3 years working on TelemetryDeck, we think it's about time to streamline things for a more clear long-term future.

## Consistency, Longevity, and Clarity

Our main goals with our new naming scheme were to be internally consistent, but at the same time make sure the names are abstract enough to work flexibly for changes to come in the foreseeable future. Too abstract names can be confusing and cumbersome, so we additionally strived for clarity.

The most obvious change was to rename our Swift repository from "SwiftClient" to "SwiftSDK". Thanks to GitHub redirections, this change should have no effect on our existing customers, but long term, it should make things more consistent. 🎉

For our signals & parameters, we decided to prefix them with `TelemetryDeck` to make the source clear and avoid ambiguity. Additionally, we analyzed all the signals and parameters we currently send and some future ones we have in our roadmap and grouped them as follows:

### Signals

- `Session`: Anything related to starting, pausing, continuing, or ending a user session.

### Parameters

- `AppInfo`: Information about the specific app build, such as version, build number, or SDKs compiled with.
- `Device`: All about the device running the application, such as operating system, model name, or architecture.
- `Metric`: Information about app and device performance, such as memory (RAM), battery, or charging status.
- `RunContext`: The context the app runs in, such as simulator, debug mode, or target environment.
- `SDK`: Information about the TelemetryDeck SDK, such as its name or version number.
- `UserPreference`: Any choices the user made to express personal preferences, such as language & region.
- `Route`: Information about how a user is navigating through the app, such as URL paths or view controllers.

{% noteinfo "No immediate action needed" %}
To make sure these changes don't affect existing customers with existing insights anytime soon, we decided to keep sending the old names for a transition period of at least 1 year while also sending the new ones. We recommend using the new ones for any new insights you create and will elaborate ways to support our users in transitioning any existing insights before we fully migrate.
{% endnoteinfo %}

## Filling the Gaps

While doing the rename, we also noticed some missing variants of parameters that could be useful. So we added the following variants:

- `TelemetryDeck.AppInfo.versionAndBuildNumber`: Combines the app version and build into one String, such as `1.7.1 (build 22)`.
- `TelemetryDeck.Device.orientation`: One of `Portrait`, `Landscape`, or `Fixed`.
- `TelemetryDeck.Device.screenResolutionWidth`: The resolution width of the screen in pixel/points (whatever is most common on platform).
- `TelemetryDeck.Device.screenResolutionHeight`: The resolution height of the screen in pixel/points (whatever is most common on platform).
- `TelemetryDeck.Device.timeZone`: The timezone expressed by the UTC offset, such as `UTC+0` (London), `UTC+9` (Tokyo), or `UTC-8` (San Francisco).
- `TelemetryDeck.RunContext.language`: The language the app is currently used in (one of the supported languages).
- `TelemetryDeck.SDK.name`: Just the TelemetryDeck SDK's name that was used to send the signal, such as `SwiftSDK`, `KotlinSDK`, or `FlutterSDK`.
- `TelemetryDeck.SDK.version`: Just TelemetryDeck SDK's version that was used to send the signal, such as `1.5.1`.
- `TelemetryDeck.UserPreference.region`: The regional identifier of the users country setting, e.g. `US` for United States.
- `TelemetryDeck.UserPreference.language`: The language most preferred by the user on this device (might not be supported by app).

These could be useful to create even more accurate charts to make better informed data-driven decisions.

## Full Migration Table

Here's a full overview of all the changes we have done:

| Old Name                   | New Name                                                |
| -------------------------- | ------------------------------------------------------- |
| ActivityCreated            | Deleted (was sending too many signals)                  |
| ActivityDestroyed          | Deleted (was sending too many signals)                  |
| ActivityPaused             | Deleted (was sending too many signals)                  |
| ActivityResumed            | Deleted (was sending too many signals)                  |
| ActivitySaveInstanceState  | Deleted (was sending too many signals)                  |
| ActivityStarted            | Deleted (was sending too many signals)                  |
| ActivityStopped            | Deleted (was sending too many signals)                  |
| AppBackground              | Deleted (was sending too many signals)                  |
| AppForeground              | Deleted (was sending too many signals)                  |
| NewSessionBegan            | TelemetryDeck.Session.started                           |
| newSessionBegan            | TelemetryDeck.Session.started                           |
| -------------------------- | ------------------------------------------------------- |
| buildNumber                | TelemetryDeck.AppInfo.buildNumber                       |
| dartVersion                | TelemetryDeck.AppInfo.dartVersion                       |
| appVersion                 | TelemetryDeck.AppInfo.version                           |
| architecture               | TelemetryDeck.Device.architecture                       |
| brand                      | TelemetryDeck.Device.brand                              |
| modelName                  | TelemetryDeck.Device.modelName                          |
| operatingSystem            | TelemetryDeck.Device.operatingSystem                    |
| platform                   | TelemetryDeck.Device.platform                           |
| majorSystemVersion         | TelemetryDeck.Device.systemMajorVersion                 |
| majorMinorSystemVersion    | TelemetryDeck.Device.systemMajorMinorVersion            |
| systemVersion              | TelemetryDeck.Device.systemVersion                      |
| extensionIdentifier        | TelemetryDeck.RunContext.extensionIdentifier            |
| isAppStore                 | TelemetryDeck.RunContext.isAppStore                     |
| isDebug                    | TelemetryDeck.RunContext.isDebug                        |
| isSimulator                | TelemetryDeck.RunContext.isSimulator                    |
| isTestFlight               | TelemetryDeck.RunContext.isTestFlight                   |
| locale                     | TelemetryDeck.RunContext.locale                         |
| targetEnvironment          | TelemetryDeck.RunContext.targetEnvironment              |
| telemetryClientVersion     | TelemetryDeck.SDK.nameAndVersion                        |

We hope you like these changes as much as we do!
While we did our best to not affect any data, we might have missed something, so please contact us if you run into any issues.
