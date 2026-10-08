# Security test matrix

Before production, test these with Firebase Emulator Suite and real Auth tokens:

- Student cannot read another student document.
- Student cannot update role/grade/subscription/score/passed.
- Teacher cannot write admin-only collections.
- Teacher cannot access attempts outside authorized subjects.
- Student cannot read exam correct answers if your production exam schema stores them in a separate private document.
- Student cannot read Storage videos directly.
- Student cannot access a future lesson without prerequisite attempt passed.
- Payment status cannot be changed from browser.
- Subscription cannot be extended from browser.
- Certificate cannot be issued by client.
- Attempt cannot be created/updated directly by client.
- Retake requests can only be created through callable backend.
- App Check enforcement enabled after monitoring.
