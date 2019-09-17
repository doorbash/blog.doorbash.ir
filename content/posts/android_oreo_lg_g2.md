---
layout: post
title: نصب اندروید 8.1 Oreo روی LG G2
img: android_oreo_g2.jpeg
date: 2018-10-01
---

این آموزش برای نصب LineageOS 15.1 (اندروید 8.1) روی گوشی LG G2 d802 هست.

برای مدل های دیگر G2 هم میتونید این آموزش رو دنبال کنید منتها باید بعضی جاها فایل مربوط به دستگاه خودتون رو دانلود کنید بجای فایلی که من نوشتم.

اولین کاری که باید انجام بدیم فلش کردن رام استوک LP (همون Lollipop یا اندروید 5.0.2) هست.برای اینکار اول باید فایل kdz برای LP رو دانلود کنیم:

{{<link "https://www.iranmobile.org/%D8%B1%D8%A7%D9%85-%D8%B1%D8%B3%D9%85%DB%8C-lg-g2/">}}

{{<link "https://lg-firmwares.com/lg-d802-firmwares/firmwares/">}}

نرم افزار LG Flash Tool رو دانلود میکنیم:

{{<link "https://drive.google.com/open?id=1raZzks3nHvCF8Er8JKk-ZwZRCmrTGg57">}}

درایور گوشی رو روی ویندوز دانلود و نصب میکنیم:

{{<link "https://drive.google.com/open?id=15dqqAGXKp7KkZeKy-BNztG7zFAe1zODb">}}

همونطور که اینجا آموزش داده شده kdz را روی گوشی فلش میکنیم.

حالا باید روتش کنیم. اول این فایل رو دانلود میکنیم:

{{<link "https://drive.google.com/open?id=1SfoZxRnyC6V9vygVCS_CSg7EiENK4dzK">}}

بعد گوشی رو به حالت Developer Mode میبریم و دیباگ از طریق USB رو فعال میکنیم.

برای فعال کردن Developer Mode باید 5 بار روی Build Number داخل Settings گوشی قسمت About Phone زد.

بعد یک گزینه در تنظیمات اضافه میشه به اسم Developer Option که داخل اون هم خودش باید فعال باشه هم USB Debug یا یه همچین چیزی.

بعد فایل LG Root Script.bat رو از توش با cmd اجرا میکنیم و گوشی ریستارت میشه.(برای اینکه adb روی ویندوز گوشی رو بشناسه اگر پیغام باز شد در اندروید که باید این اجازه رو بهش بدید)

حالا اسکریپت تو خط Looking for LG serial port.. میمونه و جلوتر نمیره. حالا باید گوشی رو دستی به حالت دانلود مود ببریم . برای اینکار کابل USB رو قطع میکنیم گوشی رو خاموش میکنیم. دکمه ولوم بالا رو میگیریم و کابل USB رو وصل میکنیم. بعدش خط دوم تا جایی که نوشته :

{{< code >}}echo Rebooting into Download mode..{{< /code >}}

رو پاک میکنیم تا اسکریپت به این شکل در بیاد:
{{< code >}}@echo off
Send_Command.exe \\.\%comPath% < enterDownload | echo off
{{< /code >}}

حالا دوباره اجراش میکنیم و گوشی روت میشه. اگر ریستارت نشد خودمون ریستارتش میکنیم و با برنامهAuto Rec ریکاوری TWRP رو نصب می کنیم: (فایل مخصوص d802 هست)

{{<link "https://drive.google.com/open?id=1AP8M9V9WwzcBi84DjC__ZSZXuvARdhDZ">}}

حالا با دستور زیر میریم تو حالت ریکاوری:

{{< code >}}adb reboot recovery
{{< /code >}}

نسخه TWRP الان 2.8.6.1 هست. نسخه جدیدتر رو دانلود میکنیم از اینجا: (فایل مخصوص d802 هست)

{{<link "https://drive.google.com/open?id=1-EF_lKkltmg8SVm4PWZmmJTFA9guFIUV">}}

اگر d802 نیستید از اینجا:

{{<link "http://www.gregtwallace.com/lgg2-projects/twrp/">}}

بعد با TWRP نصبش میکنیم (قبلش باید فایل زیپ رو ریخته باشید روی مموری)

** بعدش اینو نصب میکنیم:(فایل مخصوص d802 هست)

{{<link "https://drive.google.com/open?id=1Tm2669H1KxRfZ-rS69hqlGhenuKPopar">}}

اگر d802 نیستید از اینجا:

{{<link "https://forum.xda-developers.com/lg-g2/development/boot-g2-hybrid-bootstacks-t3183219">}}

حالا میریم لینیج او اس رو دانلود میکنیم:

{{<link "https://download.lineageos.org/d802">}}

از اکسترا هم فایل مربوط به روت رو دانلود میکنیم:

{{<link "https://download.lineageos.org/extras">}}

جی اپس هم از اینجا دانلود میکنیم:

{{<link "https://opengapps.org/">}}

دیتا و سیستم و کش ها رو wipe میکنیم

لینیج و su و جی اپس به همین ترتیب نصب میکنیم با TWRP

اگه هی ریبوت میشد fixboot رو از اینجا نصب می‌کنیم:

{{<link "https://drive.google.com/open?id=1jA5ZgXEEuVwkHErckNQOhrCjOtKPi1bZ">}}

اگه فایده نداشت این دستورات رو با adb shell میزنیم:

{{< code >}}dd if=/dev/block/platform/msm_sdcc.1/by-name/misc of=/sdcard/miscBU.img
dd if=/dev/zero of=/dev/block/platform/msm_sdcc.1/by-name/misc{{< /code >}}

نصب انجام شد. روی سیستم بوت میکنیم و تمام.

نکته: اگر آپدیت اومد توی گوشی و نصب کردید و بعدش گوشی هی ریبوت میشد از روش بالا گفتم میشه مشکلش رو حل کرد. ریکاوری ای که اینجا ** گفتم رو اگر نصب کنید باید مشکل بوت لوپ که با نصب از طریق OTA (OTA یعنی همین نصب آپدیت که میاد رو گوشی) بوجود میاد رو حل کنه همونطور که اینجا گفته:

{{<link "https://forum.xda-developers.com/lg-g2/orig-development/rom-lineageos-15-alpha-d802-t3707239">}}

<h4>نحوه رفتن به ریکاوری از حالت خاموش:</h4>
<ol>
<li>دکمه ولوم پایین + پاور رو میگیریم.</li>

<li>وقتی لوگوی الجی اومد ول میکنیم دوباره ولوم پایین + پاور میگیریم (بعضی وقتام اگه نشد ولوم پایین + ولوم بالا)</li>

<li>3-یس میزنیم دوبار و بعدش وارد ریکاوری میشه</li>
</ol>
<h4>نحوه رفتن به حالت دانلود :</h4>

از حالت خاموش دکمه ولوم بالا رو میگیریم. سپس کابل یو اس بی که اون سرش به کامپیوتر وصل شده رو به گوشی وصل میکنیم.و بعد پاور رو میگیریم


منبع:

{{<link "https://forum.xda-developers.com/lg-g2/orig-development/rom-lineageos-15-alpha-d802-t3707239">}}