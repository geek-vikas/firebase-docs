# Firebase Integration Documentation

This document provides a comprehensive guide on how Firebase is integrated and used in the Chant application.

## Firebase Services Used

The application uses several Firebase services:

1. Firebase Authentication
2. Cloud Firestore
3. Firebase Storage
4. Firebase Analytics
5. Firebase Dynamic Links
6. Firebase Remote Config
7. Firebase Performance
8. Firebase Crashlytics
9. Firebase App Check

## Setup and Dependencies

The following Firebase dependencies are used in the project (as specified in pubspec.yaml):

```yaml
dependencies:
  firebase_core: ^3.7.0
  firebase_analytics: ^11.3.4
  cloud_firestore: ^5.4.5
  firebase_storage: ^12.3.5
  firebase_dynamic_links: ^6.0.9
  firebase_remote_config: ^5.1.4
  firebase_performance: ^0.10.0+9
  firebase_crashlytics: ^4.1.4
  firebase_app_check: ^0.3.1+5
```

## Authentication

### Email Authentication

The application uses Firebase Email Authentication. Key operations:

1. Sign In:

```dart
await FirebaseAuth.instance.signInWithEmailAndPassword(
  email: email,
  password: password
);
```

2. Sign Out:

```dart
await FirebaseAuth.instance.signOut();
```

3. Get Current User:

```dart
final user = FirebaseAuth.instance.currentUser;
```

## Cloud Firestore

### Data Structure

The application uses Cloud Firestore for storing various types of data including:

- User profiles
- Chat messages
- Tickets
- Polls
- Giveaways

### Basic Operations

1. Reading Data:

```dart
// Get a single document
final docSnapshot = await FirebaseFirestore.instance
    .collection('collection_name')
    .doc('document_id')
    .get();

// Query multiple documents
final querySnapshot = await FirebaseFirestore.instance
    .collection('collection_name')
    .where('field', isEqualTo: value)
    .get();
```

2. Writing Data:

```dart
await FirebaseFirestore.instance
    .collection('collection_name')
    .doc('document_id')
    .set(data);
```

3. Real-time Updates:

```dart
FirebaseFirestore.instance
    .collection('collection_name')
    .snapshots()
    .listen((snapshot) {
      // Handle real-time updates
    });
```

## Firebase Storage

The application uses Firebase Storage for storing media files. Key operations are handled through the `FirebaseStorageService` class.

### Basic Operations

1. Upload File:

```dart
final Reference ref = FirebaseStorage.instance.ref().child(path);
await ref.putFile(file);
final downloadUrl = await ref.getDownloadURL();
```

2. Download File:

```dart
final data = await FirebaseStorage.instance.ref().child(path).getData();
```

3. Delete File:

```dart
await FirebaseStorage.instance.ref().child(path).delete();
```

## Best Practices

1. Error Handling

   - Always wrap Firebase operations in try-catch blocks
   - Handle network connectivity issues
   - Implement proper error messages for users

2. Security

   - Never store sensitive information in plain text
   - Use Firebase Security Rules for Firestore and Storage
   - Implement proper user authentication checks

3. Performance
   - Use pagination when fetching large datasets
   - Implement caching where appropriate
   - Optimize queries using proper indexes

## Common Issues and Solutions

1. Authentication State

   - Always check authentication state before performing operations
   - Handle token expiration properly
   - Implement proper error handling for auth failures

2. Data Consistency
   - Use transactions for critical operations
   - Implement proper validation before writing data
   - Handle offline/online synchronization

## Testing

When testing Firebase functionality:

1. Use Firebase Emulator Suite for local development
2. Create separate Firebase projects for development and production
3. Use test accounts for authentication testing

## Security Rules

Ensure proper security rules are set up in Firebase Console for:

1. Firestore Database
2. Storage
3. Authentication

Remember to review and update security rules regularly based on application requirements.
