---
author: Hussain
comments: true
date: 2017-12-21 23:29:53+00:00
layout: post
link: https://arabianAnalyst.com/2017/12/21/tadawul-web-scraping-with-r-ar/
slug: tadawul-web-scraping-with-r-ar
title: تقشير البيانات من موقع تداول بستخدام R
wordpress_id: 150
categories:
- دروس
---



















#### مقدمة


احد أهم الخطوات في تحليل البيانات هي الحصول على البيانات نفسها. صعوبة هذه الخطوة يعتمد على مدى توفر البيانات المراد تحليلها.لذلك اليوم راح اشرح لك طريقة “تقشير الويب” وهي احد طرق جمع البيانات. وبالتحديد راح احاول اجمع بيانات موقع تداول عن طريق استخدام لغة R وهي اللغة المفضلة عندي لمثل هذه المهمات.









#### هل تقشير الويب هي الطريقة الوحيدة؟


اكيد لا , في الاسواق العالمية والأمريكية بالخصوص يمكن تحميل بيانات اداء الأسهم أو المؤشر بدون اللجوء للبرمجة. موقع جوجل على سبيل المثال عنده صفحة خاصة لعرض بيانات الاسهم الامريكية ويمكن تحميلها كملف csv او ملف اكسل. كذلك موقع ياهو يوفر نفس الخدمة. للأسف, موقع تداول لا يوفر مثل هذه الخدمة ويمكن الحصول على بيانات الأسهم او المؤشر السعودي إما بتصفح الموقع أو بشراء ترخيص للنشر. لذلك قررت ان اكتب كود يستخرج البيانات من موقع تداول لتحليلها لاحقا.









#### بعض الأساسيات عن الويب


لكن قبل ما نبدأ مشوارنا مع تقشير الويب, لازم نعرف بعض الاشياء المهمة عن كيفية حصول المواقع نفسها على البيانات وكيف تعرضها على صفحاتها. في معظم الاحيان كل ما يتطلب منا هو ان نكتب موقع تداول في برنامج المتصفح عندنا والموقع يحمل الصفحة مع بيانات اليوم للمؤشر او السهم. ولكن ما يحصل بالضبط هو ان المتصفح يستخدم الرابط الذي كتبناه ويحوله مع التقيد ببروتوكول نقل النص الفائق (أو ما يعرف بـ إتش تي تي بي) إلى “طلب نقل نص فائق” ويرسله إلى خادم معين يستقبل مثل هذه الطلبات. الخادم يستجيب بدوره للطلب ويرسل المعلومات المناسبة.









#### خلينا ناخذ مثال


إذا شعرت ان الموضوع بدأ يتعقد انا راح اعطيك مثال مع ياهو

[https://uk.finance.yahoo.com/quote/AAPL/history?period1=1511211600&period2=1513803600&interval=1d&filter=history&frequency=1d](https://uk.finance.yahoo.com/quote/AAPL/history?period1=1511211600&period2=1513803600&interval=1d&filter=history&frequency=1d)

هذا الرابط لموقع ياهو يعرض لنا أخر ٣٠ يوم من تداولات شركة آبل. لو امعنت النظر, راح تشوف ان هذا الرابط يحتوي على خمس متغيرات بالإضافة إلى رمز شركة آبل APPL الظاهر في الرابط. الأول هو period1 وقيمته 1511211600. المتغير الثاني هو period2 وقيمته 1513803600. هذان المتغيران هما من يحدد النافذة الزمنية للبيانات التي نريد جمعها. كما تلاحظ، هذه الارقام لا تشبه اي تاريخ فما الحكاية هنا. الجدير بالذكر هو ان موقع ياهو يستخدم نظام توقيت يونكس وهو ببساطة نظام يمثل الوقت والتاريخ بعدد الثواني المنقضية منذ الواحد من يناير سنة ١٩٧٠. هذه الطريقة تسهل على الكمبيوترات التعامل الوقت وإجراء العمليات الحسابية عليه. المتغير الثالث هو interval وقيمته 1d اي اعطيني البيانات بالأيام وليس بالأسابيع على سبيل المثال. المتغيران الآخيران هم filter و frequency سوف نغض النظر عنهم لأنهم غير مهمين بالنسبة لنا الآن.

الأن بإمكاننا ان نغير قيم هذه المتغيرات ونحصل على اي نوع من البيانات الي نبغاها بدون ما نتصفح موقع ياهو يدويا. خلينا نجرب نطلب بيانات تداولات سهم شركة مايكروسوفت للشهرين المنصرمين. متغيراتنا راح تكون كالتالي



 	
  * الأول : 1508533200

 	
  * الثاني : 1513803600

 	
  * الثالث : لا تغيير

 	
  * الرابع : لا تغيير

 	
  * الخامس : لا تغيير


الرابط راح يكون بشكل كالتالي

[https://uk.finance.yahoo.com/quote/MSFT/history?period1=1508533200&period2=1513803600&interval=1d&filter=history&frequency=1d](https://uk.finance.yahoo.com/quote/MSFT/history?period1=1508533200&period2=1513803600&interval=1d&filter=history&frequency=1d)

طبعا لاحظ أننا قمنا بتغيير رمز الشركة إلى MSFT في الرابط









#### طلبات خلف الكواليس


ماتعلمنا إلى الآن كافي لإستخراج بيانات موقع تداول القديم. لكن تداول حدثت موقعها في ١٥ مايو ٢٠١٦ . الموقع الجديد يستخدم تقنية الأجاكس AJAX . لعلك لاحظت عند تصفحك بعض المواقع ان بعضها يعيد تحميل الصفحة بكاملها وبعضها الآخر يحدث جزء من الصفحة فقط. المواقع الي تحدث جزء من الصفحة تستخدم تقنية الأجاكس. الأجاكس بختصار هي تقنية تستخدمها بعض المواقع لتتمكن من تكوين طلبات http خلف الكواليس بدون التأثير على المحتويات الاخرى في الصفحة. في موقع تداول وصفحة اداء المؤشر على سبيل المثال, راح تلاحظ ان الصفحة تعرض اولا ثم جدول البيانات. وإذا غيرت تواريخ الأداء , يتم تحديث الجدول فقط وليس كل الصفحة. هذا ايضا يعني ان الرابط لا يتم تحديثه وبالتالي لا يمكننا الاعتماد عليه. للحصول على روابط الطلبات “الخفية” انصحك ان تستخدم فايرفوكس وتثبيت إضافة اسمها httpFox فعّل الإضافة واذهب إلى التالي

أدوات -> مطوريّ الويب -> httpfox ثم open in own window

يفترض ان تفتح لك هذه النافذة

[![](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot1-300x142.png)](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot1.png)

الآن افتح موقع تداول وروح إلى صفحة المؤشر راح تشوف ان البرنامج بدأ برصد جميع الطلبات الي صارت خلف الكواليس كما هو في الصورة ادناه.

[![](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot2-300x138.png)](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot2.png)

هذه الطلبات هي الي بنستخدمها لكتابة الكود. رابط تداول لبيانات شركة معينة يحتوي على ٧٠ متغير. لحسن الحظ, كل ما يمهنا هو اربع متغيرات فقط وهم رمز الشركة والفترة (من تاريخ إلى تاريخ معين) و رقم الصفحة. موقع تداول يعرض فقط ثلاثين سجل لكل طلب. لذلك نحتاج رقم الصفحة لتصفح وجمع كل البيانات الي طلبناها. لو ننسخ الطلب بدون اي تعديل راح نحصل على البيانات وحدها لكن بتنسيق غريب. هذا التنسيق هو ما يعرف بالجيسون JSON. هذا التنسيق في انتشار كبير حاليا وتستخدمه مواقع كبيرة مثل الفيسبوك والتويتر. لحسن الحظ هناك حزمة في لغة الـ R راح تساعدنا كثير في قراءة البيانات من الملفات بتنسيق الجيسون. الرابط سيكون بالشكل التالي

    
    <code class="hljs">https://www.tadawul.com.sa/wps/portal/tadawul/markets/equities/indices/today/!ut/p/z1/rZFNTwIxEIZ_C4cepbOwgnprTFhrViIRcO1lU7pVavqxaQuL_97CzUSXGJ3bZJ4nM28GM1xhZvlevfGonOU69S9sUo9Gt1fZdQ4llNMMyKQAunjIx8UM8HMfAEWG2a_8gs6nQBbkbj1bL5M__psP-Xmf9SLzrB84RfwKfJOhFzgeeQLghyKA78_lSI_axtjeIEDQdd1QbcxQOIPgYLQNCFrvGgQNjzx-tBKBcDZKGxF4GdzOC3khnNZSHP8eMIt8Q20jD7h6lP7VecOtkP-9RGy5j3XkQdVi530i65CGzuNqSZ4obs1qVYGi75d6X5LB4BP_kbdL/p0/IZ7_NHLCH082KGN530A68FC4AN2OM2=CZ6_22C81940L0L710A6G0IQM43GF0=N/?draw=<span class="hljs-number">4</span>&columns;%5B0%5D%5Bdata%5D=date&columns;%5B0%5D%5Bname%</code>


الرابط اطول من كذا لكن اختصرته لأعطيك فكرة فقط.









#### كتابة الكود


حان الوقت اننا نضع كل شي تعلمناه في كتابة الكود. أول شي راح نعمله هو كتابة دالة تأخذ المتغيرات الي ذكرناها بالإضافة إلى متغير نوع الرابط ومخرجاتها رابط يمكننا استخدامه لإستخراج بيانات المؤشر أو شركة بعينها.

    
    <code class="hljs">parseURL <- <span class="hljs-keyword">function</span>(p,fromDate, toDate, comSymbol = <span class="hljs-literal">NULL</span> , type, adjustment =<span class="hljs-string">"no"</span>) {
      <span class="hljs-keyword">if</span> (type == <span class="hljs-string">"company"</span>) {
        firstJunk <-<span class="hljs-string">"https://www.tadawul.com.sa/wps/portal/tadawul/market-participants/issuers/issuers-directory/company-details/!ut/p/z1/pdDJDoIwFAXQr2HdSwFFdwgKlSHBAbUbUzUqCdMCNfr1FnVj4hDj2zU5973cEk7mhBfimO5EnZaFyOR7wVvLyAtsDyb13f5Eg9VyQnsUMQqAzG6AUttUOzoCBG1VAhcsDnUNsUb4T3mXRW1YseUlg0RSk_6Xh_4tPyR8l5Wre9V9XVddBQpqsRGnQ6aA8HWZV6I4j8_5qpRIhdoc5c974VFH7u37vmMbFD3jAT79yzN4UfwjaJrdAN6MBVLl0-n8EmzHLGVXY0T5Tw!!/p0/IZ7_NHLCH082KGET30A6DMCRNI2086=CZ6_NHLCH082KGET30A6DMCRNI2000=NJhistoricalPerformance=/?draw=2&columns%5B0%5D%5Bdata%5D=transactionDate&columns%5B0%5D%5Bname%5D=&columns%5B0%5D%5Bsearchable%5D=true&columns%5B0%5D%5Borderable%5D=true&columns%5B0%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B0%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B1%5D%5Bdata%5D=todaysOpen&columns%5B1%5D%5Bname%5D=&columns%5B1%5D%5Bsearchable%5D=true&columns%5B1%5D%5Borderable%5D=true&columns%5B1%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B1%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B2%5D%5Bdata%5D=highPrice&columns%5B2%5D%5Bname%5D=&columns%5B2%5D%5Bsearchable%5D=true&columns%5B2%5D%5Borderable%5D=true&columns%5B2%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B2%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B3%5D%5Bdata%5D=lowPrice&columns%5B3%5D%5Bname%5D=&columns%5B3%5D%5Bsearchable%5D=true&columns%5B3%5D%5Borderable%5D=true&columns%5B3%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B3%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B4%5D%5Bdata%5D=previousClosePrice&columns%5B4%5D%5Bname%5D=&columns%5B4%5D%5Bsearchable%5D=true&columns%5B4%5D%5Borderable%5D=true&columns%5B4%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B4%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B5%5D%5Bdata%5D=change&columns%5B5%5D%5Bname%5D=&columns%5B5%5D%5Bsearchable%5D=true&columns%5B5%5D%5Borderable%5D=true&columns%5B5%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B5%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B6%5D%5Bdata%5D=changePercent&columns%5B6%5D%5Bname%5D=&columns%5B6%5D%5Bsearchable%5D=true&columns%5B6%5D%5Borderable%5D=true&columns%5B6%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B6%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B7%5D%5Bdata%5D=volumeTraded&columns%5B7%5D%5Bname%5D=&columns%5B7%5D%5Bsearchable%5D=true&columns%5B7%5D%5Borderable%5D=true&columns%5B7%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B7%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B8%5D%5Bdata%5D=turnOver&columns%5B8%5D%5Bname%5D=&columns%5B8%5D%5Bsearchable%5D=true&columns%5B8%5D%5Borderable%5D=true&columns%5B8%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B8%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B9%5D%5Bdata%5D=noOfTrades&columns%5B9%5D%5Bname%5D=&columns%5B9%5D%5Bsearchable%5D=true&columns%5B9%5D%5Borderable%5D=true&columns%5B9%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B9%5D%5Bsearch%5D%5Bregex%5D=false&start="</span>
        <span class="hljs-keyword">return</span> (
          paste(
            firstJunk,p,<span class="hljs-string">"&length=30&search%5Bvalue%5D=&search%5Bregex%5D=false&isNonAdjusted=0&startDate="</span>, fromDate, <span class="hljs-string">"&toDate="</span> ,toDate,<span class="hljs-string">"&symbol="</span>,comSymbol,<span class="hljs-string">"&_=1463986258725"</span>,sep = <span class="hljs-string">""</span>
          )
        )
      }
      <span class="hljs-keyword">if</span> (type == <span class="hljs-string">"index"</span>) {
        firstJunk <- <span class="hljs-string">"https://www.tadawul.com.sa/wps/portal/tadawul/markets/equities/indices/today/!ut/p/z1/rZFNTwIxEIZ_C4cepbOwgnprTFhrViIRcO1lU7pVavqxaQuL_97CzUSXGJ3bZJ4nM28GM1xhZvlevfGonOU69S9sUo9Gt1fZdQ4llNMMyKQAunjIx8UM8HMfAEWG2a_8gs6nQBbkbj1bL5M__psP-Xmf9SLzrB84RfwKfJOhFzgeeQLghyKA78_lSI_axtjeIEDQdd1QbcxQOIPgYLQNCFrvGgQNjzx-tBKBcDZKGxF4GdzOC3khnNZSHP8eMIt8Q20jD7h6lP7VecOtkP-9RGy5j3XkQdVi530i65CGzuNqSZ4obs1qVYGi75d6X5LB4BP_kbdL/p0/IZ7_NHLCH082KGN530A68FC4AN2OM2=CZ6_22C81940L0L710A6G0IQM43GF0=N/?draw=25&columns%5B0%5D%5Bdata%5D=date&columns%5B0%5D%5Bname%5D=&columns%5B0%5D%5Bsearchable%5D=true&columns%5B0%5D%5Borderable%5D=true&columns%5B0%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B0%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B1%5D%5Bdata%5D=open&columns%5B1%5D%5Bname%5D=&columns%5B1%5D%5Bsearchable%5D=true&columns%5B1%5D%5Borderable%5D=true&columns%5B1%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B1%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B2%5D%5Bdata%5D=high&columns%5B2%5D%5Bname%5D=&columns%5B2%5D%5Bsearchable%5D=true&columns%5B2%5D%5Borderable%5D=true&columns%5B2%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B2%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B3%5D%5Bdata%5D=low&columns%5B3%5D%5Bname%5D=&columns%5B3%5D%5Bsearchable%5D=true&columns%5B3%5D%5Borderable%5D=true&columns%5B3%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B3%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B4%5D%5Bdata%5D=close&columns%5B4%5D%5Bname%5D=&columns%5B4%5D%5Bsearchable%5D=true&columns%5B4%5D%5Borderable%5D=true&columns%5B4%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B4%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B5%5D%5Bdata%5D=totalVolume&columns%5B5%5D%5Bname%5D=&columns%5B5%5D%5Bsearchable%5D=true&columns%5B5%5D%5Borderable%5D=true&columns%5B5%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B5%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B6%5D%5Bdata%5D=totalTurnover&columns%5B6%5D%5Bname%5D=&columns%5B6%5D%5Bsearchable%5D=true&columns%5B6%5D%5Borderable%5D=true&columns%5B6%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B6%5D%5Bsearch%5D%5Bregex%5D=false&columns%5B7%5D%5Bdata%5D=noOfTrades&columns%5B7%5D%5Bname%5D=&columns%5B7%5D%5Bsearchable%5D=true&columns%5B7%5D%5Borderable%5D=true&columns%5B7%5D%5Bsearch%5D%5Bvalue%5D=&columns%5B7%5D%5Bsearch%5D%5Bregex%5D=false&order%5B0%5D%5Bcolumn%5D=0&order%5B0%5D%5Bdir%5D=desc&start="</span>
        fromDate <- strptime(fromDate, format = <span class="hljs-string">"%Y-%m-%d"</span>) ; toDate <- strptime(toDate, format = <span class="hljs-string">"%Y-%m-%d"</span>)
        
        fromY <- format.Date(fromDate, <span class="hljs-string">"%Y"</span>) ; toY <- format.Date(toDate, <span class="hljs-string">"%Y"</span>)
        fromM <- format.Date(fromDate, <span class="hljs-string">"%m"</span>) ; toM <- format.Date(toDate, <span class="hljs-string">"%m"</span>)
        fromD <- format.Date(fromDate, <span class="hljs-string">"%d"</span>) ; toD <- format.Date(toDate, <span class="hljs-string">"%d"</span>)
        
        <span class="hljs-keyword">return</span> (
          paste(
            firstJunk,p,<span class="hljs-string">"&length=10&search%5Bvalue%5D=&search%5Bregex%5D=false&sourceCallerId=datePicker&dateParameter="</span>, fromY,<span class="hljs-string">"%2F"</span>,fromM,<span class="hljs-string">"%2F"</span>,fromD,<span class="hljs-string">"+-+"</span>,toY,<span class="hljs-string">"%2F"</span>,toM,<span class="hljs-string">"%2F"</span>,toD,<span class="hljs-string">"&typeOfCall="</span>, ifelse(adjustment ==<span class="hljs-string">"no"</span>,  <span class="hljs-string">"adjustedType"</span>,<span class="hljs-string">"nonAdjustedType&old_tasi_current_sector=TASI"</span>),sep = <span class="hljs-string">""</span>
          )
        )
      }
      
    }</code>


ممتاز!! الآن نحتاج دالة خاصة بإستخراج بيانات المؤشر وتنسيقها في إيطار بيانات. لكن قبل ما نبدأ بكتابة هذه الدالة, لازم نحّمل ونرفق حزمة الـ rjson هذه الحزمة مهم لإنها توفر دالة خاصة لقراءة تنسيق الجيسون. ضع في عين الاعتبار انك تحتاج تحمل الحزمة مرة واحدة فقط ولكنك تحتاج ترفقها لكل جلسة جديد في لغة الـ R هذه الدالة سوف تأخذ متغيرين فقط وهما بدء وانتهاء الفترة التاريخية للمؤشر. الجدير بالذكر, ان المؤشر تم إعادة هيكلته في عام ٢٠٠٨ شهر ابريل. البيانات القديمة له رابط مختلف قليلا عن البيانات الجديدة. لذلك قمت بكتابة دالة اضافية لتعديل ودمج البيانات عند الضرورة.

    
    <code class="hljs"><span class="hljs-comment">#install.packages("rjson")  # only if not installed already </span>
    <span class="hljs-keyword">library</span>(<span class="hljs-string">"rjson"</span>)
    <span class="hljs-comment">#Private FUNCTION: to get the index</span>
    privateGetIndex <-<span class="hljs-keyword">function</span>(startDate, endDate, adjustPeriod){
      nRecords <-  fromJSON(file= parseURL(<span class="hljs-number">0</span>, startDate, endDate, type = <span class="hljs-string">"index"</span>, adjustment = adjustPeriod))$recordsFiltered
      ifelse(nRecords <= <span class="hljs-number">10</span>, nPages <- <span class="hljs-number">1</span>, nPages <- ceiling(nRecords/<span class="hljs-number">10</span>)) 
      
      fullData <- data.frame(stringsAsFactors = <span class="hljs-literal">FALSE</span>)
      <span class="hljs-keyword">for</span> (i <span class="hljs-keyword">in</span> <span class="hljs-number">0</span>:nPages ) {
        jsonData <- fromJSON(file = parseURL((i*<span class="hljs-number">10</span>), startDate, endDate, type = <span class="hljs-string">"index"</span>, adjustment = adjustPeriod))
        p.table <- t(sapply(jsonData$data, <span class="hljs-keyword">function</span>(x) unlist(x)))
        fullData <- rbind(fullData, as.data.frame(p.table, stringsAsFactors = <span class="hljs-literal">FALSE</span>))
      }
      <span class="hljs-comment">#Formating the table</span>
      fullData$date <- strptime(fullData$date, format = <span class="hljs-string">"%Y/%m/%d"</span>)
      fullData$high <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$high))
      fullData$open <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$open))
      fullData$low <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$low) )
      fullData$close <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$close))
      fullData$noOfTrades <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$noOfTrades))
      fullData$totalVolume <- as.numeric(gsub(<span class="hljs-string">","</span>,<span class="hljs-string">""</span>, fullData$totalVolume))
      <span class="hljs-keyword">return</span> (fullData[nRecords:<span class="hljs-number">1</span>,])
    }</code>


رائع, الآن كل ما علينا هو ان نكتب دالة خاصة لإستخراج بيانات الشركات. هذه الدالة راح تأخذ متغير الفترة (البدء والإنتهاء) ورمز الشركة ومخرجاتها بيانات الشركة للفترة المحددة وتنسيقها في إيطار بيانات.

    
    <code class="hljs"><span class="hljs-comment">#FUNCTION: get all records of a specified company for a specified period. </span>
    getCompanyRecords <- <span class="hljs-keyword">function</span> (startDate, endDate, companySymbol){
      nRecords <-  fromJSON(file= parseURL(<span class="hljs-number">0</span>, startDate, endDate, companySymbol, type = <span class="hljs-string">"company"</span>))$recordsFiltered
      ifelse(nRecords <= <span class="hljs-number">30</span>, nPages <- <span class="hljs-number">1</span>, nPages <- ceiling(nRecords/<span class="hljs-number">30</span>))
      
      fullData <- data.frame(stringsAsFactors = <span class="hljs-literal">FALSE</span>)
      <span class="hljs-keyword">for</span> (i <span class="hljs-keyword">in</span> <span class="hljs-number">0</span>:nPages ) {
        jsonData <- fromJSON(file = parseURL((i*<span class="hljs-number">30</span>), startDate, endDate, companySymbol, type = <span class="hljs-string">"company"</span>))
        p.table <- t(sapply(jsonData$data, <span class="hljs-keyword">function</span>(x) unlist(x)))
        fullData <- rbind(fullData, as.data.frame(p.table, stringsAsFactors = <span class="hljs-literal">FALSE</span>))
      }
      
      <span class="hljs-comment">#Formating the table</span>
      fullData$transactionDate <- strptime(fullData$transactionDate, format = <span class="hljs-string">"%b %e, %Y"</span>)
      fullData$previousClosePrice <- as.numeric(fullData$previousClosePrice)
      fullData$todaysOpen <- as.numeric(fullData$todaysOpen)
      fullData$highPrice <- as.numeric(fullData$highPrice)
      fullData$lowPrice <- as.numeric(fullData$lowPrice)
      fullData$volumeTraded <- as.numeric(fullData$volumeTraded)
      fullData$turnOver <- as.numeric(fullData$turnOver)
      fullData$noOfTrades <- as.numeric(fullData$noOfTrades)
      fullData$lastTradePrice <- as.numeric(fullData$lastTradePrice)
      fullData$change <- as.numeric(fullData$change)
      fullData$changePercent <- as.numeric(fullData$changePercent)
      
      <span class="hljs-keyword">return</span> (fullData[nRecords:<span class="hljs-number">1</span>,])
    }</code>


ممتاز، بما أننا كتبنا دالة لإستخراج بيانات الشركات ، الا يجدر بنا ايضا كتابة دالة متخصص لإستخراج بيانات المؤشر السعودي. الكود سيكون كالتالي

    
    <code class="hljs"><span class="hljs-comment">#FUNCTION: get all records of TASI for a specified period. </span>
    getIndexRecords <- <span class="hljs-keyword">function</span>(fromDate, toDate){
      
      cutoffDate <- strptime(<span class="hljs-string">"2008-04-02"</span>  , format = <span class="hljs-string">"%Y-%m-%d"</span>)
      stY<-strptime(fromDate, format = <span class="hljs-string">"%Y-%m-%d"</span>)
      endY<-strptime(toDate, format = <span class="hljs-string">"%Y-%m-%d"</span>)
      
      <span class="hljs-keyword">if</span>(stY<=cutoffDate & endY <= cutoffDate) {
        period <- <span class="hljs-string">"AllBeforeRestructure"</span>
        <span class="hljs-keyword">return</span>(privateGetIndex(startDate = fromDate, endDate = toDate, adjustPeriod = <span class="hljs-string">"yes"</span>))
      }
      <span class="hljs-keyword">if</span>((stY<=cutoffDate & endY >cutoffDate)){
        period <- <span class="hljs-string">"Combination"</span>
        oldPart<- privateGetIndex(startDate = fromDate, endDate = <span class="hljs-string">"2008-04-02"</span>, adjustPeriod = <span class="hljs-string">"yes"</span>)
        newPart<- privateGetIndex(startDate = <span class="hljs-string">"2008-04-05"</span>, endDate = toDate, adjustPeriod = <span class="hljs-string">"no"</span>)
        oldPart$close * <span class="hljs-number">0.9801111</span>;
        <span class="hljs-keyword">return</span>(rbind(oldPart,newPart)); } 
      <span class="hljs-keyword">if</span>((stY >cutoffDate)){
        period <- <span class="hljs-string">"NewIndex"</span>
        <span class="hljs-keyword">return</span>(privateGetIndex(startDate = fromDate, endDate = toDate, adjustPeriod = <span class="hljs-string">"no"</span>))
      }
    }</code>










#### تجربة و أختبار الكود (مبايعة الأمير محمد بن سلمان وليا للعهد)


إلى هنا, عملنا انتهى مع كتابة الكود. الأن احنا جاهزين لإختبار الكود والإجابة عن بعض التساؤلات حول السوق. خلينا نحاول نستخرج البيانات الخاصة بالمؤشر خلال هذه السنة المليئة بالأحداث. الهدف من استخراج هذه البيانات هو عرض ما حدث في شهر يونيو ٢٠١٧ عندما تم مبايعة الأمير محمد بن سلمان وليا للعهد من خلال رسم بياني. لاحظ ان الكود قد يستغرق بعض الوقت. لذلك انصحك ترتاح و تحتسي لك كوب من الشاهي وتروّق. بعد ما يخلص الكود, راح يكون عندنا كل البيانات محفوظة في المتغير TASI . لتتأكد اننا حصلنا على البيانات المطلوبة خلينا نشغل هذا الكود

    
    <code class="hljs">TASI <- getIndexRecords(<span class="hljs-string">"2017-01-01"</span>, <span class="hljs-string">"2017-12-01"</span>)
    head(TASI)</code>



    
    <code class="hljs">##           date    high    open     low   close totalVolume
    ## 229 2017-01-01 7250.19 7210.43 7207.65 7237.95   257557959
    ## 228 2017-01-02 7290.33 7237.95 7224.77 7247.34   317860636
    ## 227 2017-01-03 7260.37 7247.34 7209.51 7250.76   287349412
    ## 226 2017-01-04 7254.61 7250.76 7182.79 7198.11   254165677
    ## 225 2017-01-05 7224.50 7198.11 7176.32 7198.73   232230440
    ## 224 2017-01-08 7212.04 7198.73 7130.25 7138.86   202674809
    ##        totalTurnover noOfTrades
    ## 229 4,460,586,641.65     113416
    ## 228 5,841,410,563.75     147723
    ## 227 5,151,905,839.90     132104
    ## 226 5,031,016,273.05     138147
    ## 225 4,460,386,480.90     120440
    ## 224 3,900,807,491.20     106546</code>


ممتاز, يبدو ان كل شي تمام. الآن راح نعمل رسم بياني ونأخذ فكرة عن ردة فعل السوق تجاه الخبر.

    
    <code class="hljs">at.tick <- format(TASI$date, <span class="hljs-string">"%d"</span>) %<span class="hljs-keyword">in</span>% c(<span class="hljs-string">"15"</span>)
    maxV<-dim(TASI)[<span class="hljs-number">1</span>]
    plot(x=as.Date(TASI$date), y=TASI$close, type = <span class="hljs-string">"l"</span>, xaxt=<span class="hljs-string">"n"</span>, xlab = <span class="hljs-string">"Time"</span>, ylab = <span class="hljs-string">"Index"</span>)
    axis(side = <span class="hljs-number">1</span>, at = as.Date(TASI$date[at.tick]), labels = format(TASI$date[at.tick], <span class="hljs-string">"%b-%y"</span>))
    text(x=as.Date(<span class="hljs-string">"2017-08-25"</span>), y =<span class="hljs-number">7480</span> , labels = <span class="hljs-string">"Market Jump"</span>, cex = <span class="hljs-number">1</span>)</code>




[![](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot3-300x214.png)](http://arabianAnalyst.com/wp-content/uploads/2017/12/TadawulPlot3.png)

الإرتفاع واضح تمام حيث ان الموشر وصل إلى ما فوق الـ ٧٤٠٠ نقطة بعد ما كان ما دون ٦٧٠٠ نقطة. وبذلك نختم موضوعنا اليوم واتمنى انك استمتعت بقراءته. ولكن الأهم هو انك تعلمت ولو القليل عن طريقة تقشير الويب. لا تتردد بنسخ الكود وتجريب أو حتى تطويره كما تشاء. إذا عندك اي ملاحظات او اقتراحات ارجو انك ترسلها عبر قسم التعليقات و شكراً.











