# Setting Up Firebase in Flutter

This guide will walk you through the process of integrating Firebase into your Flutter application.

## Prerequisites

Before you begin, make sure you have:

1. [Flutter SDK](https://flutter.dev/docs/get-started/install) installed on your machine
2. A Flutter project created
3. [Firebase CLI](https://firebase.google.com/docs/cli) installed
4. A Google account

## Step 1: Create a Firebase Project

1. Go to the [Firebase Console](https://console.firebase.google.com/)
2. Click on "Add Project"
3. Enter your project name
4. Choose whether to enable Google Analytics
5. Follow the prompts to complete project creation

## Step 2: Install FlutterFire CLI

The FlutterFire CLI tool helps you configure Firebase in your Flutter apps.

```bash
dart pub global activate flutterfire_cli
```

## Step 3: Configure Flutter Project

1. Add Firebase SDK dependencies to your `pubspec.yaml`:

```yaml
dependencies:
  firebase_core: ^2.15.1
  # Add other Firebase packages as needed:
  # firebase_auth: ^4.9.0
  # cloud_firestore: ^4.9.1
  # firebase_storage: ^11.2.6
```

2. Run flutter pub get:

```bash
flutter pub get
```

## Step 4: Initialize Firebase

1. Login to Firebase:

```bash
firebase login
```

2. Configure your Flutter app with Firebase:

```bash
flutterfire configure
```

This command will:
- Create Firebase apps for each platform
- Download platform-specific configuration files
- Add the configuration to your Flutter project

## Step 5: Initialize Firebase in Your App

Update your `lib/main.dart`:

```dart
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  runApp(MyApp());
}
```

## Platform-Specific Setup

### Android Setup

1. Ensure your `android/build.gradle` has the Google services plugin:

```gradle
buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.3.15'
    }
}
```

2. Apply the plugin in your `android/app/build.gradle`:

```gradle
apply plugin: 'com.google.gms.google-services'
```

### iOS Setup

1. Set the minimum iOS deployment target in Xcode to 11.0 or higher
2. Add the Firebase initialization code to your `ios/Runner/AppDelegate.swift`:

```swift
import Firebase

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
  override func application(_ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    FirebaseApp.configure()
    return true
  }
}
```

## Verifying Setup

To verify your Firebase setup:

1. Add a simple Firebase operation (like Firebase Analytics)
2. Run your app
3. Check the Firebase Console to see if your app is connected

## Common Issues and Troubleshooting

1. **Compilation Errors**: Make sure all dependencies are properly added and versions are compatible
2. **Platform Issues**: Ensure minimum SDK versions match Firebase requirements
3. **Configuration Issues**: Verify that `google-services.json` (Android) and `GoogleService-Info.plist` (iOS) are in the correct locations

## Next Steps

After successful setup, you can start using Firebase features:

- Authentication
- Cloud Firestore
- Realtime Database
- Cloud Storage
- Cloud Functions
- Analytics

For more detailed information, visit the [official FlutterFire documentation](https://firebase.flutter.dev/docs/overview/).