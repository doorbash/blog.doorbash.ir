---
layout: post
title: "پروژه جدید: بازی Solitaire Klondike"
img: solitaire.jpg
date: 2021-05-21
---

{{<image_link "https://play.google.com/store/apps/details?id=io.github.doorbash.solitaire" "google-play-badge.png" "io.github.doorbash.solitaire" 207 80>}}

این بازی (Solitaire) یک بازی اندرویدی یک نفره با ورق (پاسور) هست که فعلا ویژگی های زیر رو داره:
- حالت سه کارتی و تک کارتی
- حرکت با لمس کارت
- نمایش امتیاز و زمان
- دکمه آندو (برگرداندن)
- چینش دست چپی
- امکان تعیین جهت صفحه (افقی، عمودی یا چرخش خودکار)
- دو نوع چینش حالت افقی
- نمایش آمار بازی و لیست بازی های قبلی 
- پشتیبانی از دو زبان فارسی و انگلیسی
- امکان قطع و وصل کردن صدای بازی

### بهره‌وری یا performance

#### - فریم در ثانیه (fps)

 تمام تصاویر، آیکون ها و فونت هایی که تو بازی استفاده شده در قالب یک عکس 1024x1024 پک شده. که باعث میشه فقط یک تکسچر بایند بشه و تعداد draw call ها تو هر فریم میشه 1.

#### - ذخیره و بازیابی از فایل
برای پیاده سازی ذخیره و بازیابی بازی اول میخاستم از JSON استفاده کنم ولی ازونجایی که JSON به صورت متنی هست بهتر بود از روشی که باینری ذخیره میکنه استفاده میکردم. برای همین {{<inline_link "https://github.com/protocolbuffers/protobuf" "protobuf">}} رو انتخاب کردم. کتابخونه دیگه ای که از لحاظ بهره وری {{<inline_link "https://github.com/lxohi/java-json-protobuf-flatbuffers-kryo-jvm-benchmark" "توی بنچمارک هایی که دیدم">}} به protobuf نزدیکه {{<inline_link "https://github.com/EsotericSoftware/kryo" "kryo">}} بود که اونم میتونست انتخاب خوبی باشه.

این مثلا نحوه ذخیره لیست بازی ها رو نشون میده:
هر بازی 56 بایت فضا میگیره (52 کارت به اضافه 4 بایت سربار که خود protobuf برای ذخیره ساختار اضافه میکنه)

```plaintext
message GamePB {
  bytes data = 1;
}

message GamesPB {
  repeated GamePB list = 1;
}
```

#### - proguard
تفاوت حجم فایل باندل نهایی بدون proguard و با proguard برای این پروژه یچیزی حدود 15MB بود. 
غیر از اون میشه برای proguard قوانینی گذاشت که null check های اضافی کاتلین رو خودش خودکار حذف کنه که توی performance تاثیر داره.

```plaintext
-assumenosideeffects class kotlin.jvm.internal.Intrinsics {
  public static void checkExpressionValueIsNotNull(java.lang.Object, java.lang.String);
  public static void checkFieldIsNotNull(java.lang.Object, java.lang.String);
  public static void checkFieldIsNotNull(java.lang.Object, java.lang.String, java.lang.String);
  public static void checkNotNull(java.lang.Object);
  public static void checkNotNull(java.lang.Object, java.lang.String);
  public static void checkNotNullExpressionValue(java.lang.Object, java.lang.String);
  public static void checkNotNullParameter(java.lang.Object, java.lang.String);
  public static void checkParameterIsNotNull(java.lang.Object, java.lang.String);
  public static void checkReturnedValueIsNotNull(java.lang.Object, java.lang.String);
  public static void checkReturnedValueIsNotNull(java.lang.Object, java.lang.String, java.lang.String);
  public static void throwUninitializedPropertyAccessException(java.lang.String);
}
```

### حجم فایل نصبی
از اونجایی که حجم فایل  نهایی زیر 15MB میشه. میتونیم از قابلیت Instant Play گوگل پلی استفاده کنیم. اینطوری یک دکمه Try now کنار دکمه نصب نشون میده که کاربر میتونه سریع بازی رو بدون نصب ببینه و بعد اگر خواست نصب کنه. به همین دلیل پایین بودن حجم یک مزیت محسوب میشه.

با جدا کردن فایل .so هر معماری تو gradle و حذف فایل استایل libgdx و تعریف دستیشون تو کد و یه سری حذف های جزئی و proguard تونستم حجم فایل نهایی رو بیارم روی 750KB. 

اما بعد که protobuf اضافه کردم شد 920KB. هنوز زیر یک مگابایت بود! تا اینکه مجبور شدم برای رابط کابری از کتابخونه material گوگل استفاده کنم که با فونت ها و 20,000 بازی آسان حجم نهایی 3.6MB شد.

![not great not terrible](https://media.giphy.com/media/B2l0NnxK9KiVa0CXBh/giphy.gif)

### پیاده سازی

#### چالش ها

**ریزش کارت ها آخر بازی**:
برای پیاده کردن این قسمت اول فکر کردم با پاک نکردن صفحه زمان draw میشه ولی باعث شد کارت ها جاهای اشتباهی ترسیم بشن و کلا عجیب غریب شد. که بعد فهمیدم بخاطر double buffering هست.

بعدش کاری که کردم یک فریم بافر به اندازه صفحه ساختم. و زمان draw روی اون کارت ها رو draw کردم بدون اینکه پاکشون کنم. و بعد خود فریم بافر رو draw کردم.

```kotlin
val width = Gdx.graphics.width
val height = Gdx.graphics.height
fbo = FrameBuffer(Pixmap.Format.RGBA8888, width, height, false)
matrix = Matrix4().apply {
    setToOrtho2D(0f, height.toFloat(), width.toFloat(), -height.toFloat())
}

... 

//draw

fbo.use {
  // draw on fbo
}

stage.batch.use(stage.camera.combined) {
    // draw fbo
    it.draw(fbo.colorBufferTexture, 0f, 0f)
}
```

{{<image src="solitaire_card_trail.jpg" alt="card trail">}}

#### جزئیات فنی
بازی با استفاده از {{<inline_link "https://libgdx.com/" "LibGDX">}} ساخته شده و با زبان برنامه نویسی {{<inline_link "https://kotlinlang.org/" "کاتلین">}} (و کمی هم جاوا)

برای شروع ساخت پروژه از {{<inline_link "https://github.com/tommyettinger/gdx-liftoff" "gdx-liftoff">}}

برای گیم پلی و منو ها از {{<inline_link "https://github.com/libgdx/libgdx/wiki/Scene2d" "Scene2D">}} که برای LibGDX هست. 

از {{<inline_link "https://github.com/libktx/ktx" "LibKTX">}} برای کار کردن راحت تر با LibGDX

برای پک کردن عکس ها و فونت ها از {{<inline_link "https://github.com/raeleus/skin-composer" "Skin Composer">}}

برای ذخیره و بازیابی استیت بازی و همینطور بازیابی لیست بازی های آسان از {{<inline_link "https://github.com/protocolbuffers/protobuf" "protobuf">}}

 منوهای بازی (و دکمه ها و switch و spinner) هم ویجت کاستوم هستن که از  floatingGroup از کتابخونه {{<inline_link "https://github.com/kotcrab/vis-ui" "vis-ui">}} ارث بری میکنند. البته فقط دیالوگ منو، تنظیمات و خروج از بازی.
برای ساخت دیالوگ "بازی های قبلی" و "شما برنده شدید" از Dialog اندروید SDK استفاده شده. 

تم کل برنامه هم Theme.MaterialComponents.NoActionBar هست. 

از کتابخونه {{<inline_link "https://github.com/marlonlom/timeago" "timeago">}} برای نمایش تاریخ به صورت نسبی (مثلا دو ساعت پیش)

آیکون ها هم {{<inline_link "https://github.com/google/material-design-icons" "آیکون های متریال دیزاین">}} گوگل هستن