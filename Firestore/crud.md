# Cloud Firestore CRUD Operations in Flutter

This guide demonstrates how to perform Create, Read, Update, and Delete (CRUD) operations using Cloud Firestore in Flutter applications.

## Prerequisites

1. Firebase project set up in your Flutter app
2. Cloud Firestore dependency in `pubspec.yaml`:

```yaml
dependencies:
  cloud_firestore: ^4.9.1
```

## Basic Setup

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

// Get Firestore instance
final FirebaseFirestore firestore = FirebaseFirestore.instance;
```

## Create Operations

### 1. Add Document with Auto-Generated ID

```dart
Future<void> addUser(User user) async {
  try {
    await firestore.collection('users').add(user.toMap());
  } catch (e) {
    throw Exception('Failed to add user: $e');
  }
}
```

### 2. Add Document with Custom ID

```dart
Future<void> addUserWithId(User user) async {
  try {
    await firestore
        .collection('users')
        .doc(user.id)
        .set(user.toMap());
  } catch (e) {
    throw Exception('Failed to add user: $e');
  }
}
```

### 3. Add Document with Merge Option

```dart
Future<void> addUserWithMerge(User user) async {
  try {
    await firestore
        .collection('users')
        .doc(user.id)
        .set(user.toMap(), SetOptions(merge: true));
  } catch (e) {
    throw Exception('Failed to add user: $e');
  }
}
```

## Read Operations

### 1. Get Single Document

```dart
Future<User?> getUser(String userId) async {
  try {
    DocumentSnapshot doc = await firestore
        .collection('users')
        .doc(userId)
        .get();

    if (doc.exists) {
      return User.fromMap(doc.data() as Map<String, dynamic>);
    }
    return null;
  } catch (e) {
    throw Exception('Failed to get user: $e');
  }
}
```

### 2. Get Multiple Documents

```dart
Future<List<User>> getAllUsers() async {
  try {
    QuerySnapshot querySnapshot = await firestore
        .collection('users')
        .get();

    return querySnapshot.docs
        .map((doc) => User.fromMap(doc.data() as Map<String, dynamic>))
        .toList();
  } catch (e) {
    throw Exception('Failed to get users: $e');
  }
}
```

### 3. Real-Time Updates

```dart
Stream<List<User>> getUsersStream() {
  return firestore
      .collection('users')
      .snapshots()
      .map((snapshot) => snapshot.docs
          .map((doc) => User.fromMap(doc.data() as Map<String, dynamic>))
          .toList());
}
```

### 4. Querying Data

```dart
Future<List<User>> queryUsers({
  required int minAge,
  String? nameStartsWith,
}) async {
  try {
    Query query = firestore.collection('users').where('age', isGreaterThan: minAge);

    if (nameStartsWith != null) {
      query = query.where('name', isGreaterThanOrEqualTo: nameStartsWith)
          .where('name', isLessThan: nameStartsWith + 'z');
    }

    QuerySnapshot querySnapshot = await query.get();

    return querySnapshot.docs
        .map((doc) => User.fromMap(doc.data() as Map<String, dynamic>))
        .toList();
  } catch (e) {
    throw Exception('Failed to query users: $e');
  }
}
```

## Update Operations

### 1. Update Entire Document

```dart
Future<void> updateUser(User user) async {
  try {
    await firestore
        .collection('users')
        .doc(user.id)
        .update(user.toMap());
  } catch (e) {
    throw Exception('Failed to update user: $e');
  }
}
```

### 2. Update Specific Fields

```dart
Future<void> updateUserAge(String userId, int newAge) async {
  try {
    await firestore
        .collection('users')
        .doc(userId)
        .update({'age': newAge});
  } catch (e) {
    throw Exception('Failed to update user age: $e');
  }
}
```

### 3. Atomic Operations

```dart
Future<void> incrementUserAge(String userId) async {
  try {
    await firestore
        .collection('users')
        .doc(userId)
        .update({
          'age': FieldValue.increment(1),
        });
  } catch (e) {
    throw Exception('Failed to increment user age: $e');
  }
}
```

## Delete Operations

### 1. Delete Document

```dart
Future<void> deleteUser(String userId) async {
  try {
    await firestore
        .collection('users')
        .doc(userId)
        .delete();
  } catch (e) {
    throw Exception('Failed to delete user: $e');
  }
}
```

### 2. Delete Specific Fields

```dart
Future<void> deleteUserEmail(String userId) async {
  try {
    await firestore
        .collection('users')
        .doc(userId)
        .update({
          'email': FieldValue.delete(),
        });
  } catch (e) {
    throw Exception('Failed to delete user email: $e');
  }
}
```

## Batch Operations

```dart
Future<void> batchUpdateUsers(List<User> users) async {
  try {
    WriteBatch batch = firestore.batch();

    for (var user in users) {
      DocumentReference docRef = firestore.collection('users').doc(user.id);
      batch.set(docRef, user.toMap());
    }

    await batch.commit();
  } catch (e) {
    throw Exception('Failed to batch update users: $e');
  }
}
```

## Transactions

```dart
Future<void> transferPoints(String fromUserId, String toUserId, int points) async {
  try {
    await firestore.runTransaction((transaction) async {
      // Get both user documents
      DocumentSnapshot fromUserDoc = await transaction.get(
        firestore.collection('users').doc(fromUserId)
      );
      DocumentSnapshot toUserDoc = await transaction.get(
        firestore.collection('users').doc(toUserId)
      );

      // Check if documents exist and have enough points
      if (!fromUserDoc.exists || !toUserDoc.exists) {
        throw Exception('One or both users do not exist');
      }

      int fromPoints = (fromUserDoc.data() as Map<String, dynamic>)['points'] ?? 0;
      if (fromPoints < points) {
        throw Exception('Not enough points');
      }

      // Update both documents
      transaction.update(
        firestore.collection('users').doc(fromUserId),
        {'points': fromPoints - points}
      );

      int toPoints = (toUserDoc.data() as Map<String, dynamic>)['points'] ?? 0;
      transaction.update(
        firestore.collection('users').doc(toUserId),
        {'points': toPoints + points}
      );
    });
  } catch (e) {
    throw Exception('Failed to transfer points: $e');
  }
}
```

## Best Practices

1. **Error Handling**

   - Always wrap Firestore operations in try-catch blocks
   - Provide meaningful error messages
   - Handle network connectivity issues

2. **Data Validation**

   - Validate data before writing to Firestore
   - Use appropriate data types
   - Handle null values appropriately

3. **Security Rules**

   - Implement proper security rules in Firebase Console
   - Validate user permissions
   - Protect sensitive data

4. **Offline Persistence**

   - Enable offline persistence when needed:

   ```dart
   await FirebaseFirestore.instance.settings = Settings(
     persistenceEnabled: true,
     cacheSizeBytes: Settings.CACHE_SIZE_UNLIMITED,
   );
   ```

5. **Query Optimization**
   - Use appropriate indexes
   - Limit query results
   - Use compound queries efficiently

## Common Issues and Solutions

### 1. Document Not Found

```dart
// Always check if document exists
DocumentSnapshot doc = await firestore.collection('users').doc(userId).get();
if (!doc.exists) {
  throw Exception('User not found');
}
```

### 2. Permission Denied

```dart
// Handle permission errors
try {
  await operation();
} on FirebaseException catch (e) {
  if (e.code == 'permission-denied') {
    // Handle permission error
  }
}
```

### 3. Network Issues

```dart
// Check network connectivity
if (await checkConnectivity()) {
  // Perform operation
} else {
  // Handle offline scenario
}
```

For more information, visit the [official Cloud Firestore documentation](https://firebase.google.com/docs/firestore/quickstart).
