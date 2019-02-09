---
author: Hussain
comments: true
date: 2018-03-27 20:00:59+00:00
layout: post
link: https://arabianAnalyst.com/2018/03/27/exploring-xbrl-world/
slug: exploring-xbrl-world
title: دخول عالم المال وبياناتها مع صيغة XBRL
wordpress_id: 181
categories:
- دروس
---




































### مقدمة




في نظري احد اهم التحديات التي قد يواجهها محلل البيانات هي التعامل مع صيغ وتنسيقات مختلفة للبيانات. على سبيل المثال، وكالة ناسا للفضاء تستخدم صيغة netCDF وهي تنسيق خاص للبيانات قامت ناسا نفسها بتطويره لحفظ محتوى البيانات الفلكية وتعهدت بصيانته. أيضا من بين تلك الصيغ الخاصة، وهي أيضا محور هذا المقال، هي XBRL وهي اختصار لـ Extensible Business Reporting Language وهي صيغة قام بتطويرها تحالف دولي غير ربحي مكوّن من ٤٠٠ عضو. الهدف من هذه الصيغة هو تحسين عملية الإفصاح عن المعلومات المالية للشركات وجعل قوائمها المالية اكثر إفادة ودقة ومصداقية وفاعلية.










### لماذا اهتم كمحلل للبيانات بصيغة XBRL؟




هناك مزايا وفوائد كثيرة لتعلم هذه الصيغة لا يسع هذا المقال تناولها كلها لكن سنكتفي ببعضها.




1-تبني كثير من هيئات سوق المال حول العالم لهذه الصيغة. في كثير من الدول الآن تطالب الشركات المدرجة للتداول في السوق المالي برفع قوائمها المالية بإستخدام هذه الصيغة. مؤخرا، هيئة سوق المال السعودية




2-تعلم صيغة XBRL مهارة تفتح لك باب عالم إدارة الأموال وتقييم الشركات ومقارنة ادائها بالنسبة للشركات الأخرى في نفس النشاط التجاري.




3- تعلم صيغ مختلفة للبيانات يساعدك على تحسين خطوات سير عملك improve your workflow. لتكون لديك المرونة لاحقا للتعامل مع اَي نوع من البيانات.










### لمحة سريعة عن XBRL




من مستوى رفيع، تتكون صيغة XBRL الى قسمين رئيسين





 1- التصنيفات أو ما تعرف بـ XBRL Taxonomy. فذا القسم مسؤول عن مخطط المستندات المالية الذي بدوره يحتوي على تعاريف المفاهيم والمصطلحات التي من خلالها نقوم ببناء المستند. لتقريب الصورة, التصنيفات هي مثل كروكي أو مخطط البيت. في المخطط نعرف عدد الغرف و أسمائها وكذلك وظائفها.




2- النموذج أو ما يعرف بـ XBRL Instance. النموذج يحتوي على جميع الحقائق المنشورة في التقارير المالية. بإختصار XBRL Instance هو المنتج الذي نصنعه وفق الشروط و المصطلحات الموجودة في قسم التصنيفات. لو نرجع لمثال مخطط البيت، النموذج هو البيت على ارض الواقع.




بالتأكيد كل قسم يتفرع لعدة اقسام وكل منها له تفاصيل كثيرة لكن نكتفي بذكر كل منها والتعليق على اهميتها.




اجزاء التصنيفات كالتالي




1- مخطط التصنيفات Taxonomy Schemas : مجرد مخطط كما في ملفات XML يحوي على المصطلحات وتعريفاتها






 2- قواعد الوصل Linkbases ولها وظيفة مهمة جدا وهي ربط تلك المفاهيم والمصطلحات ببعضها البعض. يوجد ثلاث انواع من قواعد الربط وهي Presenation و Calculations و Definition سوف نستعرض اهميتها في المثال.




اجزاء النموذج كالتالي




1-المصادر References و المقصود بها هنا نقاط الرجوع إلى مصدر التصنيفات حيث أن كل عنصر من النموذج لابد أن يكون تحت حوكمة التصنيفات. سنتطرق لذلك في المثال.




2- السياق Contexts وهو ما يعطي الحقاق معنى للمستخدم. على سبيل المثال , ١٠٠ دولار بحد ذاتها لا تعني شيء لكن وجودها في سياق قائمة الارباح لفترة معنية يضيف لها معنى.




3-الوحدة Units الحقائق تختلف محفوظة بصيغ مختلفة فمنها بالآلاف ومنها بالملايين. الوحدة توضح لنا ايهما الآخر.




4- الحقائق Facts هنا يتم حفظ كل الحقائق المذكورة في القوائم المالية.




هناك اجزاء اخرى لا تهمنا في هذا المقال, لذلك سنكتفي هؤلاء الاربعة.










### مثال عملي : حقائق شركة ابل المالية عام 2017




في البداية سنقوم بإرفاق بعض الحزم المهمة. ايضا سنقوم بإجراء بعض التعديلات على خيارات بيئة أر



    
    <code>#Installing the required packages. 
    #install.packages(c("XBRL", "finreportr", "stringr"))
    library("XBRL")
    library("finreportr")
    library("stringr")
    #============================ Setting up some parameters =========================
    
    #Setting up the downloading method
    options(download.file.method = "curl")
    
    #Saving our current settings
    original.options <-options()
    
    #Setting up the new options
    new.options <- options(stringsAsFactors = FALSE)</code>




كذا تمام. التقرير الي راح نقوم بتحليله هو تقرير شركة ابل المالي لعام 2017 .لذلك قبل ما نبدأ نحتاج نتأكد أن التقرير هذا اصلا موجود. ولو موجود نحتاج برضو نستخرج الرابط الي ياخذنا للتقرير بشكل تلقائي



    
    <code>#===================Getting The links for our specified company  ==================
    
    #Inital Parameters
    Comp.tick <- "AAPL"
    
    #Getting the available Annual reports for the JP Morgan  bank 
    Annuals.list<-AnnualReports(Comp.tick, foreign = FALSE)
    Annuals.list</code>



    
    <code>
    ##    filing.name filing.date         accession.no
    ## 1         10-K  2017-11-03 0000320193-17-000070
    ## 2         10-K  2016-10-26 0001628280-16-020309
    ## 3         10-K  2015-10-28 0001193125-15-356351
    ## 4         10-K  2014-10-27 0001193125-14-383437
    ## 5         10-K  2013-10-30 0001193125-13-416534
    ## 6         10-K  2012-10-31 0001193125-12-444068
    ## 7         10-K  2011-10-26 0001193125-11-282113
    ## 8         10-K  2010-10-27 0001193125-10-238044
    ## 9       10-K/A  2010-01-25 0001193125-10-012091
    ## 10        10-K  2009-10-27 0001193125-09-214859
    ## 11        10-K  2008-11-05 0001193125-08-224958
    ## 12        10-K  2007-11-15 0001047469-07-009340
    ## 13        10-K  2006-12-29 0001104659-06-084288
    ## 14        10-K  2005-12-01 0001104659-05-058421
    ## 15        10-K  2004-12-03 0001047469-04-035975
    ## 16        10-K  2003-12-19 0001047469-03-041604
    ## 17        10-K  2002-12-19 0001047469-02-007674
    ## 18     10-K405  2001-12-21 0000912057-01-544436
    ## 19        10-K  2000-12-14 0000912057-00-053623
    ## 20        10-K  1999-12-22 0000912057-99-010244
    ## 21     10-K405  1998-12-23 0001047469-98-044981
    ## 22      10-K/A  1998-01-23 0001047469-98-001822
    ## 23        10-K  1997-12-05 0001047469-97-006960
    ## 24        10-K  1996-12-19 0000320193-96-000023
    ## 25        10-K  1995-12-19 0000320193-95-000016
    ## 26        10-K  1994-12-13 0000320193-94-000016</code>




اوكي تمام التقرير موجود, طيب خلينا نستخرج الرابط الآن



    
    <code>#Selecting the Type of record we would like to analyze 
    Rec.no <- 1
    
    #Extracting the CIK number 
    cik <- as.character(as.numeric(str_split(Annuals.list$accession.no[Rec.no], "-")[[1]][1]))
    
    #Listing all the LINK available for the Annual reports 
    cbind(paste0("https://www.sec.gov/Archives/edgar/data/",cik,"/", Annuals.list$accession.no, "-index.html"), Annuals.list$filing.date)</code>



    
    <code>##       [,1]                                                                            
    ##  [1,] "https://www.sec.gov/Archives/edgar/data/320193/0000320193-17-000070-index.html"
    ##  [2,] "https://www.sec.gov/Archives/edgar/data/320193/0001628280-16-020309-index.html"
    ##  [3,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-15-356351-index.html"
    ##  [4,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-14-383437-index.html"
    ##  [5,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-13-416534-index.html"
    ##  [6,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-12-444068-index.html"
    ##  [7,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-11-282113-index.html"
    ##  [8,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-10-238044-index.html"
    ##  [9,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-10-012091-index.html"
    ## [10,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-09-214859-index.html"
    ## [11,] "https://www.sec.gov/Archives/edgar/data/320193/0001193125-08-224958-index.html"
    ## [12,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-07-009340-index.html"
    ## [13,] "https://www.sec.gov/Archives/edgar/data/320193/0001104659-06-084288-index.html"
    ## [14,] "https://www.sec.gov/Archives/edgar/data/320193/0001104659-05-058421-index.html"
    ## [15,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-04-035975-index.html"
    ## [16,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-03-041604-index.html"
    ## [17,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-02-007674-index.html"
    ## [18,] "https://www.sec.gov/Archives/edgar/data/320193/0000912057-01-544436-index.html"
    ## [19,] "https://www.sec.gov/Archives/edgar/data/320193/0000912057-00-053623-index.html"
    ## [20,] "https://www.sec.gov/Archives/edgar/data/320193/0000912057-99-010244-index.html"
    ## [21,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-98-044981-index.html"
    ## [22,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-98-001822-index.html"
    ## [23,] "https://www.sec.gov/Archives/edgar/data/320193/0001047469-97-006960-index.html"
    ## [24,] "https://www.sec.gov/Archives/edgar/data/320193/0000320193-96-000023-index.html"
    ## [25,] "https://www.sec.gov/Archives/edgar/data/320193/0000320193-95-000016-index.html"
    ## [26,] "https://www.sec.gov/Archives/edgar/data/320193/0000320193-94-000016-index.html"
    ##       [,2]        
    ##  [1,] "2017-11-03"
    ##  [2,] "2016-10-26"
    ##  [3,] "2015-10-28"
    ##  [4,] "2014-10-27"
    ##  [5,] "2013-10-30"
    ##  [6,] "2012-10-31"
    ##  [7,] "2011-10-26"
    ##  [8,] "2010-10-27"
    ##  [9,] "2010-01-25"
    ## [10,] "2009-10-27"
    ## [11,] "2008-11-05"
    ## [12,] "2007-11-15"
    ## [13,] "2006-12-29"
    ## [14,] "2005-12-01"
    ## [15,] "2004-12-03"
    ## [16,] "2003-12-19"
    ## [17,] "2002-12-19"
    ## [18,] "2001-12-21"
    ## [19,] "2000-12-14"
    ## [20,] "1999-12-22"
    ## [21,] "1998-12-23"
    ## [22,] "1998-01-23"
    ## [23,] "1997-12-05"
    ## [24,] "1996-12-19"
    ## [25,] "1995-12-19"
    ## [26,] "1994-12-13"</code>




زي ما انت شايف لقينا الرابط. في الحقيقة هذا الرابط راح نستخدمه علشان نحصل رابط ملفات XBRL لذلك راح نحتاج ننسخ الرابط في المتصفح ونرجع برابط الملفات (انا عملت هذه الشغلة والرابط كما هو باين عندك تحت)



    
    <code>#Selecting the URL we are interested in
    url <- "https://www.sec.gov/Archives/edgar/data/320193/000032019317000070/aapl-20170930.xml"</code>




الحين يجي دور حزمة XBRL العظيمة. هذه الحزمة راح تعالج لنا البيانات بشكل جميل. هذه الحزمة تقوم بجمع كل البيانات من ملفات XBRL ووضعها في هيكلة بيانات مجدولة



    
    <code>#=================== Starting the XBRL files Analysis =========================
    
    #Analzying the XBRL files ### THIS MAY TAKE LONG TIME ####
    xbrl_data <- xbrlDoAll(url, delete.cached.inst = TRUE)</code>




ما حلصنا عليه في المتغير xbrl_data هو مجموعة من الجداول تمثل ما سبق وتحدثنا عنه حول اقسام واجزاء صيغة XBRL لعل هذه الصورة توضح ما لدينا





![](https://raw.githubusercontent.com/bergant/XBRLFiles/master/img/xbrl_files.png)




بفضل هذه الحزمة العجيبة اصبح بإمكاننا التنقل بين عناصر هذه الصيغة بكل سهولة. بل بإستعانة بحزمة dplyr اصبح لدينا القدرة على صنع استعلامات شبيه بأوامر SQL للحصول عل ما نريد




لنفترض أننا نريد الحصول على مبيعات ابل خلال الثلاث سنوات الماضية. يمكننا فعل ذلك بكل سهولة عبر الكود التالي



    
    <code>#Loading Some Data minupliating libraries 
    library(dplyr)
    library(tidyr)
    
    SalesRevenueNet<-xbrl_data$element %>%
      filter(elementId=="us-gaap_SalesRevenueNet" ) %>%
      left_join(xbrl_data$fact, by =  "elementId") %>% left_join(xbrl_data$context, by ="contextId") %>%
      left_join(xbrl_data$label, by = "elementId") %>%  left_join(xbrl_data$unit, by ="unitId") %>% 
      filter(is.na(dimension1) & grepl("4YTD", contextId) & labelRole =="http://www.xbrl.org/2003/role/label") %>%
      select(labelString, contextId, fact)
    
    SalesRevenueNet</code>



    
    <code>##    labelString   contextId         fact
    ## 1 Revenue, Net FD2015Q4YTD 233715000000
    ## 2 Revenue, Net FD2016Q4YTD 215639000000
    ## 3 Revenue, Net FD2017Q4YTD 229234000000</code>




كذلك يمكننا ايضا الحصول على تكاليف تلك المبيعات بكود مشابه



    
    <code>CostOfGoodsSold<-xbrl_data$element %>%
      filter(elementId=="us-gaap_CostOfGoodsAndServicesSold" ) %>%
      left_join(xbrl_data$fact, by =  "elementId") %>% left_join(xbrl_data$context, by ="contextId") %>%
      left_join(xbrl_data$label, by = "elementId") %>%  left_join(xbrl_data$unit, by ="unitId") %>% 
      filter(is.na(dimension1) & labelRole =="http://www.xbrl.org/2003/role/label")  %>%
      select(labelString, contextId, fact)
    
    CostOfGoodsSold</code>



    
    <code>##                       labelString   contextId         fact
    ## 1 Cost of Goods and Services Sold FD2015Q4YTD 140089000000
    ## 2 Cost of Goods and Services Sold FD2016Q4YTD 131376000000
    ## 3 Cost of Goods and Services Sold FD2017Q4YTD 141048000000</code>










### طريقة مبتكرة لكشف قواعد الوصل بين العناصر




سبق وذكرنا ان صيغة XBRL تحوي بجعبتها علاقة كل عنصر بالآخر بما يتوافق مع دورهم في القوائم المالية




لتبسيط هذه الفكرة , لنأخذ قائمة الارباح على سبيل المثال. في هذه القائمة نتوقع أن تكون الأرباح هي العامل المشترك بين كل العناصر المساهمة لصنع تلك القائمة. بمعنى أخر, للحصول على صافي الارباح نحتاج إلى ان نقوم بعملية طرح بين المبيعات والتكاليف على سبيل المثال.




بدل من صنع القائمة ذاتها قررت أن اصور تلك العلاقات على هيئة علاقة شبكية. خلينا ننتقل إلى الكود, النتائج بصراحة جميلة



    
    <code>#Financial Statement Role 
    role_Id <- "http://www.apple.com/role/ConsolidatedStatementsOfOperations"
    # Visualizing The calculation stracture of the financial statements
    library(igraph)
    doc.graph <-xbrl_data$calculation [which(xbrl_data$calculation$roleId == role_Id ),c("fromElementId", "toElementId")]
    doc.viz <- graph.data.frame(doc.graph)
    plot.igraph(doc.viz, vertex.label.color ="black", vertex.label.cex = .5 ,vertex.color = "Navyblue", layout = layout.reingold.tilford)</code>





![](http://arabianAnalyst.com/wp-content/uploads/2018/03/Financial-Statements.png)





وفقط للحماس هذه قائمة الأصول




![](http://arabianAnalyst.com/wp-content/uploads/2018/03/BalanceSheet.png)




