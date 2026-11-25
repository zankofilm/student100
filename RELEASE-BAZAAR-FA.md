# ساخت APK انتشار (Release) برای کافه‌بازار

گردش‌کار `.github/workflows/android-apk.yml` اکنون نسخه Release را می‌سازد و امضای APK را با کلید اختصاصی از GitHub Secrets می‌گیرد. فایل Keystore یا رمزها نباید در مخزن GitHub قرار بگیرند.

## ۱) ساخت کلید امضا
روی کامپیوتری که Java نصب است، در ترمینال دستور زیر را اجرا کنید:

```bash
keytool -genkeypair -v -keystore madreseyar-release.jks -keyalg RSA -keysize 2048 -validity 10000 -alias madreseyar
```

رمزها را خودتان تعیین کنید و فایل `madreseyar-release.jks` را در چند محل امن و آفلاین پشتیبان بگیرید. اگر نسخه قبلی برنامه با کلید دیگری امضا شده، برای به‌روزرسانی همان نسخه باید کلید اصلی قبلی را پیدا کنید؛ کلید جدید جایگزین آن نمی‌شود.

## ۲) تبدیل Keystore به Base64
Linux/macOS:
```bash
base64 -w 0 madreseyar-release.jks
```

Windows PowerShell:
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("madreseyar-release.jks"))
```

خروجی Base64 را کپی کنید. این خروجی نیز محرمانه است.

## ۳) افزودن GitHub Secrets
در مخزن GitHub وارد Settings → Secrets and variables → Actions → New repository secret شوید و این چهار Secret را بسازید:

- `ANDROID_KEYSTORE_BASE64` = خروجی Base64 فایل Keystore
- `ANDROID_KEYSTORE_PASSWORD` = رمز Keystore
- `ANDROID_KEY_ALIAS` = نام alias (در مثال بالا `madreseyar`)
- `ANDROID_KEY_PASSWORD` = رمز کلید

مقادیر را در کد، فایل workflow یا پیام عمومی قرار ندهید.

## ۴) ساخت و دریافت APK
از تب Actions، گردش‌کار **Build Android APK (Release)** را اجرا کنید. پس از موفقیت، در بخش Artifacts فایل `Madreseyar-Student-Release-APK` را دانلود کنید. فایل داخل آن `app-release.apk` است و برای بارگذاری در کافه‌بازار استفاده می‌شود.

## نکته مهم درباره نسخه نصب‌شده
اگر می‌خواهید APK جدید روی نسخه قبلی به‌صورت Update نصب شود، باید با همان کلید امضای نسخه قبلی ساخته شود. اگر کلید قبلی در دسترس نیست، با کلید جدید امکان به‌روزرسانی مستقیم وجود ندارد؛ حذف نسخه قبلی و نصب تازه باعث پاک شدن داده‌های محلی برنامه روی گوشی می‌شود.
