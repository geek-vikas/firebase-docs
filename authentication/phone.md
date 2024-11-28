# Phone Authentication in Flutter using Firebase

This guide explains how to implement phone number authentication in your Flutter application using Firebase Authentication.

## Prerequisites

1. Firebase project set up in your Flutter app
2. `firebase_auth` package installed in `pubspec.yaml`:

```yaml
dependencies:
  firebase_auth: ^4.9.0
```

## Implementation Guide

### 1. Basic Phone Authentication Flow

```dart
import 'package:firebase_auth/firebase_auth.dart';

class PhoneAuthentication {
  final FirebaseAuth _auth = FirebaseAuth.instance;
  String? _verificationId;

  // Step 1: Start phone number verification
  Future<void> verifyPhoneNumber(String phoneNumber) async {
    try {
      await _auth.verifyPhoneNumber(
        phoneNumber: phoneNumber,
        verificationCompleted: _onVerificationCompleted,
        verificationFailed: _onVerificationFailed,
        codeSent: _onCodeSent,
        codeAutoRetrievalTimeout: _onCodeTimeout,
        timeout: const Duration(seconds: 60),
      );
    } catch (e) {
      throw Exception('Failed to verify phone number: $e');
    }
  }

  // Step 2: Handle automatic verification (Android only)
  void _onVerificationCompleted(PhoneAuthCredential credential) async {
    try {
      await _auth.signInWithCredential(credential);
      // User is signed in automatically
    } catch (e) {
      throw Exception('Error on auto verification: $e');
    }
  }

  // Step 3: Handle verification failure
  void _onVerificationFailed(FirebaseAuthException error) {
    if (error.code == 'invalid-phone-number') {
      throw Exception('Invalid phone number');
    } else {
      throw Exception('Verification failed: ${error.message}');
    }
  }

  // Step 4: Handle SMS code sent
  void _onCodeSent(String verificationId, int? resendToken) {
    _verificationId = verificationId;
    // Store verificationId for later use
  }

  // Step 5: Handle code timeout
  void _onCodeTimeout(String verificationId) {
    _verificationId = verificationId;
    // Handle timeout
  }

  // Step 6: Verify SMS code
  Future<UserCredential> verifySmsCode(String smsCode) async {
    if (_verificationId == null) throw Exception('Verification ID is null');

    PhoneAuthCredential credential = PhoneAuthProvider.credential(
      verificationId: _verificationId!,
      smsCode: smsCode,
    );

    return await _auth.signInWithCredential(credential);
  }
}
```

## Best Practices

1. **Phone Number Formatting**

   - Always use E.164 format (e.g., +1234567890)
   - Consider using a phone number input formatter package

2. **Error Handling**

   - Handle all possible error scenarios
   - Provide clear error messages to users
   - Implement proper retry mechanisms

3. **Security**

   - Implement rate limiting
   - Add reCAPTCHA verification if required
   - Monitor authentication attempts in Firebase Console

4. **Testing**
   - Test with different phone number formats
   - Test timeout scenarios
   - Test invalid code scenarios
   - Test auto-verification on Android

## Common Issues and Solutions

### 1. Invalid Phone Number Format

```dart
// Correct format
'+1234567890'

// Incorrect formats
'1234567890'
'(123) 456-7890'
```

### 2. Rate Limiting

Firebase has quotas for phone authentication. Implement proper error handling:

```dart
try {
  await _auth.verifyPhoneNumber(...);
} on FirebaseAuthException catch (e) {
  if (e.code == 'too-many-requests') {
    // Handle rate limiting
    showMessage('Too many requests. Try again later.');
  }
}
```

### 3. Testing Mode

For development, use test phone numbers:

```dart
// Test phone numbers
'+15555555555'  // Always succeeds
'+15555555551'  // Always fails
```

## Platform-Specific Considerations

### Android

- Automatic SMS code detection available
- Requires Google Play Services
- SHA-1 fingerprint in Firebase Console

### iOS

- Manual code entry required
- Push notifications should be enabled
- APNs configuration in Firebase Console

## Security Recommendations

1. **Server-Side Validation**

   - Implement additional validation on your backend
   - Verify phone numbers against a trusted source

2. **Rate Limiting**

   - Implement client-side cooldown
   - Monitor authentication patterns

3. **User Education**
   - Inform users about SMS delivery time
   - Provide clear instructions for code entry

## Troubleshooting

1. **SMS Not Received**

   - Check phone number format
   - Verify Firebase configuration
   - Check quota limits

2. **Invalid Code Errors**
   - Verify code length
   - Check for code expiration
   - Ensure proper verification ID handling

For more information, visit the [official Firebase Phone Auth documentation](https://firebase.google.com/docs/auth/flutter/phone-auth).
