---
author: Hussain
comments: true
date: 2017-11-18 15:56:57+00:00
layout: post
link: https://arabianAnalyst.com/2017/11/18/visualizing-twitter-followers-growth-ar/
slug: visualizing-twitter-followers-growth-ar
title: تحليل حركة نمو المتابعين في تويتر
wordpress_id: 76
categories:
- دروس
---








حساب المدونة في تويتر تم انشاءه منذ فترة وجيزة جداً. لكن في يوم ٢٥ يونيو ٢٠١٦ ٫حدث ارتفاع غير معتاد في عدد المتابعين لحساب التويتر الخاص بالمدونة. ظللت اتلقى الكثير من الإشعارات بالبريد الإلكتروني لكل متابع جديد. الفكرة الأول التي كانت لدي هي ان اسرع إلى اعدادت تويتر وآقوم بتعطيل خاصية الإشعارات. لكن بعد حين استوعبت ان هذه الرسائل مليئة بالبيانات المهمة والتي بالإمكان تحليلها.

هناك الكثير من الأدوات لتحليل وتصوير حركة المتابعين في تويتر بما في ذلك أداة تويتر التحليلية الخاصة بتويتر نفسه [Twitter Analytics](https://analytics.twitter.com/) . إذا كنت جديد العهد مع R، فإنني اوصيك باستخدام هذه الأداة السهلة. أما إن كنت من محبي لغة R تابع القراءة إلى نهاية المقال لمعرفة كيف يمكنك تحليل حركة المتابعين عن طريق الـ R.





#### Twitter Analytics


هي أداة قوية تم تصميمها بواسطة تويتر لتحليل معلومات وبيانات تويتر. بالإضافة إلى ذلك، فإن هذه الاداة توفر الكثير من المعلومات المثيرة للاهتمام مثل (عدد الأشخاص الذين شاهدوا التغريدات الخاصة بك)، ومستوى المشاركة (النقرات، والردود والإعجاب) وأكثر من ذلك بكثير. حتى لو كنت مهووس بـ R، انصح حقا بتجريب هذه الاداة. الجدير بالذكر هو انه بإمكانك تنشيطها واستخدامها بكل سهولة ويسر. اذهب إلى [Twitter Analytics](https://analytics.twitter.com/) و قم بتنشيطها. سترى في الجزء العلوي من الشاشة عدة علامات تبويب كالرئيسية، والتغريدات، والجماهير، والأحداث. كل علامة تبويب تعطيك معلومات مثيرة للاهتمام حول حسابك.









#### طريقة الـ R


بالرغم من أن اداة تويتر لطيفة وسهلة، لكن كوني من محبي لغة الـ R فقد قررت اقوم بجمع وتحليل كل رسائل الإشعارات وتحليلها. أول شيء سوف نحتاج إلى القيام به هو الحصول على جميع رسائل البريد الإلكتروني من البريد الوارد و تحميلها في جلسة R. بعد ذلك، سوف نقوم بفرز جميع رسائل البريد الإلكتروني غير ذات الصلة ونبقي رسائل الإشعارات فقط. الخطوة الأخيرة هي إنشاء رسم بياني يبين الجدول الزمني الخاص بنمو المتابعين على حسابنا في تويتر.

قد يبدو هذا معقد قليلا لكن لا تقلق. أحد الأمور التي أحبها حول R هي أن R لديه مجتمع ضخم و متحمس يقوم بإستمرار بتصميم حزم لفعل أي شيء في R. لحسن الحظ، هناك حزمة تسمى “edeR”. هذه الحزمة متخصصة في استخراج المعلومات الرأسية لكل الرسائل. للإسف هذه الحزمة لا تعمل إلا مع حسابات Gmail. لذلك، إذا كان لديك حساب بريد إلكتروني مع مزود آخر، هذه الحزمة لن تجديك نفعا.









#### القسم الأول : إعداد حساب Gmail الخاص بك


قبل أن نبدأ، هناك قليل من الأشياء التي تحتاج إلى القيام بها على حساب جوجل الخاص بك لكي تعمل الحزمة بشكل سليم. أولا عليك أن تذهب إلى إعدادات Gmail وتقوم بتمكين IMAP (بروتوكول الوصول إلى الرسائل عبر الإنترنت). للقيام بذلك اتبع الخطوات التالية



 	
  1. تسجيل الدخول حساب Gmail الخاص بك

 	
  2. انتقل إلى إعدادات عن طريق النقر على “رمز الترس” ثم حدد “إعدادات” من القائمة المنسدلة

 	
  3. انقر على “إعادة التوجيه و POP / IMAP” علامة التبويب

 	
  4. تمكين IMAP من قسم “IMAP الوصول”


بما أن جوجل مجنونة جدا حول الأمن، هناك خطوة أخيرة عليك القيام بها. قم بتمكين “الوصول إلى تطبيقات أقل أمنا” من خلال هذه [الصفحة](https://www.google.com/settings/security/lesssecureapps).

_تحذير: هذا قد يجعل حسابك أكثر عرضة لهجمات قراصنة الإنترنت. ولذلك، فإنني أوصي بشدة لتعطيله مرة أخرى بعد الانتهاء مع التحليل._









#### القسم الثاني: تحليل البيانات







##### استخراج البيانات


والآن بعد أن اصبح حساب Gmail لدينا جاهز. دعنا نمضي في كتابة بعض الكودات الرهيبة. سنبدأ اولا بتثبيت الحزمة وإرفاقها. الكود هو كالتالي:

    
    <code class="hljs">install.packages(<span class="hljs-string">"edeR"</span>)</code>



    
    <code class="hljs"><span class="hljs-keyword">library</span>(<span class="hljs-string">"edeR"</span>)</code>


انيا، سوف نستخدم الدالة ()extractKeywordB. هذه الدالة سوف تمكننا من استخراج كافة رسائل البريد الإلكتروني استنادا إلى الكلمة محددة في نص الرسالة. من اجل ذلك، فهو من المهم أن نختار كلمة فريدة ومذكورة فقط في رسائل الإشعارات. من اجل تبسيط مهمتتنا سوف اقوم بختيار كلمة “follower”. هنا ايضا بعض القيم نحتاج إلى تزويدها في الدالة.



 	
  * user: يمكنك وضع عنوان البريد الإلكتروني بالكامل هنا.

 	
  * password: يمكنك كتابة كلمة المرور الخاصة بك هنا.

 	
  * Kw: يمكنك تحديد الكلمة التي تريد استخدامها هنا.

 	
  * nmail: يمكنك إما تحديد عدد من رسائل البريد الإلكتروني التي ترغب في استرداد أو ضع -1 للحصول على جميع رسائل البريد الإلكتروني.



    
    <code class="hljs">db <- extractKeywordB(username = <span class="hljs-string">"arabianAnalyst@gmail.com"</span>, password = <span class="hljs-string">"XXXXXXXXX"</span>, kw = <span class="hljs-string">"followers"</span>, nmail = -<span class="hljs-number">1</span>)</code>


ملاحظة جانبية: هذه العملية قد تستغرق بعض الوقت اعتمادا على عدد من رسائل البريد الإلكتروني لديك.

هذه الدالة سوف تقوم بإرجاع قائمة ذو متغيرين. المتغير الأول يحوي عدد الرسائل التي نجحت الدالة من استخراجها. والمتغير الثاني يتحوي على البيانات. وبما أننا مهتمون فقط في البيانات، سوف نقوم بإنشاء متغير لتخزين البيانات بشكل منفصل.

    
    <code class="hljs">dos <- db$data</code>













#### تجهيز وتنسيق البيانات


حتى الآن ما قمنا به هو عمل رائع. كل ما علينا القيام به الآن هو تحويل عمود التواريخ إلى صيغة يتعرف عليها R، ثم سنكون على استعداد تام لرسم نتائجنا. للقيام بذلك، سوف نقوم بكاتبة هذا الكود اللطيف.

    
    <code class="hljs">dataDates <- as.POSIXlt(dbs$email_date, format = <span class="hljs-string">"%a, %d %b %Y %H:%M:%S %z"</span>, tz= Sys.timezone())
    dataDates <- sort(dataDates)</code>


لاحظ أنني أيضا رتبت التواريخ لضمان أن تكون مبنية على الترتيب الزمني.









#### التصوير البياني


دعنا الآن نرى مدى ارتفاع القفزة في عدد المتابعين

    
    <code class="hljs">plot(dataDates, <span class="hljs-number">1</span>:length(dataDates), xlab = <span class="hljs-string">""</span>, ylab= <span class="hljs-string">"followers"</span> , col = <span class="hljs-string">"darkblue"</span>, type = <span class="hljs-string">"l"</span>, main = <span class="hljs-string">"Followers Growth"</span>)</code>


[![](http://arabianAnalyst.com/wp-content/uploads/2017/11/layeredmultichart-300x180.png)](http://arabianAnalyst.com/wp-content/uploads/2017/11/layeredmultichart.png)

يمكننا رؤية الازدياد في عدد المتابعين بتسارع أُسي خلال فترة 24 يونيو - 26 يونيو.









#### مزيد من التحقيقات


يمكننا التوقف هنا وهذا كافي لرؤية متى بدأت القفزة الغير اعتيادية. ولكن ماذا لو أردنا أن نعرف كيف حدث ذلك ومن كان السبب في حدوثه. في تويتر وبشكل عام٫ إذا حصلت تغريدتك على إعادة تغريد من قبل شخص ذو قاعدة متابعين كبيرة، حسابك يحصل على مزيد من االشهرة والدعاية. لكن هذا ليس دائما صحيح. أحيانا تحصل للدعاية من خلال الذكر في تغريدة شخص معين ذو متابعين كثر. لذلك، سنحاول تحليل كل من هما.

سوف أستخدم كودات مشابهة جدا لما ناقشناه سابقا. لقد قمت ايضا بتحسين الكود لجعله ابسط للقراءة

سوف نقوم بإستخراج رسائل البريد الإلكتروني حول الإشعارات التالية



 	
  * المتابعون

 	
  * الذكر

 	
  * إعادة تغريد


الكود سوف يكون كالتالي

    
    <code class="hljs"><span class="hljs-comment"># Extracting The email Information Based on Keywords</span>
    <span class="hljs-comment">#Based on keyword "Followers"</span>
    dbf <-
      extractKeywordB(
        username = <span class="hljs-string">"arabianAnalyst@gmail.com"</span>,
        password = <span class="hljs-string">"XXXXXXXXXXX"</span>,
        kw = <span class="hljs-string">"follower"</span>,
        nmail = -<span class="hljs-number">1</span>
      )
    <span class="hljs-comment">#Based on keyword "Mentioned"</span>
    dbm <-
      extractKeywordB(
        username = <span class="hljs-string">"arabianAnalyst@gmail.com"</span>,
        password = <span class="hljs-string">"XXXXXXXXXXX"</span>,
        kw = <span class="hljs-string">"mentioned"</span>,
        nmail = -<span class="hljs-number">1</span>
      )
    <span class="hljs-comment">#Based on keybord "Retweeted"</span>
    dbRT <-
      extractKeywordB(
        username = <span class="hljs-string">"arabianAnalyst@gmail.com"</span>,
        password = <span class="hljs-string">"XXXXXXXXXXX"</span>,
        kw = <span class="hljs-string">"retweeted"</span>,
        nmail = -<span class="hljs-number">1</span>
      )
    <span class="hljs-comment">#Extracting only the Email information from the list</span>
    dbFollow <- dbf$data dbMention <- dbm$data dbRetweet <- dbRT$data
    <span class="hljs-comment">#FUNCTION: Convertes Dates from String to POISXlt tpye and return them sorted.</span>
    dateConvertor <- <span class="hljs-keyword">function</span> (thedata) {
      <span class="hljs-comment">#Converting the Dates from String to POISXlt tpye.</span>
      dataDates <-
        as.POSIXlt(thedata$email_date, format = <span class="hljs-string">" %a, %d %b %Y %H:%M:%S %z"</span>, tz = Sys.timezone())
      <span class="hljs-comment">#Sorting the Data</span>
      dataDates <- sort(dataDates) <span class="hljs-keyword">return</span>(dataDates)
    }
    <span class="hljs-comment">#Storing The times of following, mentioning and retweeting in specific variables</span>
    followTime <- dateConvertor(dbFollow)
    retweetTime <- dateConvertor(dbRetweet)
    mentionTime <- dateConvertor(dbMention)</code>


للحصول على صورة اوضح حول البيانات سوف نلخصها في جدول قابل للقراءة. انا افضل استخدام دالة ()table. هذه الدالة عادة ما تعمل بشكل أفضل مع المتغيرات النوعية. ولذلك، فإنني سوف اقوم بجمع تواريخ الرسائل إلى ايام من الاسبوع

    
    <code class="hljs"><span class="hljs-comment">#creating a custom column to group dates by days. </span>
    days <- strftime(followTime, format = <span class="hljs-string">"%a, %d %b"</span>) 
    dbsExted <- cbind(dbFollow, days)</code>


لاحظ أنني قد اضفت عامود “days” لإطار البيانات باستخدام الدالة ()cbin. الآن بإمكاننا تلخيص المعلومات بشكل اوضح عبر الكود التالي

    
    <code class="hljs">table(dbsExted$days,dbsExted$email_from)</code>


[![](http://arabianAnalyst.com/wp-content/uploads/2017/11/Followers-Summary-300x229.png)](http://arabianAnalyst.com/wp-content/uploads/2017/11/Followers-Summary.png)

وكما نرى، كان هناك عدد كبير من المتابعين في 25 يونيو.









#### تحديد هوية المسوّق أو المروّج


ن الأشياء المفيدة من تلخيص البيانات هو الحصول على أدلة آو تلميحات حول الوجهة او الخطوة القادمة والمناسبة في التحليل. في الواقع، في مثالنا الحالي، هناك دليل قوي على أن هناك شيء يستحق التحقيق حوله في كل من يومي الاثنين 20 يونيو والسبت 25 يونيو.

إحدى الطرق المستخدمة في تقصي الاحداث الزمنية هي رسم كل الأحداث في رسم بياني كنقاط ذو طبقات متعدد. وبالإضافة إلى ذلك، يمكننا تلوين هذه النقاط لتعزيز الوضوح. الكود هو كالتالي

    
    <code class="hljs">plot(
      followTime,
      <span class="hljs-number">1</span>:length(followTime),
      xlab = <span class="hljs-string">""</span>,
      ylab = <span class="hljs-string">"followers"</span> ,
      col = <span class="hljs-string">"darkblue"</span>,
      type = <span class="hljs-string">"l"</span>,
      main = <span class="hljs-string">"Followers Growth"</span>
    ) points(mentionTime, rep(<span class="hljs-number">20</span>, length(mentionTime)), col = <span class="hljs-string">"darkgreen"</span>, pch = <span class="hljs-number">1</span>) points(retweetTime, rep(<span class="hljs-number">30</span>, length(retweetTime)), col = <span class="hljs-string">"darkred"</span>, pch = <span class="hljs-number">2</span>) legend(
      followTime[<span class="hljs-number">1</span>],
      length(followTime) * <span class="hljs-number">0.8</span>,
      c(<span class="hljs-string">"mentions"</span>, <span class="hljs-string">"retweets"</span>, <span class="hljs-string">"followers"</span>),
      pch = c(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-literal">NA</span>),
      lty = c(<span class="hljs-literal">NA</span>, <span class="hljs-literal">NA</span>, <span class="hljs-number">1</span>),
      col = c(<span class="hljs-string">"darkgreen"</span>, <span class="hljs-string">"darkred"</span>, <span class="hljs-string">"darkblue"</span>)
    )</code>


[![](http://arabianAnalyst.com/wp-content/uploads/2017/11/layeredmultichart-300x180.png)](http://arabianAnalyst.com/wp-content/uploads/2017/11/layeredmultichart.png)

في الرسم البياني أعلاه يظهر أن أول قفزة صغيرة في عدد المتابعين ربما كانت بسبب إعادة تغريدة. أما القفزة الثانية وهي الكبيرة فالسبب ليس واضحاً فيما إذا كانت بسبب ذكر من شخص معين او إعادة تغريدة. لذلك سوف نركز تحليلنا على هذه القفزة والتي تصادف ٢٥ يونيو.

    
    <code class="hljs">follow25th <-
      followTime[which(strftime(followTime, format = <span class="hljs-string">"%d"</span>) == <span class="hljs-string">"25"</span>)]
    mention25th <-
      mentionTime[which(strftime(mentionTime, format = <span class="hljs-string">"%d"</span>) == <span class="hljs-string">"25"</span>)]
    retweet25th <-
      retweetTime[which(strftime(retweetTime, format = <span class="hljs-string">"%d"</span>) == <span class="hljs-string">"25"</span>)]
    plot(
      retweet25th,
      rep(<span class="hljs-number">15</span>, length(retweet25th)),
      col = <span class="hljs-string">"darkred"</span>,
      pch = <span class="hljs-number">2</span>,
      ylim = range(<span class="hljs-number">0</span>, <span class="hljs-number">20</span>),
      main = <span class="hljs-string">"June 25th timeline"</span>,
      xlab = <span class="hljs-string">"time"</span>,
      yaxt = <span class="hljs-string">"n"</span>,
      ylab = <span class="hljs-string">""</span>
    )
    points(mention25th, rep(<span class="hljs-number">10</span>, length(mention25th)), col = <span class="hljs-string">"darkgreen"</span>, pch = <span class="hljs-number">1</span>)
    points(follow25th, rep(<span class="hljs-number">5</span>, length(follow25th)), col = <span class="hljs-string">"darkblue"</span>, pch = <span class="hljs-number">0</span>)</code>


[![](http://arabianAnalyst.com/wp-content/uploads/2017/11/Timeline-300x150.png)](http://arabianAnalyst.com/wp-content/uploads/2017/11/Timeline.png)

في الرسم البياني أعلاه، المربعات الزرقاء الداكنة هي عدد المتابعين، والدوائر الخضراء الداكنة هي عدد الذكر والمثلثات الحمراء الداكنة هي عدد إعادة التغريدات

هذا الرسم البياني يبين لنا أمرين على الأقل. أولا، لا تبدو إعادة التغريدات هي السبب في النمو لأنها تظهر بعد ظهور المتابعين وليس قبله. الامر الثاني هو ان الكثير من المتابعين والذكر كانوا في الفترة المسائية (بعد 6:00pm). بتوقيت الولايات المتحدة

للكشف عن هوية المسوق الرائع. يمكننا أن نذهب إلى حساب بريدنا الإلكتروني و التحقق من الذي كان أول من ذكرنا بعد 6:00pm. أو يمكننا أن نفعل ذلك باستخدام R العجيب. الكود كالتالي.

    
    <code class="hljs">dbMention[which(strftime(mentionTime, format = <span class="hljs-string">"%H"</span>) > <span class="hljs-string">"18"</span>)[<span class="hljs-number">1</span>],<span class="hljs-string">"email_subj"</span>]
    <span class="hljs-comment">#"Subject: Fahad Alhazmi (@fahd09) mentioned you in conversation on Twitter!"</span></code>










#### النتائج


وفقا لتحليلنا، كان الحدث المسؤول الذي تسبب في هذا التدفق من المتابعين هو [الذكر](https://twitter.com/fahd09/status/746864690240815104) الذي كتبه [فهد الحازمي](https://twitter.com/fahd09) في 08:20 (التوقيت الشرقي للولايات المتحدة). بما ان المستخدم يقطن في منطقة زمنية مختلفة، هذا الحدث كان في حوالي الساعة 12:20 من صباح يوم 26 حسب وقت المسوق. لذلك، أعطي شكر خاص إلى فهد لدعمه حسابنا على تويتر و والموقع كذلك.









#### ملخص


في هذه المقالة ناقشنا مختلف الأدوات المتاحة لمتابعة وتحليل المتابعين في تويتر. بالإضافة إلى ذلك، فلقد استخدمنا البريد الإلكتروني لدينا كمصدر للبيانات و لتتبع كل الأحداث على حساب تويتر لدينا. وأخيرا، أجرينا تحليلا دقيقا لتحديد نقطة التحول الغير اعتيادية لنمو عدد المتابعين لدينا ومعرفة سبب هذا الحدث.











