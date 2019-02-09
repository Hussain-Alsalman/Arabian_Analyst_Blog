---
author: Hussain
comments: true
date: 2017-11-22 20:02:24+00:00
layout: post
link: https://arabianAnalyst.com/2017/11/22/interactive-map-saudi-imports/
slug: interactive-map-saudi-imports
title: 'فكرة عابرة : خريطة تفاعلية لواردات المملكة'
wordpress_id: 89
categories:
- دروس
---



































احد اسرار عشقي للغة الـ R هو شعوري بأنه لا يوجد حد لما استطيع فعله بهذه اللغة الرائعة. اليوم سوف استعرض لكم احد تلك الإمكانيات الا وهي صنع تصويرات بيانية تفاعلية. نعم لقد قرأت تلك الجملة بشكل صحيح. R بإمكانه أن يكون تفاعلي. في الحقيقة هناك الكثير من الحزم التي تمكننا من فعل ذلك لكنني لن استعرضها كلها فذلك يستحق كتاب بذاته.







### حركة الـ HTMLWIDGETS




مؤخرا انتشرت حركة في وسط مجتمع مطوري حزم لغة R خصوصا المهتمين بالتصوير البياني. تلك الحركة تهدف إلى جلب قوة Java Script الرائعة في صنع مختلف الرسومات والتصويرات البيانية على صفحات الويب و ترويضها لتعمل بتناغم تام مع لغة R . تلك الحركة انجبت لنا ما يعرف الآن بـ [HTMLWIDGETS](https//www.htmlwidgets.com)







#### مثال واردات المملكة




دعنا نحاول ان نمثل واردات المملكة على خريطة تفاعلية حيث يمكننا التصغير و التكبير وحتى سحب الخريطة نفسها. سنقوم بذلك عن طريق اتباع الخطوات التالية :






  1. تحميل المعلومات من موقع [هيئة الإحصاء](https://www.stats.gov.sa/ar/214)



    
    <code class="r"><span class="identifier">sa.imp</span> <span class="operator"><-</span> <span class="identifier">read.csv</span><span class="paren">(</span><span class="string">"Saudi_Imports.csv"</span>, <span class="identifier">sep</span> <span class="operator">=</span> <span class="string">","</span>, <span class="identifier">header</span> <span class="operator">=</span> <span class="literal">TRUE</span><span class="paren">)</span></code>






  2. إرفاق بعض الحزم المهمة



    
    <code class="r"><span class="keyword">library</span><span class="paren">(</span><span class="string">"dplyr"</span><span class="paren">)</span> <span class="comment"># لمعالجة البيانات</span>
    <span class="keyword">library</span><span class="paren">(</span><span class="string">"stringi"</span><span class="paren">)</span> <span class="comment"># حزمة متخصصة لمعالجة النصوص</span>
    <span class="keyword">library</span><span class="paren">(</span><span class="string">"leaflet"</span><span class="paren">)</span> <span class="comment"># لرسم الخرائط التفاعلية</span>
    <span class="keyword">library</span><span class="paren">(</span><span class="string">"sp"</span><span class="paren">)</span> <span class="comment"># حزمة مساعدة للرسم على الخرائط</span>
    <span class="keyword">library</span><span class="paren">(</span><span class="string">"maps"</span><span class="paren">)</span> <span class="comment"># معلومات عن الدول وعواصمها</span>
    <span class="keyword">library</span><span class="paren">(</span><span class="string">"widgetframe"</span><span class="paren">)</span></code>






  3. حساب مجموع قيمة الواردات إلى المملكة



    
    <code class="r"><span class="identifier">grouped.imports</span><span class="operator"><-</span> <span class="identifier">sa.imp</span> <span class="operator">%>%</span> <span class="identifier">group_by</span><span class="paren">(</span><span class="identifier">Country.Name</span><span class="paren">)</span> <span class="operator">%>%</span> <span class="identifier">summarise</span><span class="paren">(</span><span class="identifier">grand.total</span> <span class="operator">=</span> <span class="identifier">sum</span><span class="paren">(</span><span class="identifier">Value</span><span class="paren">)</span><span class="paren">)</span></code>






  4. كتابة دالة لإستخراج احداثيات عاصمة اي دولة




  * نحتاج هذه العملية لرسم الخطوط بين المملكة وباقي الدول و لأن بعض الدول كبيرة جدا يتوجب علينا تحديد بداية كل خط


  * لذلك اخترنا بداية الخط لتكون العاصمة في تلك الدول



    
    <code class="r"><span class="identifier">get.capital.location</span> <span class="operator"><-</span> <span class="keyword">function</span><span class="paren">(</span><span class="identifier">c.n</span><span class="paren">)</span> <span class="paren">{</span>
      <span class="identifier">x</span> <span class="operator"><-</span><span class="identifier">filter</span><span class="paren">(</span><span class="identifier">world.cities</span>, <span class="identifier">country.etc</span> <span class="operator">==</span> <span class="identifier">c.n</span>, <span class="identifier">capital</span> <span class="operator">==</span> <span class="number">1</span><span class="paren">)</span>
      <span class="keyword">return</span><span class="paren">(</span><span class="identifier">x</span><span class="paren">[</span>,<span class="number">5</span><span class="operator">:</span><span class="number">4</span><span class="paren">]</span><span class="paren">)</span>
    <span class="paren">}</span></code>






  5. معالجة الأسماء الموجودة في البيانات من هيئة الإحصاء لتتوافق مع حزمة maps



    
    <code class="r"><span class="identifier">ds</span><span class="operator"><-</span><span class="identifier">sapply</span><span class="paren">(</span><span class="identifier">stri_trans_totitle</span><span class="paren">(</span><span class="identifier">grouped.imports</span><span class="operator">$</span><span class="identifier">Country.Name</span><span class="paren">)</span>,<span class="identifier">get.capital.location</span><span class="paren">)</span>
    <span class="identifier">ds.1</span> <span class="operator"><-</span> <span class="identifier">t</span><span class="paren">(</span><span class="identifier">ds</span><span class="paren">)</span></code>






  6. معالجة بعض اسماء الدول يدويا



    
    <code class="r"><span class="identifier">ds.1</span><span class="paren">[</span><span class="string">"United Kingdom"</span>,<span class="paren">]</span> <span class="operator"><-</span> <span class="identifier">get.capital.location</span><span class="paren">(</span><span class="string">"UK"</span><span class="paren">)</span>
    <span class="identifier">ds.1</span><span class="paren">[</span><span class="string">"U.s.a"</span>,<span class="paren">]</span> <span class="operator"><-</span> <span class="identifier">get.capital.location</span><span class="paren">(</span><span class="string">"USA"</span><span class="paren">)</span>
    <span class="identifier">ds.1</span><span class="paren">[</span><span class="string">"Sultanate Of Oman"</span>,<span class="paren">]</span> <span class="operator"><-</span> <span class="identifier">get.capital.location</span><span class="paren">(</span><span class="string">"Oman"</span><span class="paren">)</span>
    <span class="identifier">ds.1</span><span class="paren">[</span><span class="string">"Russian Federation"</span>,<span class="paren">]</span> <span class="operator"><-</span> <span class="identifier">get.capital.location</span><span class="paren">(</span><span class="string">"Russia"</span><span class="paren">)</span>
    <span class="identifier">ds.1</span><span class="paren">[</span>,<span class="number">1</span><span class="paren">]</span><span class="operator"><-</span><span class="identifier">as.numeric</span><span class="paren">(</span><span class="identifier">as.character</span><span class="paren">(</span><span class="identifier">ds.1</span><span class="paren">[</span>,<span class="number">1</span><span class="paren">]</span><span class="paren">)</span><span class="paren">)</span>
    <span class="identifier">ds.1</span><span class="paren">[</span>,<span class="number">2</span><span class="paren">]</span><span class="operator"><-</span><span class="identifier">as.numeric</span><span class="paren">(</span><span class="identifier">as.character</span><span class="paren">(</span><span class="identifier">ds.1</span><span class="paren">[</span>,<span class="number">2</span><span class="paren">]</span><span class="paren">)</span><span class="paren">)</span>
    <span class="identifier">ds.c</span> <span class="operator"><-</span> <span class="identifier">matrix</span><span class="paren">(</span><span class="identifier">unlist</span><span class="paren">(</span><span class="identifier">ds.1</span><span class="paren">)</span>, <span class="identifier">ncol</span> <span class="operator">=</span> <span class="number">2</span><span class="paren">)</span>
    <span class="identifier">rownames</span><span class="paren">(</span><span class="identifier">ds.c</span><span class="paren">)</span> <span class="operator"><-</span><span class="identifier">rownames</span><span class="paren">(</span><span class="identifier">ds.1</span><span class="paren">)</span>
    <span class="identifier">ds.c</span> <span class="operator"><-</span> <span class="identifier">ds.c</span><span class="paren">[</span><span class="operator">!</span><span class="identifier">is.na</span><span class="paren">(</span><span class="identifier">ds.c</span><span class="paren">[</span>,<span class="number">1</span><span class="paren">]</span><span class="paren">)</span>,<span class="paren">]</span></code>






  7. كتابة دالة متخصص في ربط كل إحداثية عاصمة بالرياض



    
    <code class="r"><span class="identifier">creat.line2sa</span> <span class="operator"><-</span> <span class="keyword">function</span><span class="paren">(</span><span class="identifier">x</span><span class="paren">)</span> <span class="paren">{</span>
      <span class="keyword">return</span><span class="paren">(</span><span class="identifier">Line</span><span class="paren">(</span><span class="identifier">rbind</span><span class="paren">(</span>
        <span class="identifier">get.capital.location</span><span class="paren">(</span><span class="string">"Saudi Arabia"</span><span class="paren">)</span>, <span class="identifier">x</span>, <span class="identifier">make.row.names</span> <span class="operator">=</span> <span class="literal">TRUE</span>
        <span class="paren">)</span><span class="paren">)</span><span class="paren">)</span>
      
    <span class="paren">}</span></code>






  8. إعداد قائمة الخطوط ومعالجتها لتكون جاهزة للتصوير الجيوغرافي



    
    <code class="r"><span class="identifier">lines.list</span> <span class="operator"><-</span><span class="identifier">lapply</span><span class="paren">(</span><span class="identifier">seq_along</span><span class="paren">(</span><span class="identifier">ds.c</span><span class="paren">[</span>,<span class="number">1</span><span class="paren">]</span><span class="paren">)</span>, <span class="keyword">function</span><span class="paren">(</span><span class="identifier">inex</span>, <span class="identifier">x</span><span class="paren">)</span> <span class="paren">{</span><span class="paren">(</span><span class="identifier">creat.line2sa</span><span class="paren">(</span><span class="identifier">ds.c</span><span class="paren">[</span><span class="identifier">inex</span>,<span class="paren">]</span><span class="paren">)</span><span class="paren">)</span><span class="paren">}</span>, <span class="identifier">x</span><span class="operator">=</span><span class="identifier">ds.c</span><span class="paren">)</span></code>






  9. اخيرا رسم الخريطة التفاعلية مع جميع خطوط واردات المملكة



    
    <code class="r"><span class="identifier">mapp</span><span class="operator"><-</span><span class="identifier">leaflet</span><span class="paren">(</span><span class="paren">)</span> <span class="operator">%>%</span> <span class="identifier">addTiles</span><span class="paren">(</span><span class="paren">)</span>  <span class="operator">%>%</span> <span class="identifier">addPolylines</span><span class="paren">(</span><span class="identifier">data</span> <span class="operator">=</span><span class="identifier">SpatialLines</span><span class="paren">(</span><span class="identifier">list</span><span class="paren">(</span><span class="identifier">Lines</span><span class="paren">(</span><span class="identifier">lines.list</span>, <span class="string">"ID"</span><span class="paren">)</span><span class="paren">)</span><span class="paren">)</span><span class="paren">)</span> 
    <span class="identifier">frameWidget</span><span class="paren">(</span><span class="identifier">mapp</span>, <span class="identifier">height</span> <span class="operator">=</span> <span class="number">350</span>, <span class="identifier">width</span> <span class="operator">=</span> <span class="string">'95%'</span><span class="paren">)</span></code>




























