# فقيه الصغير — نسخة Capacitor (زي أكسفورد 3000 اللي شغالة عندك)

ده نفس الأسلوب بالظبط اللي شغال معاك في مشروع أكسفورد 3000: Capacitor بيبني مشروع الأندرويد لوحده وقت الـ CI، فمفيش تعارضات مكتبات زي اللي حصلت قبل كده.

## المشروع فيه 4 حاجات بس
```
package.json
capacitor.config.json
www/index.html          ← ملف التطبيق (الموسوعة) بعد التنظيف من سكريبتات Cloudflare
.github/workflows/build.yml
```

**ملحوظة:** مفيش فولدر `android` — ده بيتعمل تلقائي وقت البناء على GitHub، مش محتاج ترفعه.

## خطوات الرفع

### الأسهل: احذف الـ repo القديم وابدأ repo جديد نضيف
لو الـ repo بتاعك فيه فوضى من المحاولات اللي فاتت، ابدأ من جديد أنضف:
1. اعمل repo جديد على GitHub (أو امسح محتوى القديم وابدأ تاني)
2. ارفع الـ 3 حاجات دي بالسحب والإفلات: `www` و `package.json` و `capacitor.config.json`
3. فولدر `.github` (المخفي) هيتعمل يدوي زي المرة اللي فاتت:
   - **Add file → Create new file**
   - اكتب اسم الملف: `.github/workflows/build.yml`
   - الصق فيه المحتوى ده بالظبط:

```yaml
name: Build Android APK
on:
  push:
    branches: [ main ]
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '21'
      - name: Install dependencies
        run: npm install
      - name: Add Android platform if missing
        run: |
          if [ ! -d "android" ]; then
            npx cap add android
          fi
      - name: Sync Capacitor
        run: npx cap sync android
      - name: Build debug APK
        run: cd android && chmod +x gradlew && ./gradlew assembleDebug
      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: app-debug
          path: android/app/build/outputs/apk/debug/app-debug.apk
```

4. Commit changes

### بعد كده
- روح تبويب **Actions** وتابع البناء (أو شغّله يدوي: Actions → Build Android APK → Run workflow)
- بعد ما يخلص، هتلاقي الـ APK جاهز تحت **Artifacts** باسم `app-debug`

## ملاحظات
- الأيقونة الافتراضية اللي Capacitor بيحطها هي الأيقونة الأساسية بتاعته — تقدر تغيّرها بعدين لو حبيت.
- اسم التطبيق وexact الـ package name موجودين في `capacitor.config.json` لو عايز تغيّرهم.
- التطبيق شغال بالكامل أوفلاين زي ما هو متوقع.
