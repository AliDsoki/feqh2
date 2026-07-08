# فقيه الصغير — تطبيق أندرويد (APK)

مشروع Android بسيط بيلف تطبيق الـ HTML بتاعك (موسوعة الفقه الميسّر) جوه WebView، ويتبني تلقائي على GitHub Actions ويطلعلك ملف APK جاهز للتنصيب.

## الخطوات

### 1) اعمل مستودع (repo) جديد على GitHub
- روح على github.com واعمل New Repository (خليه Public أو Private براحتك).
- من غير ما تحط أي ملفات README أو gitignore وانت بتنشئه (عشان مش هيتعارض مع اللي عندك).

### 2) ارفع الملفات دي كلها للـ repo
لو معاك git على جهازك:
```bash
cd faqih-app
git init
git add .
git commit -m "أول نسخة من تطبيق فقيه الصغير"
git branch -M main
git remote add origin https://github.com/USERNAME/REPO_NAME.git
git push -u origin main
```
(بدّل USERNAME و REPO_NAME باسم المستخدم واسم المستودع بتاعك)

أو لو مش عايز تستخدم git من التيرمينال: اعمل "Upload files" من واجهة GitHub في المتصفح واسحب فولدر المشروع كامل.

### 3) البناء هيشتغل لوحده
بمجرد ما ترفع الملفات، GitHub Actions هيشغل تلقائي (فيه ملف `.github/workflows/build-apk.yml` بيعمل كده). تقدر كمان تشغّله يدوي من تبويب:
**Actions → Build APK → Run workflow**

### 4) نزّل الـ APK
بعد ما البناء يخلص (بياخد 3-5 دقايق تقريبًا):
- روح لتبويب **Actions**
- افتح آخر تشغيل (run) ناجح (علامة ✅)
- تحت **Artifacts** هتلاقي:
  - `faqih-alsaghir-debug-apk` ← ده الأسهل، نزّله وركبه على موبايلك على طول
  - `faqih-alsaghir-release-apk-unsigned` ← نسخة release لكن مش موقّعة (لو حبيت تنشرها على جوجل بلاي محتاجة توقيع أول)

### 5) تنصيب الـ APK على موبايلك
- بعد التنزيل، لو ظهرلك تحذير "Install unknown apps" وافق عليه من إعدادات الموبايل (مرة واحدة بس).
- افتح ملف الـ APK ونصّبه عادي.

## هيكل المشروع
```
app/src/main/assets/index.html   ← ملف التطبيق الأصلي (الموسوعة) بعد تنظيفه من سكريبتات Cloudflare اللي محتاجة نت
app/src/main/java/.../MainActivity.java  ← WebView بسيط بيفتح index.html
app/src/main/res/mipmap-*/       ← أيقونة التطبيق (تقدر تستبدلها بأيقونة من تصميمك)
.github/workflows/build-apk.yml  ← سكريبت البناء التلقائي
```

## ملاحظات مهمة
- التطبيق شغال بالكامل **أوفلاين** — مفيش أي اتصال بالإنترنت مطلوب، وشلت منه سكريبت تحليل الزيارات بتاع Cloudflare اللي كان موجود في الملف الأصلي.
- لو عايز تغيّر اسم التطبيق أو الـ package name، عدّل في:
  - `app/build.gradle` (السطر بتاع `applicationId` و `namespace`)
  - `app/src/main/res/values/strings.xml` (`app_name`)
- لو عايز توقّع الـ release APK للنشر على جوجل بلاي، محتاج تعمل keystore وتضيف خطوة توقيع في الـ workflow — قولّي لو عايز أساعدك في الخطوة دي.
- الأيقونة اللي حطيتها دلوقتي شكل بسيط (هلال ونجمة) كبداية، تقدر تستبدلها بصورة PNG بنفس الأسماء والمقاسات في فولدرات `mipmap-*`.
