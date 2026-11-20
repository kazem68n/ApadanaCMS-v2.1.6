با عنایت به اینکه پروژه آپادانا تحت مجوز GPL V3 منتشر شده، اقدام به توسعه و تغییر و توزیع نسخه‌ جدید نمودیم.

سیستم مدیریت محتوای آپادانا نگارش 2.1.6 منتشر شد. آپادانا یک سایت ساز کاملا ایرانی و به صورت متن باز منتشر می شود. با استفاده از این مدیریت محتوا می توانید در کم ترین زمان سایت دلخواه خود را راه اندازی کنید. از قابلیت های کلیدی این سیستم میتوان به مدیریت صفحات، مطالب ، نوشته ها و فایل ها اشاره کرد.

در جهت زنده نگهداشتن سیستم های ایرانی، ضمن حمایت از نویسندگان سیستم ها و با توجه به اینکه سیستم از سال 1392 آپدیت نشد، تصمیم بر این شد تا با نسخه های جدید php سازگار شود. در حال حاضر نسبت به بروزرسانی و بازنویسی بخش هایی از سیستم اقدام شد. باگ های موجود برطرف گردید.

برخی از بروزرسانی های سیستم مدیریت محتوای ایرانی آپادانا نسخه 2.1.6:

# Apadana CMS 2.1.0

**پایه:** شاخه develop گیت‌هاب (نگارش ۱.۱-آلفا)  
**هدف:** امنیت مدرن + سازگاری PHP 8.x + حفظ امکانات ۱.۱

## تفاوت پایه ۱.۱ نسبت به ۱.۰.x اولیه
- پشتیبانی mysqli (دوگانه با mysql قدیمی)
- CKEditor جدیدتر، PHPMailer به‌روزتر
- حذف TCPDF و GeoIP از هسته
- option.function.php، pagination با حروف کوچک
- کپچای فارسی، آرشیو پست، تقویم، موضوعات سلسله‌مراتبی
- سیستم قلاب (hooks)، فیلد compatibility ماژول‌ها

## تغییرات امنیتی و فنی ۲.۱ (روی پایه ۱.۱)
1. **password_hash / password_verify** با سازگاری عقب‌رو برای md5(sha1)
2. **ارتقا خودکار هش** هنگام ورود موفق با رمز قدیمی
3. **Prepared Statements** در database.class (`prepare_query`, `prepare_fetch`, `prepare_num_rows`)
4. **الزام mysqli** (حذف وابستگی به mysql_* حذف‌شده در PHP ۷+)
5. **CSRF** (`csrf_token`, `csrf_field`, `csrf_check`)
6. **Rate-limit ورود**
7. **هدرهای امنیتی** و سخت‌سازی session
8. حذف **create_function** از template
9. جایگزینی `$str{$i}` با `$str[$i]` برای PHP ۸
10. free_result/close امن در برابر mysqli_result بسته‌شده

 
## 2.1.1 

- حذف کامل مسیر `mysql_*` از database.class
- CSP + Permissions-Policy
- media manager امن جایگزین elFinder (connector غیرفعال)
- VARCHAR(255) برای member_password در نصب + اسکریپت ارتقا
- password_hash هنگام نصب ادمین
- prepared برای لاگین account و برخی کوئری‌های posts
- .htaccess ضد اجرای PHP در uploads
 
 
 امنیت مدرن و PHP 8  اعمال شد.

  
### ۲.۲ پایگاه‌داده و SQL Injection
- **الزام mysqli** — حذف مسیرهای `mysql_*`
- متدهای **`prepare_query` / `prepare_fetch` / `prepare_num_rows`**
- مهاجرت ده‌ها کوئری حساس در:
  - لاگین ادمین و کاربری
  - ماژول posts (فرانت و ادمین)
  - comments، blocks، banned
  - account (ثبت‌نام، ایمیل، فراموشی رمز، session، جستجوی اعضا)
- سخت‌سازی LIKE جستجو (`%` / `_`) و whitelist برای ORDER BY

### ۲.۳ CSRF و Session
- توابع **`csrf_token` / `csrf_field` / `csrf_check`**
- Session: `cookie_httponly`، `use_only_cookies`، `use_strict_mode`
- هدرهای امنیتی در PHP و `.htaccess`

### ۲.۴ XSS و قالب
- تبدیل `preg_replace /e` در template و **BBCode** به `preg_replace_callback`
- **whitelist توابع** قابل فراخوانی از تگ‌های قالب + لیست سیاه توابع خطرناک
- **`sanitize_user_html()`** برای نظرات
- **`safe_search_term()`** برای جستجو
- محدودسازی محتوای CKEditor (مسدود script/iframe/رویدادهای on*)

### ۲.۵ فایل و رسانه
- **جایگزینی elFinder** با مدیریت رسانه محدود به `uploads/`
- غیرفعال‌سازی connector قدیمی elFinder
- لیست پسوند مجاز + جلوگیری از path traversal
- **`.htaccess` ضد اجرای PHP** در `uploads/`
- ماژول **downloads** (جدا/همراه بسته): آپلود امن، تب فایل‌های آپلود‌شده، صفحه‌بندی، ارتباط با عنوان دانلود

### ۲.۶ کتابخانه‌ها
- **PHPMailer 6.9.3** (TLS/SMTPS، اعتبارسنجی ایمیل)
- CKEditor  با امنیت **config امن‌تر** 
- اصلاحات مرتبط با PHP 8 در فیلتر، template، html class و …

### ۲.۷ نصب و استقرار
- `member_password` به **VARCHAR(255)** در SQL نصب
- اسکریپت ارتقا برای نصب‌های قبلی
- **قفل installer** پس از وجود `config.inc.php` (مگر `install/reinstall.allow`)
- پیش‌فرض **`debug_system = false`**
- `display_errors = Off` در `index.php` برای production
- `.htaccess` ریشه: مسدود کردن config، فایل‌های حساس، و ترجیحاً install

### ۲.۸ سایر رفع‌های عملیاتی (مسیر گفتگو)
- خطای `create_function` در نصب
- صفحه سفید / Content Encoding (Gzip)
- null به توابع رشته در PHP 8
- آمارگیر counter، نمایش jdate، backup، voting
- ثبت‌نام کاربر، لاگین ادمین، رمز ادمین
- ماژول دانلود با UI شبیه shop و نوار پیشرفت آپلود

---

## ۳. پیش‌نیاز سرور (Production)

| مورد | حداقل | توصیه |
|------|--------|--------|
| PHP | 8.0 | 8.1 یا 8.2 |
| افزونه‌ها | mysqli, mbstring, openssl, json, session, filter | + zip, gd |
| دیتابیس | MySQL 5.7 / MariaDB 10.3 | MySQL 8 / MariaDB 10.6+ |
| وب‌سرور | Apache یا Nginx | Apache + mod_rewrite + mod_headers |

  
## ۵. چک‌لیست امنیت قبل از Go-Live

- [ ] `debug_system = false`
- [ ] `display_errors` خاموش
- [ ] پوشه `install` حذف شده
- [ ] HTTPS فعال
- [ ] `member_password` به VARCHAR(255) ارتقا یافته (در صورت دیتابیس قدیمی)
- [ ] رمز ادمین قوی
- [ ] بکاپ منظم فایل + دیتابیس
- [ ] افزونه/قالب فقط از منبع مورد اعتماد
- [ ] دسترسی FTP/SSH محدود
- [ ] مجوز پوشه‌ها ترجیحاً ۷۵۵/۶۴۴ (نه ۷۷۷ عمومی)

- اضافه شدن پراگرس بار برای بارگزاری تم و افزونه ها 

و...............
