# React Native App Initial Setup

This document provides step-by-step instructions to set up a new React Native project. We will
set up a new React Native project, set up a styling theme. This will include:

- [Expo](https://expo.dev/) - A production-grade React Native Framework which provides features like file-based routing, high-quality universal libraries, and the ability to write plugins that modify native code without having to manage native files.
- [NativeWind](https://www.nativewind.dev/) - A styling library that allows you to use `Tailwind CSS` to style your components in React Native

## Prerequisites

Make sure you have the following installed on your machine:

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/en)
- [npm](https://www.npmjs.com/) (Node Package Manager)

## 1. Setting up empty React Native project with Expo

### 1.1 To create a new Expo project, run the following in your terminal:

```bash
  npx create-expo-app@latest
```

### 1.2 Set up a local development environment

To run your project on Android or iOS using `Expo Go`, Download the `Expo Go` app from the [App Store](https://apps.apple.com/us/app/expo-go/id982107779) or [Google Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent&referrer=www).

### 1.3 Start a development server:

```bash
  npm run start
```

In the output, you'll find options to open the app in a

- [development build](https://docs.expo.dev/develop/development-builds/introduction/)
- [Android emulator](https://docs.expo.dev/workflow/android-studio-emulator/)
- [iOS simulator](https://docs.expo.dev/workflow/ios-simulator/)
- [Expo Go](https://expo.dev/go), a limited sandbox for trying out app development with Expo

### 1.4 Open the project in the Expo app:

After running the command above, you will see a QR code in your terminal. Scan this QR code to open the app on your device. <br>
(Note: Make sure that your mobile device is connected to the same network as your computer)
<br><br>
You can start developing by editing the files inside the **app** directory. This project uses [file-based routing](https://docs.expo.dev/router/introduction).

## 2. Setting up styling with NativeWind

### 2.1 Install NativeWind

Install `nativewind` and it's peer dependencies `tailwindcss`, `react-native-reanimated` and `react-native-safe-area-context`

```bash
npx expo install nativewind tailwindcss react-native-reanimated react-native-safe-area-context
```

### 2.2 Setup Tailwind CSS

Run the following command to create a `tailwind.config.js` file in the root of your project:

```bash
npx tailwindcss init
```

Add the paths to all of your component files in your `tailwind.config.js`.

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
	// NOTE: Update this to include the paths to all of your component files.
	content: ["./app/**/*.{js,jsx,ts,tsx}"],
	presets: [require("nativewind/preset")],
	theme: {
		extend: {},
	},
	plugins: [],
};
```

Create a `globals.css` file in the `app` directory and add the Tailwind directives

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 2.3 Add the Babel preset

Create a `babel.config.js` file in the root of your project and add the following:

```js
module.exports = function (api) {
	api.cache(true);
	return {
		presets: [
			["babel-preset-expo", { jsxImportSource: "nativewind" }],
			"nativewind/babel",
		],
	};
};
```

### 2.4. Modify your `metro.config.js`

If your project does not have a `metro.config.js`, run the following command:

```bash
npx expo customize metro.config.js
```

Add the following to the `metro.config.js` file:

```js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativeWind } = require("nativewind/metro");

const config = getDefaultConfig(__dirname);

module.exports = withNativeWind(config, { input: "./app/global.css" });
```

### 2.5 Import your CSS file

Import your `globals.css` file in the `_layout.tsx` file:

```tsx
import "./globals.css";
```

### 2.6 TypeScript

If you are using TypeScript, create a new `nativewind-env.d.ts` file in the root of your project and add a [triple slash directive](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html) referencing the types:

```ts
/// <reference types="nativewind/types" />
```
