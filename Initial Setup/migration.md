# Firebase Flutter Migration Guide

This guide helps you migrate your Firebase Flutter integration between different versions and handle breaking changes.

## Migrating to FlutterFire

If you're using an older Firebase setup, it's recommended to migrate to FlutterFire for better integration and features.

### From Manual Setup to FlutterFire CLI

1. Install FlutterFire CLI:
```bash
dart pub global activate flutterfire_cli
```

2. Remove old manual initialization code from `main.dart`
3. Run FlutterFire configuration:
```bash
flutterfire configure
```

## Version Migration Guide

### Migrating to Firebase Core 2.x

#### Breaking Changes
- Removed deprecated APIs
- Updated minimum Flutter SDK requirement to 1.20.0
- Updated minimum Dart SDK requirement to 2.18.0

#### Steps
1. Update `pubspec.yaml`:
```yaml
dependencies:
  firebase_core: ^2.15.1
```

2. Update initialization code:
```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  runApp(MyApp());
}
```

### Migrating Authentication (^4.x)

#### Breaking Changes
- New phone authentication API
- Updated OAuth provider handling
- Improved error handling

#### Steps
1. Update dependency:
```yaml
dependencies:
  firebase_auth: ^4.9.0
```

2. Update phone authentication:
```dart
// Old code
await _auth.verifyPhoneNumber(
  phoneNumber: phoneNumber,
  verificationCompleted: (credential) {},
  verificationFailed: (error) {},
  codeSent: (verificationId, [forceResendingToken]) {},
  codeAutoRetrievalTimeout: (verificationId) {},
);

// New code
await _auth.verifyPhoneNumber(
  phoneNumber: phoneNumber,
  verificationCompleted: (PhoneAuthCredential credential) {},
  verificationFailed: (FirebaseAuthException error) {},
  codeSent: (String verificationId, int? resendToken) {},
  codeAutoRetrievalTimeout: (String verificationId) {},
);
```

### Migrating Cloud Firestore (^4.x)

#### Breaking Changes
- Updated transaction API
- New query constraints syntax
- Improved offline persistence

#### Steps
1. Update dependency:
```yaml
dependencies:
  cloud_firestore: ^4.9.1
```

2. Update query syntax:
```dart
// Old code
query.where('age', isGreaterThan: 18)
     .where('status', isEqualTo: 'active')
     .limit(10);

// New code
query.where(Filter.and(
  Filter('age', isGreaterThan: 18),
  Filter('status', isEqualTo: 'active')
)).limit(10);
```

### Migrating Storage (^11.x)

#### Breaking Changes
- New upload/download progress tracking
- Updated error handling
- Improved file metadata support

#### Steps
1. Update dependency:
```yaml
dependencies:
  firebase_storage: ^11.2.6
```

2. Update upload with progress:
```dart
// Old code
final task = storageRef.putFile(file);
task.events.listen((event) {
  // Handle progress
});

// New code
final task = storageRef.putFile(file);
task.snapshotEvents.listen((TaskSnapshot snapshot) {
  final progress = snapshot.bytesTransferred / snapshot.totalBytes;
  // Handle progress
});
```

## Platform-Specific Migration

### Android Migration

1. Update Google Services plugin in `android/build.gradle`:
```gradle
buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.3.15'
    }
}
```

2. Update minimum SDK version in `android/app/build.gradle`:
```gradle
android {
    defaultConfig {
        minSdkVersion 19
        multiDexEnabled true
    }
}
```

### iOS Migration

1. Update minimum deployment target in Xcode to iOS 11.0
2. Update Pod dependencies:
```bash
cd ios
pod update
```

## Troubleshooting Common Migration Issues

### 1. Dependency Conflicts
- Clear pub cache: `flutter pub cache clean`
- Update all Firebase dependencies to compatible versions
- Run `flutter pub get`

### 2. Build Errors
- Clean build: `flutter clean`
- Update Gradle version if needed
- Check platform-specific configuration files

### 3. Runtime Errors
- Verify initialization order
- Check for deprecated API usage
- Review error handling implementation

## Best Practices for Migration

1. **Backup Your Project**
   - Create a source control branch
   - Backup configuration files

2. **Incremental Migration**
   - Update one Firebase service at a time
   - Test thoroughly after each update

3. **Testing**
   - Test offline functionality
   - Verify all Firebase features
   - Check platform-specific behavior

## Post-Migration Verification

1. Verify Firebase Console connection
2. Test all Firebase services
3. Check error handling
4. Monitor app performance
5. Review security rules

For detailed migration information, refer to the [official FlutterFire documentation](https://firebase.flutter.dev/docs/migration-guide).