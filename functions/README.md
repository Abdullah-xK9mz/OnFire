# Functions setup

Use Firebase Admin SDK or a one-time secure bootstrap script to assign the first admin custom claim. Do not create an admin role from the browser.

Example server-side only:
`await getAuth().setCustomUserClaims(uid, {role: "admin"});`

After changing a claim, the user must refresh their ID token/sign in again.

Secrets: `PAYMOB_API_KEY`, `PAYMOB_HMAC_SECRET`, `AI_API_KEY`.
