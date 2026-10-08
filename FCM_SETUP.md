# Notifications / FCM

The platform stores in-app notifications in Firestore. For push notifications, enable Cloud Messaging, create a Web Push certificate/key, register the service worker, and store each user's FCM token under `users/{uid}/devices/{tokenId}`. Send notifications only from Cloud Functions using the Firebase Admin SDK. Never put an FCM server credential in HTML.
