# expo-camera: barcode scanning broken in SDK 58 Android release builds (R8)

Minimal reproduction: `CameraView` never calls `onBarcodeScanned` in an Android **release** build on SDK 58. It works on iOS and in Android debug builds.

SDK 58's template turns on R8 for Android release builds (`android.enableMinifyInReleaseBuilds=true`; it was `false` in SDK 57). Turning it off makes scanning work again.

- expo 58.0.19, expo-camera 58.0.12, react-native 0.88.0
- No `android/` directory: the native project is generated at build time (CNG)

## Reproduce

1. `npm install`
2. `npx eas-cli build -p android --profile preview` (internal release APK)
3. Install the APK and point the camera at any QR code.
   **Result:** the text never changes from "Point the camera at a QR code…".

## Workaround (shows it's R8)

`expo-build-properties` is already installed. Add it to `plugins` in `app.json`:

```json
["expo-build-properties", { "android": { "enableMinifyInReleaseBuilds": false } }]
```

Rebuild with the same command. The scanned value now appears under the camera.
