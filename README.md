# Yelpi React Native

A React Native mobile app with account registration, sign-in, and a guided patient intake flow. The repository contains native Android and iOS projects; its internal application name is `helpandroid`. The interface includes English and Portuguese text.

![App preview](screenshots/cover.png)

## Features

- Register an account, sign in, and sign out.
- Persist authentication tokens with AsyncStorage and restore a session at startup.
- Collect patient details through a step-by-step form, including name, birth date, weight, gender, mental state, relationship, general health, and physicians.
- Review patient information and submit it to the backend API.

The app is a work in progress: the home screen is minimal, and parts of the patient review form contain placeholder or unfinished editing behavior.

## Technology

- React 16.11.0 and React Native 0.62.2
- React Navigation 4 with stack and switch navigators
- Redux, React Redux, and Redux Thunk
- Axios and JWT decoding
- React Native Elements and React Native Vector Icons
- Jest and ESLint

## Getting started

### Prerequisites

- Node.js and npm, or Yarn.
- For Android: a compatible JDK, Android SDK, and an emulator or connected device.
- For iOS: macOS, Xcode, CocoaPods, and a simulator or connected device.
- A running backend compatible with the API described below.

This repository uses a legacy native toolchain. Android is configured for SDK 28, Build Tools 28.0.3, Android Gradle Plugin 3.5.2, and Gradle 6.0.1. The iOS Podfile declares iOS 9.0 and includes Flipper 0.33.x. Compatibility with current development tools has not been verified; the repository does not pin Node.js or JDK versions.

### Install dependencies

From the repository root:

```sh
npm install
```

`src/utils/api.js` imports `moment`, but `package.json` does not declare it directly. Add it if it is not already available through the dependency tree:

```sh
npm install moment
```

Both `package-lock.json` and `yarn.lock` are present. The commands here use npm; use one package manager consistently when updating dependencies.

For iOS, also install native dependencies:

```sh
cd ios
pod install
cd ..
```

### Configure the API

Edit `baseUrl` in [`src/utils/api.js`](src/utils/api.js) to point to your backend. Its checked-in value is `https://mighty-ocean-68056.herokuapp.com`; availability of that deployment has not been verified. There is no environment-variable configuration in the current API helper.

The client calls these endpoints:

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `POST` | `/v1/users` | Register a user; sends `{user: signupData}`. |
| `POST` | `/v1/sessions` | Sign in; sends the login form data. |
| `GET` | `/v1/users/:id` | Retrieve the authenticated user's data. |
| `POST` | `/v1/patients` | Create a patient; sends `{patient: patientData}`. |

Authentication responses are expected to include `token` and `user`. The client stores the token under the AsyncStorage key `token`, reads its `id` and `exp` JWT claims, and attaches it to authorized requests as `Authorization: Bearer <token>`.

When using a local backend, choose an address reachable from the emulator or physical device.

### Run the app

Start Metro:

```sh
npm start
```

In a separate terminal, launch Android:

```sh
npm run android
```

Or launch iOS on macOS:

```sh
npm run ios
```

## Development commands

| Command | Description |
| --- | --- |
| `npm start` | Start the Metro development server. |
| `npm run android` | Build and launch the Android app. |
| `npm run ios` | Build and launch the iOS app. |
| `npm test` | Run Jest; the repository includes an app render test. |
| `npm run lint` | Run ESLint. |

## Project structure

```text
App.js                 Navigation configuration and Redux provider
src/
  components/          Shared UI, authentication forms, and patient inputs
  screens/             Authentication, session resolution, home, and patient screens
  store/
    actions/           Redux action creators and action types
    reducers/          User, patient, form field, and error state
    thunks/            Authentication and patient API operations
  utils/               API helpers, navigation reference, and formatting helpers
android/               Native Android project
ios/                   Native iOS project
__tests__/             Jest tests
screenshots/           App preview image
```

The backend source is not included in this repository. Authentication and patient submission require a compatible backend, and patient submission errors are currently logged rather than displayed in the UI.
