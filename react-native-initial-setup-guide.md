# React Native App Initial Setup

This document provides step-by-step instructions to set up a new React Native project. This guide uses Expo as a React Native framework which is recommended by React Native team.

Expo is a production-grade React Native Framework which provides features like file-based routing, high-quality universal libraries, and the ability to write plugins that modify native code without having to manage native files.

## Prerequisites

Make sure you have the following installed on your machine:

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/en)
- [npm](https://www.npmjs.com/) (Node Package Manager)

## Start a new React Native project

1. To create a new Expo project, run the following in your terminal:

   ```bash
     npx create-expo-app@latest
   ```

2. Set up a local development environment for running your project on Android or iOS using `Expo Go`. Download the `Expo Go` app from the [App Store](https://apps.apple.com/us/app/expo-go/id982107779) or [Google Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent&referrer=www).

3. Start a development server:

   ```bash
     npm run start
   ```

   In the output, you'll find options to open the app in a

   - [development build](https://docs.expo.dev/develop/development-builds/introduction/)
   - [Android emulator](https://docs.expo.dev/workflow/android-studio-emulator/)
   - [iOS simulator](https://docs.expo.dev/workflow/ios-simulator/)
   - [Expo Go](https://expo.dev/go), a limited sandbox for trying out app development with Expo

4. Open the project in the Expo app:

   After running the command above, you will see a QR code in your terminal. Scan this QR code to open the app on your device. <br>
   (Note: Make sure that your mobile device is connected to the same network as your computer)
   <br><br>
   You can start developing by editing the files inside the **app** directory. This project uses [file-based routing](https://docs.expo.dev/router/introduction).
