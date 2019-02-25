---
title: شرح كامل لإعداد Shiny-Server على Raspberry Pi
author: Hussain Alsalman
date: '2019-02-25'
slug: configure-shiny-server-on-RaspberryPi
tags:
  - متقدم
  - Shiny
---

## الهدف
هدفنا في هذه التدوينة ان نقوم بإعداد shiny-server على جهاز Raspberry Pi . سأتناول جميع الخطوات الازمة لتحقيق ذلك الهدف. السبب في إختيار Raspberry لأنها بيئة عمل آمنة ويمكننا البدء من الصفر في حال ارتبكنا اخطاء فادحة. الجدير بالذكر هو ان كل ما سوف نتعلم على هذا الجهاز الصغير ينطبق في مجمله على السيرفرات الكبيرة والتي يمكننا استأجرها. 

## متطلبات العتاد - Hardware
  - [Raspberry Pi B+](https://www.raspberrypi.org/products/raspberry-pi-3-model-b-plus/)
  - Keyboard 
  - Mouse 
  - HDMI cable
  - Screen
  - Micro USB power
  - MicroSD card (انا استخدم 32GB)
  - Computer/labtop
 
## متطلبات البرمجيات
  - [NOOBS v3.0](https://www.raspberrypi.org/downloads/noobs/)
  - OS sytem
    - Linux
    - MacOS

## إعداد الذاكرة MicroSD 
إذا كنت قد اشتريت ذاكرة جديدة او انك قررت ان تستخدم ذاكرة موجودة لديك فهذا يعني انك سوف تحتاج ان تعمل لها فورمات (إعادة تنسيق). هذه العملية تعتمد على النظام على جهازك. أنا استخدم نظام لينكس لذلك سوف اقوم بشرح الخطوات لهذا النظام. إذا كنت تستخدم نظام MacOS فيمكنك اتباع هذه التعليمات [هنا](https://computers.tutsplus.com/tutorials/how-to-install-noobs-on-a-raspberry-pi-with-a-mac--mac-57831). قبل ان نبدأ يجب ان يكون لديك صلاحيات الـ Admin حتى يمكنك تشغيل الأوامر في نافذة الـ Terminal. 
اول امر هو ان نجد عنوان الذاكرة في الجهاز عن طريق عرض جميع الديسكات في الكمبيوتر.

```
sudo fdisk -l
```
ستعرض لدينا قائمة طويلة من ضمنها معلومات الذاكرة لدينا . نحن نعلم ان هذه هي الذاكرة لأن حجمه قريب من حجم 32GB. 
```
Disk /dev/mmcblk0: 29.4 GiB, 31609323520 bytes, 61736960 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x000d26da
```
بعد ذلك سوف نقوم بمسح جميع الأقسام الموجودة على الذاكرة من خلال هذا الأمر حتى نتمكن من جعلها قسم واحد وبالتنسيق الذي نريده وهو ```FAT32```. 

```
sudo fdisk /dev/mmcblk0
```

سيقوم البرنامج بالإستعداد لإجراء الأوامر الخاصة التحكم بالذاكرة يمكننا كتابة ```m```حت نرى جميع الخيارات 

```
Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table

```

قم بإدخال الأوامر بالتتالي :

  1.   ```d``` لحذف الاقسام الموجودة . قد يتطلب منك الأمر لإعادة هذا الأمر لجميع الأقسام الموجودة

  2.   ```n``` إنشاء قسم جديد في الذاكرة . قم بإدخال رقم ```1``` لتحديد عدد الأقسام. في هذه الحالة نحن نريد قسم واحد فقط. كذلك اختار النوع ```p``` ليكون نوع القسم رئيسي. 

  3.   ```t``` لتغيير الصيغة للقسم الذي قمنا بإنشاءه . سوف يطلب منك البرنامج رقم القسم وهو رقم ```1```. كذلك سوف يطلب منك نوع الصيغة وستكون ```b```و هي صيغة ```FAT32```

  4.   ```w``` سيقوم بكتابة جميع هذه التخصيصات . 

بالرغم من أننا قمنا بتحديد تنسيق الذاكرة إلا ان كل ما قمنا بعمله هو إنشاء ذاكرة خالية . لذلك يجب علينا الآن فقط إعادة تنسيقها وستكون جاهزة للعمل. وهذا هو الأمر الذي المطلوب. 

```
mkfs.vfat /dev/mmcblk0p1
```

## تنصيب NOOBS على الذاكرة
اولا, اود ان اهنئك فلقد كانت الخطوة السابقة صعبة بعض الشيء خصوصا إذا كانت تجاربك مع برنامج Terminal قليلة. في هذه الخطوة سوف نقوم بعملية في غاية البساطة الا وهي فك ملفات NOOBS و وضعها في الذاكرة . فقط تأكد من ان المجلدات كما هي وليست داخل مجلد واحد. 

## إعداد النظام NOOBS 
النظام NOOBS جميل جدا فهو يسهل علينا اختيار اي نظام نريد من خلال شاشة تفاعلية. في هذه الخطوة قم بتوصيل جميع العتاد ثم قم بتشغيل الجهاز الصغير. إذا تكللت الخطوات السابقة بالنجاح فسوف ترى هذه الشاشة. 

<img class ="rounded mx-auto d-block" src="/post/2019-02-24-شرح-كامل-لإعداد-shiny-server-على-rasperry-pi_files/IMG_1595.jpg" alt="" width="300px"/>

في الصورة قمت بإضافة بعض التعليمات. كما نرى سوف نختار Raspbian Lite with no Desktop Envirnoment.  هذه نسخة بسيطة من نظام لينكس لا تحوي على سطح مكتب لذلك سيكون مشوارنا كله من هذه النقطة على شاشة الاوامر Command Line Interface. 

بعد اختيار هذه النسخة سوف يقوم NOOBs بتحميل النظام وتنصيبه . كذلك سيغير اقسام الذاكرة حسب متطلبات النظام. بعد ذلك كله سيعاد تشغيل الجهاز وستظهر لك شاشة الأوامر . هذه 

<img class="rounded mx-auto d-block" src="/post/2019-02-24-شرح-كامل-لإعداد-shiny-server-على-rasperry-pi_files/IMG_1596.jpg" alt="" width="300px"/>

وهذه عادة معلومات الدخول 

```
login ID : pi
Password : raspberry
```

## إعداد متطلبات Shiny Server
لتنصيب Shiny Server  سوف نحتاج إلى أن ننصب لغة  R بكل تأكيد. لكن يجدر بنا أولا ان نتأكد من أننا سوف نحصل على أخر نسخة من R . نقوم بذلك عن طريق إضافة مصدر جديد لمثبت البرامج  عن طريق الكود التالي

```
sudo nano \etc\apt\sources.list 
```
هذا سوف يفتح لنا ملف المصادر سنضيف هذا السطر في أخر الملف ثم نقوم بحفظ الملف عن طريق ```ctrl+O``` ثم نغلقه عن طريق ```ctrl+X```.

```
# This is the line
deb https://cloud.r-project.org/bin/linux/debian stretch-cran35/
```
### تحميل وتنصيب R
الآن نقوم بتحميل لغة R عن طريق الأوامر التالية: 

```
sudo apt-get update
sudo apt-get install r-base-core 
sudo apt-get upgrade -y
```
هناك احتمال أن يتم رفض هذه الأوامر لأنه ستقرأ من مصدر غير موّقع. يمكننا توقيعه عن طريق إضافة ```public key``` عن طريق الأوامر التالية. 

```
gpg --keyserver keyserver.ubuntu.com --recv  256A04AF 
gpg --export --armor E084DAB9 | sudo apt-key add - && sudo apt-get update  
```

### تحميل حزم R اللازمة لـShiny 
هناك قائمة من الحزم التي تعتمد عليها حزمة Shiny وهي كالتالي. 

```
Rcpp 
httpuv 
mime 
jsonlite 
digest 
htmltools 
xtable 
R6 
Cairo 
sourcetools
rmarkdown
```
قبل تنصيب هذه الحزم لابد ان نغير صلاحيات الكتاب في ملف ```/usr/local/lib/R/site-library```  عن طريق هذا الأمر 

```
sudo chmod 777 /usr/local/lib/R/site-library
```
هذا الأمر سيسمح لنا بتنصيب الحزم بدون إنشاء ملف خاص للمستخدم ```pi``` هذا الأمر مهم لأننا نريد من السيرفر إمكانية الوصول للملف عن طريق حساب مختلف.

الآن يمكننا تنصيب الحزم التي ذكرناها خلال هذا الأمر علما بأننا سوف نغير كلمة ```package```إلى الحزمة التي نريد تنصيبها 

```
R
install.packages("package")
```
سوف نجري هذا الأمر لكل حزمة من القائمة. 

ملاحظة : بالنسبة لحزمة Cairo قد يتطلب الأمر منك إلى تنصيب هذه الحزم ```libcairo2-dev libxt-dev``` عن طريق هذا الأمر 
```
sudo apt-get install libcairo2-dev libxt-dev
```

إذا تمت كل تلك العمليات بنجاح, سيكون ممكنا لك أن تقوم بتنصيب حزمة Shiny بدون مشاكل
```
R
install.packages("shiny")
```
 
### تحميل بعض الحزم المهمة لـShiny Server 
هذه الحزم يعتمد عليها الكود الذي طوروه فريق Rstudio لذلك لابد ان يكونو حاضرين في النظام. أولا سنقوم بتحميل حزمة git لأنها مهمة لتحميل احدث الحزم المتوفرة على منصة github. 
```
sudo apt-get install git
```
الحزمة التالية هي ```fs``` وهي مهمة لإدارة الملفات بشكل مستقل عن النظام. 

```
git clone https://github.com/r-lib/fs.git
sudo R CMD INSTALL fs
sudo rm -rf fs

```
الحزمة التالية هي ```cmake``` وهي ايضا تلعب دور مشابه للحزمة السابقة لكنه تركز على تنظيم عملية بناء الكود بشكل مبسط للمبرمجين. هذه الأوامر سوف تأخذ بعض الوقت

```
wget https://cmake.org/files/v3.13/cmake-3.13.2.tar.gz # Check for the latest version on https://cmake.org/files/
tar xzf cmake-3.13.2.tar.gz
cd cmake-3.13.2
./configure; make
sudo make install
cd

```
سوف نقوم الآن بتنصيب سيرفر الـHTML وهو nginx وهو احد المتطلبات ايضا. يمتاز هذا السيرفر بخفته وبساطته على النظام. ويمكننا تنصيبه بهذه الأوامر 

```
sudo apt install nginx
sudo chown -R www-data:pi /var/www/html/
sudo chmod -R 770 /var/www/html/
```

## تحميل وتنصيب Shiny Server
الآن يمكننا أن نقوم بتحميل وتنصيب Shiny-Server أخيرًا. كل ما علينا هو تنفيذ هذه الأوامر 

```
git clone https://github.com/rstudio/shiny-server.git
cd shiny-server
DIR=`pwd`
PATH=$DIR/bin:$PATH
mkdir tmp
cd tmp
PYTHON=`which python`
sudo cmake -DCMAKE_INSTALL_PREFIX=/usr/local -DPYTHON="$PYTHON" ../
sudo make
mkdir ../build
sed -i '8s/.*/NODE_SHA256=7a2bb6e37615fa45926ac0ad4e5ecda4a98e2956e468dedc337117bfbae0ac68/' ../external/node/install-node.sh
sed -i 's/linux-x64.tar.xz/linux-armv7l.tar.xz/' ../external/node/install-node.sh
(cd .. && sudo ./external/node/install-node.sh)
(cd .. && ./bin/npm --python="${PYTHON}" install --no-optional)
(cd .. && ./bin/npm --python="${PYTHON}" rebuild)
sudo make install
```
في الكود اعلاه قمنا ايضا بتعديل كود المصدر لـ Shiny Server حتى يتطابق مع نسخة الـNodejs الموجود لدينا. يمكنك متابعة النقاش حول هذا الحل بين المطورين [هنا](https://github.com/rstudio/shiny-server/issues/347)

## ضبط الإعدادات للـShiny Server 
مبروك لقد قطعت مشوار طويل تخلله الكثير من الإنتظار وشرب القهوة (هذا ما كنت افعله أنا ). الآن كل ما علينا هو إنشاء مستخدم للسيرفر سنسميه ```shiny``` وضبط بعض الإعدادات في ملفات السيرفر  وذلك من خلال الأوامر التالية 

هذه هي الإعدادات 
```
cd
sudo ln -s /usr/local/shiny-server/bin/shiny-server /usr/bin/shiny-server
sudo useradd -r -m shiny
sudo mkdir -p /var/log/shiny-server
sudo mkdir -p /srv/shiny-server
sudo mkdir -p /var/lib/shiny-server
sudo chown shiny /var/log/shiny-server
sudo mkdir -p /etc/shiny-server
cd
sudo wget \
https://raw.github.com/rstudio/shiny-server/master/config/upstart/shiny-server.conf \
-O /etc/init/shiny-server.conf
sudo chmod 777 -R /srv

```
الأن نريد ان ننشأ عملية على النظام تقوم بتشغيل السيرفر بشكل تلقائي فور تشغيل الـRaspberry Pi. كل ما علينا هو صنع ملف فيه بعض التعليمات وإعداده كما هو في الكود ادناه 

```
# Configure shiny-server autostart 
sudo nano /lib/systemd/system/shiny-server.service # Paste the following
    #!/usr/bin/env bash
    [Unit]
    Description=ShinyServer
    [Service]
    Type=simple
    ExecStart=/usr/bin/shiny-server
    Restart=always
    # Environment="LANG=en_US.UTF-8"
    ExecReload=/bin/kill -HUP $MAINPID
    ExecStopPost=/bin/sleep 5
    RestartSec=1
    [Install]
    WantedBy=multi-user.target

sudo chown shiny /lib/systemd/system/shiny-server.service
sudo systemctl daemon-reload
sudo systemctl enable shiny-server
sudo systemctl start shiny-server
```
 وهنا ننشأ مجموعة نسميها ```shiny-apps``` ونعطيها بعض الصلاحيات لإدارة السيرفر. كذلك نقوم بإضافة كل من المستخدمين ```pi``` و ```shiny``` لهذه المجموعة الإدارية. 
 
```
sudo groupadd shiny-apps
sudo usermod -aG shiny-apps pi
sudo usermod -aG shiny-apps shiny
cd /srv/shiny-server
sudo chown -R pi:shiny-apps .
sudo chmod g+w .
sudo chmod g+s .
```
## طريقة الإستخدام
بعد إدخال جميع الأوامر السابقة بنجاح نقوم بإعادة تشغيل الـRaspberry عن طريق هذا الأمر 
```
reboot
```
بعد إعادة التشغيل, يفترض بالجهاز تشغيل السيرفر بشكل تلقائي. يمكن معاينة الصفحة الإفتراضية عن طريق الدخول على اي متصفح من اي جهاز متصل بنفس شبكة الـWiFi التي يتصل بها الـRaspberry وكتابة الـIP مع المنفذ 3838 . يمكنك معرفة رقم الـIPالخاص بك من خلال هذا الأمر 

```
hostname -I
```
انا مثلا عنوان الصفحة لدي كالتالي ```http://192.168.100.26:3838/``` 

سوف ترى صفحة فارغة مكتوب فيها فقط
```
Index of /
```

لعرض صفحة تجريبية, سوف نقوم بنسخ صفحة جاهزة Demo للسيرفر إلى ملفات السيرفر.

```
cd 
cd shiny-server/samples/ && cp -R sample-apps welcome.html /srv/shiny-server
```
الآن إذا دخلت على عنوان الصفحة سوف ترى الملفات التي قمنا بنسخها. قم بالضغط على ```welcome.html```.  يجدر بك أن ترى هذه الصحفة الآن

<img class ="rounded mx-auto d-block" src="/post/2019-02-24-شرح-كامل-لإعداد-shiny-server-على-rasperry-pi_files/You-Are-Alive.png" alt="" width="300px"/>

### حل مشكلة ملفات ```rmarkdown```
برامج ```shiny``` سوف تعمل بشكل جيد. لكن ملفات ```rmarkdown``` قد لا تعمل بسبب اختلاف نسخة ```pandoc``` والتي تعتمد عليها حزمة ```rmarkdown``` بشكل اساسي. الحل هو ان نقوم بتحميل احدث نسخة لـ```pandoc``` ونسخها في ملفات ```shiny-server``` . هذه الأوامر تقوم بهذه العملية 

```
sudo apt-get install pandoc pandoc-citeproc
sudo cp /usr/bin/pandoc /usr/bin/pandoc-citeproc /usr/local/shiny-server/ext/pandoc
```

## في الختام
اهنئك على صبرك معي في هذه التدوينة الطويلة. لكن ما انجزته هنا بإتباع التعليمات هو خلاصة تجربتي في إعداد Shiny Server والتي اخذت مني ثلاثة ايام من القراءة والبحث وإصلاح كثير من المشاكل التي واجهتني. اتمنى ان يكون الموضوع نال إعجابك ورضاك.

