---
title: React and React Native Setup Guide
tags:
  - Setup
  - JavaScript
  - Node
  - NPM
  - TypeScript
  - React
  - React Native
featured: true
description: How to include the TelemetryDeck SDK in Your React or React Native App
lead: You have an account, and you have an app ID. Now let's include the TelemetryClient Package in your React Native application or React Web App
order: 300
---

## Prerequisites

<!-- vale proselint.Cliches = NO -->

- You'll need a TelemetryDeck account. [Sign up for free](https://dashboard.telemetrydeck.com/register?source=websdk) if you don't have one yet.
- You'll need a TelemetryDeck App ID. [Create a new app](https://dashboard.telemetrydeck.com/apps/create) if you don't have one yet.
- Install TelemetryDeck React SDK using NPM or Yarn
<!-- vale proselint.Cliches = YES -->

```shell
npm install -S @typedigital/telemetrydeck-react
```

## Setup

Once you have your App ID, create a TelemetryDeck instance with the factory `createTelemetryDeck` and pass it to the `TelemetryDeckProvider`, which should sit relatively high up in your component tree.

```tsx
import * as React from "react";
import * as ReactDOM from "react-dom";
import {
  TelemetryDeckProvider,
  createTelemetryDeck,
} from "@typedigital/telemetrydeck-react";
import { Dashboard } from "./Dashboard";

const td = createTelemetryDeck({ appID: process.env.APP_ID, clientUser: "anonymous" });

const App = () => {
  return (
    <div>
      <TelemetryDeckProvider telemetryDeck={td}>
        <Dashboard />
      </TelemetryDeckProvider>
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById("root"));
```

## Basic usage

To send signals, use the `useTelemetryDeck` hook and destructure the various methods that can be used to modify the instance or send signals to TelemetryDeck.
For more information, see the [JS documentation](/docs/guides/javascript-setup/).

```tsx
import * as React from "react";
import { useTelemetryDeck } from "@typedigital/telemetrydeck-react";

function Dashboard() {
  const { signal } = useTelemetryDeck();

  const clickHandler = async () => {
    const res = await signal("click", {
      event: "button-click",
      target: "Call to Action",
    });
    console.log(res); // the response of the TelemetryDeck API
  };

  // If you want to track if a user saw a certain page or component just use an effect
  React.useEffect(() => {
    (async () => {
      const { pathname } = window.location;
      await signal("pageview", { component: "dashboard", path: pathname });
    })();
  }, []);

  return (
    <React.Fragment>
      <h1>My Dashboard</h1>
      <button onClick={async () => await clickHandler()}>Click me</button>
    </React.Fragment>
  );
}

export { Dashboard };
```

## React Native & Expo Support

`telemetrydeck-react` also supports React Native or Expo.
If no global implementation is available because you are not on the web, TelemetryDeck needs a subtle implementation which can be either injected by extending `globalThis` or added to the TelemetryDeck instance.

In the React Native context, a TextEncoder is also needed for it to work properly.

If you are developing an Expo project, you should install the following dependencies in addition to this library.

```shell
npm i -S expo-crypto text-encoding
```

### Monkey-Patching crypto and TextEncoder

To patch the functionalities, a file named `globals.js` should be created first. The following code should be added to this file. This code extends the global object for the React Native Context with the TextEncoder and the `crypto.subtle.digest` function, which converts a message to a hash.

```ts
// globals.js

import * as Crypto from "expo-crypto";

globalThis.crypto = {
  subtle: {
    digest: (algorithm, message) => Crypto.digest(algorithm, message),
  },
};
global.TextEncoder = require("text-encoding").TextEncoder;
```

Finally, the created file should be imported into the `index.js` or any other root file for the bundler.

```js
// index.js

import { registerRootComponent } from "expo";
import "./globals.js";
import App from "./App";

registerRootComponent(App);
```

## Sponsors

[<img src="/docs/images/typedigital.png" width=300 class="p-3 bg-white" />](https://typedigital.de)

The development of the TelemetryDeck React SDK was graciously provided by our friends at Augsburg-based web development agency [typedigital](https://typedigital.de). Thanks a lot, and check them out for your web application development needs. 🧡

[Follow the development on GitHub](https://github.com/typedigital/telemetrydeck-react)
