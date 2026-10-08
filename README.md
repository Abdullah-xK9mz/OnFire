# ON FIRE — Multi-page Firebase Educational Platform

مشروع منصة تعليمية حقيقية متعددة الصفحات لطلاب 1/2/3 إعدادي و1/2/3 ثانوي. كل صفحة HTML تحتوي CSS وJavaScript الخاصين بها داخل الملف، ولا يوجد SPA ولا React/Vue/Angular.

## التقنيات
- HTML5 / CSS3 / JavaScript Modules
- Firebase Authentication (Email/Password + Google)
- Cloud Firestore
- Cloud Storage
- Cloud Functions 2nd gen (Node.js 22)
- Firebase Security Rules
- App Check (موصى به في الإنتاج)

## مهم قبل التشغيل
1. أنشئ مشروع Firebase وسجّل Web App.
2. انسخ قيم Firebase Web Config إلى `firebaseConfig` داخل ملفات HTML (أو استخدم Firebase Hosting + عملية استبدال آمنة في CI). هذه القيم ليست Service Account secrets.
3. فعّل Email/Password وGoogle من Authentication. توثيق Firebase يؤكد دعم الطريقتين.
4. أنشئ Firestore وStorage في Production/Locked mode ثم انشر `firestore.rules` و`storage.rules`.
5. فعّل App Check باستخدام reCAPTCHA Enterprise في الإنتاج.
6. ادخل إلى مجلد `functions` وثبّت الاعتمادات ثم انشر Functions.
7. ضع أسرار الدفع وAI في Secret Manager/Cloud Functions secrets، وليس HTML.
8. إعداد Paymob/Card Gateway يحتاج بيانات حسابك التجارية وIntegration IDs/Webhook حسب مزود الدفع. المشروع يحتوي Adapter قابل للتوصيل بدل وضع أسرار حقيقية.

## تشغيل محلي
يمكن فتح HTML عبر Firebase Hosting أو خادم static بسيط. بعض خصائص Firebase Auth/Google وModules لن تعمل بشكل صحيح من `file://`.

## نشر
```bash
firebase login
firebase use YOUR_PROJECT_ID
firebase deploy --only hosting,firestore:rules,storage,functions
```

## بنية البيانات
- `users`: الملف الأساسي + role (`student`, `teacher`, `admin`)
- `grades`, `subjects`, `units`, `lessons`, `videos`
- `exams`, `examAttempts`, `examResults`
- `retakeRequests`, `progress`
- `plans`, `subscriptions`, `payments`, `coupons`, `paymentReviews`
- `certificates`, `notifications`, `settings`
- `teacherSubjects` للربط بين المدرسين والمواد

## الصلاحيات
الـrole الحقيقي لا يُؤخذ من localStorage. العمليات الحساسة تمر عبر Callable Cloud Functions مع Firebase Auth/App Check عند تفعيله. Firestore/Storage Rules تمنع الكتابة المباشرة للحقول الحساسة.

## الفيديو
لا يتم وضع مسار فيديو سري ثابت في HTML. `getVideoAccess/getLessonAccess` يتحقق من هوية الطالب والاشتراك والصف والتسلسل ثم يعيد URL مؤقتًا. تخزين الفيديو في Storage مقفول للقراءة المباشرة. هذا يقلل التسريب والمشاركة، لكنه لا يمنع تسجيل الشاشة أو النسخ بنسبة 100%.

## الدفع
- التحويل اليدوي: يرفع الطالب الإيصال، ثم `submitManualPayment` ينشئ حالة PENDING/AI_REVIEW. يمكن تشغيل AI verification قبل قرار الأدمن، لكن الثقة المنخفضة تتحول إلى MANUAL_REVIEW.
- AI verification: Function اختيارية تتصل بموديل AI عبر Secret، وتعيد confidence. الثقة المنخفضة تتحول إلى MANUAL_REVIEW.
- البطاقات: استخدم Checkout/Hosted Payment Page من مزود مثل Paymob. لا يتم تخزين رقم البطاقة أو CVV في Firestore. تأكيد الدفع النهائي يجب أن يأتي من Webhook موثوق وموقّع.

## ملاحظة عن AI Receipt Verification
النسخة المرفقة تحتوي طبقة Adapter واضحة ولا تحتوي مفتاح Gemini/AI حقيقي. يجب ضبط المزود والموديل وسياسة الثقة قبل الإنتاج. لا تجعل AI وحده مصدر الحقيقة في قرار مالي.

## Logo
ضع شعارك الجاهز باسم `assets/logo.png`. لم يتم إنشاء Logo جديد.

## GitHub Pages
يمكن استضافة الملفات الثابتة، لكن GitHub Pages وحده لا يشغّل Cloud Functions أو Webhooks. الأفضل Firebase Hosting للواجهة مع نفس ملفات HTML، أو GitHub Pages للواجهة مع Backend Firebase مستقل.

## مراجع رسمية حديثة
Firebase JS SDK الحالي وقت إعداد المشروع هو 13.0.0، وCloud Functions تدعم Node.js 20 و22؛ استخدم Node 22 هنا. راجع وثائق Firebase الرسمية قبل النشر.

## سياسة التواريخ
الاشتراك الشهري/السنوي يحسب من تاريخ التفعيل إلى التاريخ المقابل في الشهر/السنة التالية، ثم ينتهي في اليوم السابق لذلك التاريخ. عند عدم وجود نفس رقم اليوم في الشهر التالي (مثل 31 يناير)، يتم تثبيت يوم الاستحقاق على آخر يوم متاح في الشهر التالي قبل طرح يوم واحد.
