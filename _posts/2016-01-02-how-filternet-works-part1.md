---
layout: post
comments: true
title: فیلترینگ ایران چگونه کار می‌کند؟! - قسمت اول
tags: فیلترینگ
---
اگر عنوان این مطلب را در گوگل به فارسی و یا انگلیسی جستجو کنید، احتمالا کلی مطالب و مقاله و حتی ارائه پیدا می کنید که در رابطه با زیرساخت فیلترینگ، نحوه کار فیلترینگ، تاریخچه و تغییرات آن، روش های فیلتر کردن در ایران و ... صحبت شده است. اما چیزی که در همه آنها مشترک است، همه از روش‌های مکاشفه ای استفاده شده که ممکن هست در بعضی موارد بر اساس حدس و گمان و یا موارد مشابه در سایر کشورهای دیگر باشد. به عبارت دیگر متاسفانه سازمان و یا سازمان هایی که در رابطه با فیلترینگ ایران کار می کنند جزییات فنی در اختیار عموم قرار نمی دهند.

در این پست و پست های آتی، سعی دارم از روش ‌های مکاشفه‌ای و تحقیقات انجام شده توسط دیگران، برای شفاف کردن ساختار فیلترینگ استفاده کنم. تمرکز من بیشتر روی این سوال است: "فیلترینگ چگونه کار می کند؟"، البته در مطالب آینده به سوال "از چه روش‌هایی برای فیلتر کردن وبسایت ها استفاده می‌شود؟" نیز پاسخ خواهم داد.

اما برویم سر اصل مطلب، تا الان چه چیزهایی می دانیم؟

** بروزرسانی ۱: نتایج بدست آمده فقط برای ISP پارس آنلاین صادق است اطلاعات بیشتر:**
https://twitter.com/aliborhani1/status/689447292210786304

<!-- more -->

## ۱. پراکسی واسط

** بروزرسانی ۱: نتایج بدست آمده فقط برای ISP پارس آنلاین صادق است اطلاعات بیشتر:**
https://twitter.com/aliborhani1/status/689447292210786304

حدود یک سالی است که یک پراکسی واسط میان ارتباط مشترکین با سرور مقصد قرار گرفته است. قبل از اینکه نشان دهم که این پراکسی چه کارهایی انجام می‌دهد، می‌خواهم اثبات کنم که چنین پراکسی وجود دارد!

یکی از  ساده ترین راه‌ها استفاده از [tcptraceroute](http://linux.die.net/man/1/tcptraceroute) است:

{% highlight bash %}
sudo tcptraceroute yahoo.com 80
{% endhighlight %}
و نتیجه:
‍‍‍‍‍{% highlight bash %}
Selected device en0, address ***.***.***.***, port ***** for outgoing packets
Tracing the path to yahoo.com (98.139.183.24) on TCP port 80 (http), 30 hops max
1  ***.***.***.***  10.186 ms  0.975 ms  0.976 ms
2  ***.***.***.***  54.325 ms  43.755 ms  42.327 ms
3  10.241.33.252  42.228 ms  43.210 ms  41.730 ms
4  10.241.37.89  53.760 ms  56.329 ms  41.514 ms
5  10.234.120.177  59.333 ms  42.006 ms  42.840 ms
6  10.234.232.198  42.151 ms
   10.234.232.194  55.030 ms
   10.234.141.250  55.961 ms
7  ir2.fp.vip.bf1.yahoo.com (98.139.183.24) [open]  44.871 ms  41.724 ms  58.778 ms
{% endhighlight %}


و حالا پینگ این ip:

{% highlight bash %}
$ ping 98.139.183.24
PING 98.139.183.24 (98.139.183.24): 56 data bytes
64 bytes from 98.139.183.24: icmp_seq=0 ttl=45 time=409.474 ms
64 bytes from 98.139.183.24: icmp_seq=1 ttl=45 time=468.637 ms
64 bytes from 98.139.183.24: icmp_seq=2 ttl=45 time=447.541 ms
64 bytes from 98.139.183.24: icmp_seq=3 ttl=45 time=396.111 ms
64 bytes from 98.139.183.24: icmp_seq=4 ttl=45 time=387.041 ms
64 bytes from 98.139.183.24: icmp_seq=5 ttl=45 time=361.741 ms
^C
--- 98.139.183.24 ping statistics ---
6 packets transmitted, 6 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 361.741/411.757/468.637/36.236 ms
{% endhighlight %}

اما این قرار است چه چیزی را ثابت کند؟ اگر به زمان ها توجه کنید، عددها با هم سازگاری ندارند در حالت اول تقریبا با ۴۰-۵۰ میلی ثانیه ما به مقصد رسیده ایم اما هنگامی که پینگ همان ip گرفته می شود عدد تقریبا ۷-۸ برابر بیشتر است و واقعی تر به نظر می رسد!

اگر این دلیل برای شما قابل قبول نیست بگذارید این بار پورت ۴۴۳ (یا هر پورت دیگر غیر از ۸۰) را امتحان کنیم:

{% highlight bash %}
$ sudo tcptraceroute yahoo.com 443
Password:
Selected device en0, address ***.***.***.***, port ***** for outgoing packets
Tracing the path to yahoo.com (98.139.183.24) on TCP port 443 (https), 30 hops max
 1  ***.***.***.***  5.937 ms  0.961 ms  1.038 ms
 2  ***.***.***.***  41.974 ms  41.587 ms  42.335 ms
 3  10.241.33.252  41.516 ms  54.775 ms  41.879 ms
 4  10.241.37.89  62.787 ms  54.310 ms  53.367 ms
 5  10.234.120.177  53.411 ms  54.996 ms  42.902 ms
 6  10.234.141.254  42.980 ms
    10.234.232.194  54.889 ms
    10.234.232.198  42.363 ms
 7  10.234.141.21  47.631 ms  43.379 ms  58.778 ms
 8  10.234.1.6  56.733 ms  67.895 ms *
 9  78.38.241.66  144.380 ms  164.184 ms  170.363 ms
10  10.201.42.189  160.228 ms  165.964 ms  165.840 ms
11  ffm-k3-i1-link.telia.net (213.248.78.93)  378.697 ms  306.610 ms  307.039 ms
12  ffm-b1-link.telia.net (62.115.134.86)  307.203 ms  306.846 ms  306.975 ms
13  ffm-bb2-link.telia.net (62.115.141.238)  307.299 ms  306.073 ms  307.321 ms
14  nyk-bb2-link.telia.net (62.115.139.15)  409.406 ms * 357.779 ms
15  buf-b1-link.telia.net (62.115.141.180)  348.746 ms  360.708 ms  589.166 ms
16  yahoo-ic-306865-buf-b1.c.telia.net (213.248.89.170)  392.052 ms  408.982 ms  409.476 ms
17  et-19-0-0.pat2.bfz.yahoo.com (216.115.97.105)  409.436 ms  408.785 ms  409.651 ms
18  et-19-1-0.msr1.bf2.yahoo.com (74.6.227.147)  409.497 ms  409.005 ms  409.605 ms
19  unknown-74-6-122-x.yahoo.com (74.6.122.59)  409.663 ms  408.873 ms  512.093 ms
20  * po8.fab2-1-gdc.bf1.yahoo.com (72.30.22.35) 481.640 ms  408.899 ms
21  po-10.bas1-7-prd.bf1.yahoo.com (98.139.129.161)  409.468 ms  408.918 ms  409.478 ms
22  ir2.fp.vip.bf1.yahoo.com (98.139.183.24) [open]  408.279 ms  410.595 ms  409.567 ms
{% endhighlight %}

چه مسافرت طولانی! این برای هر پورت غیر از ۸۰ هم صادق است. اما چرا پورت ۸۰ اینقدر رفتار متفاوتی دارد خود نیز بحث مفصلی است که در ادامه این پست و پست‌های بعدی به آن اشاره خواهم کرد. همان طور که می بینید نتایج واقعی تر به نظر می رسد، بعد از نود دهم تازه از ایران خارج شده ایم! (می توانید مشخصات ip نود یازدهم را سرچ کنید) اما در قبلی تنها با گذر از ۶ نود به مقصد رسیده ایم! عجیب نیست؟!

شاید بگویید بله عجیب هست، اما آن پراکسی را اثبات نمی کند! پس ادامه مطلب را بخوانید تا در مثال های زده شده، وجود این پراکسی را نیز مشاهده کنید!

## ۲. فیلتر کردن موقع درخواست

و نه موقع پاسخ! البته این شاید برای بسیاری بدیهی به نظر برسد یا از قبل آن را بدانند. به هر حال این پیشنیازی برای دیگر موارد می باشد که باید ذکر شود.

اما درخواست چه چیز؟ dns، https، http یا ... ؟ در همه موارد گفته شده حتی فیلترینگ هوشمند اینستاگرام نیز بر اساس درخواست انجام می گیرد. درباره جزییات هر کدام بعدا توضیح خواهم داد،  اما فعلا با یک درخواست http این فرضیه را امتحان می کنم.

برای اینکه واقعا مشخص شود درخواست به سرور رسیده است یا خیر، یک tcp server ساده که با [node.js](http://nodejs.org/) نوشته شده را روی پورت ۸۰ بر روی یک سرور خارج از کشور ([دیجیتال اوشن](https://www.digitalocean.com/)) راه اندازی کرده ام.

و اما کد آن:

{% highlight js %}
var net = require('net');

var server = net.createServer(function(socket) {

    console.log('new connection! %s:%s', socket.remoteAddress, socket.remotePort);

    socket.on('data', function(data) {
        console.log('Received data: %s', data);
        socket.write('Echo: ' + data + '\n');
    });

});



server.listen(80, '0.0.0.0', function() {
    address = server.address();
    console.log("opened server on %j", address);
});

{% endhighlight %}

حال از وبسایت [vimeo.com](http://vimeo.com) برای تست استفاده می کنیم. این سایت در ایران فیلتر است و برعکس یوتیوب هنوز [dns hijack](https://en.wikipedia.org/wiki/DNS_hijacking) نشده است. (بعدا در مورد آن صحبت خواهم کرد!)

{% highlight bash %}
$ nslookup vimeo.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	vimeo.com
Address: 23.235.37.217
Name:	vimeo.com
Address: 104.156.85.217
Name:	vimeo.com
Address: 23.235.33.217
Name:	vimeo.com
Address: 104.156.81.217
{% endhighlight %}

حالا من یک درخواست http برای سرور tcp که راه اندازی کرده ام، می فرستم:

{% highlight bash %}
$ echo -ne "GET / HTTP/1.1\r\nHost: vimeo.com\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}

و نتیجه آن:

{% highlight html %}
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title></title></head><body><iframe src="http://10.10.34.36?type=Invalid Site&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
{% endhighlight %}

صفحه معروف فیلترینگ!

اما سمت سرور چی داریم؟ جواب: هیچی! حتی لاگی که برای کانکشن جدید هم گذاشته ایم هم وجود ندارد!

## ۳. اتصال TCP بر اساس هدرهای http!

** بروزرسانی ۱: نتایج بدست آمده فقط برای ISP پارس آنلاین صادق است اطلاعات بیشتر:**
https://twitter.com/aliborhani1/status/689447292210786304

موضوع کمی عجیب می شود! بگذارید یک مثال در حالت نرمال بزنم:

می‌خواهید وبسایت گوگل را باز کنید، ابتدا یک درخواست dns می زنید تا آدرس ip را پیدا کنید، سپس یک کانکشن از نوع tcp به ip و پورت مورد نظر (در اینجا ۸۰) برقرار می کنید. حالا درخواست http مورد نظر خودتان را ارسال می کنید، مثلا برای دریافت صفحه اول گوگل:

{% highlight bash %}
GET / HTTP/1.1
Host: google.com

{% endhighlight %}

و سرور گوگل آن را دریافت کرده و پاسخ مناسب را برای شما ارسال می کند. تا اینجا همه چیز نرمال هست، حالا اگر ما یک کانکشن tcp به سایت yahoo.com برقرار کنیم و همین درخواست http را ارسال کنیم، سرور یاهو چه جوابی باید بدهد؟ سرورهای یاهو میزبان سایتی به نام google.com نیستند، پس باید پاسخ یک خطا باشد؟

یعنی به عبارت دیگر چنین دستوری چه جوابی خواهد داشت:

{% highlight bash %}
echo -ne "GET / HTTP/1.1\r\nHost: google.com\r\n\r\n" | netcat yahoo.com 80
{% endhighlight %}


بگذارید آن را تست کنیم، من ابتدا آن را روی سرور خارج از کشور تست می کنم:

{% highlight html %}
HTTP/1.1 404 Not Found on Accelerator
Date: Sat, 02 Jan 2016 14:43:55 GMT
Connection: keep-alive
Via: http/1.1 ir36.fp.gq1.yahoo.com (ApacheTrafficServer)
Server: ATS
Cache-Control: no-store
Content-Type: text/html
Content-Language: en
Y-Trace: BAEAQAAAAABrggahZZRT1gAAAAAAAAAAYjPAw09Q25IAAAAAAAAAAAAFKFrqzXz1AAUoWurNhtOqB9u5AAAAAA--
Content-Length: 1810

<!DOCTYPE html>
<html lang="en-us"><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
    <meta charset="utf-8">
    <title>Yahoo</title>
    <meta name="viewport" content="width=device-width,initial-scale=1,minimal-ui">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
<!-- ... -->
{% endhighlight %}

به نظر منطقی است، شما درخواست صفحه ای از google.com را به سرور یاهو فرستاده اید، در عوض سرور یاهو با ۴۰۴ آن را جواب داده است که یعنی چنین صفحه ای وجود ندارد!

و اما این در ایران به چه صورت است:

{% highlight html %}

HTTP/1.0 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Sat, 02 Jan 2016 14:50:01 GMT
Expires: Mon, 01 Feb 2016 14:50:01 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Connection: close

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>

{% endhighlight %}

سرور گوگل [پاسخ ۳۰۱](https://en.wikipedia.org/wiki/HTTP_301) داده است و گفته است از google.com برو به www.google.com!

این مورد و مورد بعد را قبلا علیرضا شیرازی [اینجا](http://shirazi.blogfa.com/post/388) گفته بود، اما این مشکل هنوز پابرجاست! دلیل آن هم در nc یا telnet و نحوه نگارش آن، زمان و تقسیم شدن پکت ها بر می گردد که به اشتباه به نظر می‌رسد این مشکل حل شده است. در قسمت دوم مقاله به آن اشاره خواهم کرد.


## ۴. دامنه ها دوباره resolve می شوند!

** بروزرسانی ۱: نتایج بدست آمده فقط برای ISP پارس آنلاین صادق است اطلاعات بیشتر:**
https://twitter.com/aliborhani1/status/689447292210786304

و باز هم فرضیه عجیب غریب! البته اگر مورد قبل (۳) را مطالعه کرده باشید، این دیگر نباید برای شما عجیب به نظر برسد! منطقا ما ip گوگل را [resolve](https://en.wikipedia.org/wiki/Domain_Name_System#DNS_resolvers) نکرده ایم تا انتظار جواب از طرف گوگل را داشته باشیم! کسی یا چیزی اینکار را برای ما انجام داده است! پراکسی واسط را به یاد می آورید؟!! اما من این مورد را می‌خواهم به روش دیگر اثبات کنم!

{% highlight bash %}
echo -ne "GET / HTTP/1.1\r\nHost: home.netscape.com:443\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}


اگر به این پورت و به صورت http نه [https (ssl/tls)](https://en.wikipedia.org/wiki/HTTPS) درخواستی ارسال کنید و سرور [netscape](https://en.wikipedia.org/wiki/Netscape) که در حال حاضر به [AOL](http://netscape.aol.com/) منتقل می شود، به شما [خطای زیر](https://technet.microsoft.com/en-us/library/cc957018.aspx) را می دهد:

{% highlight bash %}
read(net): Connection reset by peer
{% endhighlight %}

اما این چه فایده ای داره؟ اینبار به جای [GET](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) از [CONNECT](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods) استفاده می کنم!

{% highlight bash %}
echo -ne "CONNECT home.netscape.com:443 HTTP/1.1\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}

و اما نتیجه:

{% highlight html %}
HTTP/1.0 504 Gateway Time-out
Server: httppd
Date: Sat, 02 Jan 2016 15:30:33 GMT
Content-Type: text/html
Content-Length: 2214

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<head>
		<meta http-equiv="Content-type" content="text/html; charset=iso-8859-1">
		<title>ERROR: The requested URL could not be retrieved</title>
		<style type="text/css">
			#logo {
				margin: 0 auto;
				display: block;
			}
			hr {
				height: 1px;
				border: none;
				background: gray;
				margin: 1em 0;
			}
			h1 {
				padding: 0;
				font-size: 1.5em;
				margin: 0;
			}
			h2 {
				padding: 0;
				font-size: 1.2em;
				margin: 0 0 1em 0;
			}
			body {
				font-family: sans-serif;
				padding: 1em 0;
			}
			#content {
				margin: 0 auto;
				max-width: 800px;
				border: 1px solid gray;
				background: #eee;
				padding: 1em 2em;
				-moz-border-radius: 16px;
			}
		</style>
	</head>
	<body>
		<div id="content">

		<h1>ERROR</h1>
		<h2>The requested URL could not be retrieved</h2>
		<hr/>

<P>
While trying to retrieve the URL:
<A HREF="home.netscape.com:443">home.netscape.com:443</A>
<P>
The following error was encountered:
<UL>
<LI>
<STRONG>
Connection to 195.93.85.44 Failed
</STRONG>
</UL>

<P>
The system returned:
<PRE><I>    (111) Connection refused</I></PRE>

<P>
The remote host or network may be down.  Please try the request again.
<P>Your cache administrator is <A HREF="mailto:webmaster">webmaster</A>.
        </div>
    <!--
  -- Unfortunately, Microsoft has added a clever new
  -- feature to Internet Explorer.  If the text in
  -- an errors message is too small, specifically
  -- less than 512 bytes, Internet Explorer returns
  -- its own error message.  Yes, you can turn that
  -- off, but *surprise* its pretty tricky to find
  -- buried as a switch called smart error
  -- messages  That means, of course, that many of
  -- Resins error messages are censored by default.
  -- And, of course, youll be shocked to learn that
  -- IIS always returns error messages that are long
  -- enough to make Internet Explorer happy.  The
  -- workaround is pretty simple: pad the error
  -- message with a big comment to push it over the
  -- five hundred and twelve byte minimum.  Of course,
  -- thats exactly what youre reading right now.
//-->
</body></html>

{% endhighlight %}

ببین کی جواب داده!؟ قطعا نمی تواند وب سرور ساده ما چنین پاسخ عریض و طویلی را داده باشد! خصوصا اینکه هیچ کانکشن جدیدی نیز لاگ نشده است!

اگر وجود این پراکسی واسط را هنوز انکار می کنید، احتمالا بگویید بر اساس مورد قبل، وب سرور سایتی که در هدر تعریف شده آن را جواب داده است! ممکن است این استدلال درست به نظر برسد، اما به چند دلیل اینگونه نیست:

- اول اینکه پیام خطا حاکی از آن است که اصلا نتوانسته است به مقصد متصل شود.
- دوم اینکه اگر در سرور خارج از کشور این درخواست را ارسال کنید، چنین چیزی را نمی بینید! اصلا جوابی داده نمی شود! اگر از وب سرور سایت مقصد باشد، باید برای بقیه نیز فرقی نداشته باشد.
- من در مورد بعد، با فیسبوک هم این را تست کرده ام، و نتیجه مشابه آن است! خصوصا اگر به مقدار سرور توجه کنید که مشابه هم هستند: httppd
- اگر خطاها را در گوگل سرچ کنید به [squid](http://www.squid-cache.org/) بر خواهید خورد! در مقاله بعدی از روی سورس به شما نشان خواهم داد که این پراکسی واسط همان squid است!

اما بر گردیم به فرضیه خودمان، اگر دقت کنید من به ip سرور درخواست داده ام (فرقی نمی کند اگر شما به 8.8.8.8 یا هر وب سایت دیگر هم این درخواست را ارسال کنید همین نتیجه را می گیرید!) اما در بخشی از پاسخ نوشته شده:

{% highlight html %}
Connection to 195.93.85.44 Failed
{% endhighlight %}

این آی پی از کجا آمده است؟! این همان ip دامنه وارد شده است:

{% highlight bash %}
$ nslookup home.netscape.com

Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
home.netscape.com	canonical name = hostheader.web.aol.com.
hostheader.web.aol.com	canonical name = hostheader.web.aol.com.websys.akadns.net.
Name:	hostheader.web.aol.com.websys.akadns.net
Address: 195.93.85.44
{% endhighlight %}

اما ما این را resolve نکرده ایم، بلکه در بین راه برای ما resolve شده است!


## ۵. پراکسی و فیلترینگ پیازی!

** بروزرسانی ۱: نتایج بدست آمده فقط برای ISP پارس آنلاین صادق است اطلاعات بیشتر:**
https://twitter.com/aliborhani1/status/689447292210786304

پیازی، لایه ای، ماژولار، middleware، decorator، chain of responsibility یا هر چیز مشابه دیگر که فکر می کنید، فیلترینگ چنین ساختاری دارد. یعنی اطلاعات وارد تونلی می شود و هر قسمت از تونل بخشی را کنترل می کند و یا محدود می کند و یا خدمتی برای لایه یا لایه‌های بعدی انجام می دهد.

اثبات چنین حرفی سخت است چون برای ما از بیرون مثل یک جعبه سیاه است. اما اثبات اینکه حداقل ۲ لایه وجود دارد آسان تر است (پراکسی و دروازه).

قبل از اینکه به سراغ اثبات این فرضیه برویم می خواهم نحوه فیلتر شدن فیسبوک (و امثال آن) را نشان بدهم. (در مطالب بعد مفصل تر در مورد آن خواهم گفت).

فیلتر شدن فیسبوک عمدتا از طریق dns hijack صورت می گیرد:

{% highlight bash %}
nslookup facebook.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	facebook.com
Address: 10.10.34.36
{% endhighlight %}

این آدرس، یک آدرس غیر معتبر و داخلی است و صفحه معروف فیلترینگ را نمایش می دهد! همچنین برعکس فیلترینگ سایر کشورها مانند ترکیه، که با تغییر سرور (کش) dns می توان فیلتر را دور زد، در ایران چنین چیزی نیست، تمام درخواست های dns که از طریق پروتکل udp فرستاده می شوند، کنترل می شوند. (بعدا مفصل تر خواهم نوشت).

![Turkey-Censorship](https://blog.cloudflare.com/content/images/2014/10/image05.jpg)
*منبع عکس: [https://blog.cloudflare.com/dnssec-an-introduction](https://blog.cloudflare.com/dnssec-an-introduction)*

اما برویم سراغ پیازمان!

من درخواستی را با CONNECT به فیسبوک یک بار به پورت ۸۰ و بار دیگر به پورت ۴۴۳ ارسال می کنم:

**درخواست CONNECT و پورت ۸۰**
{% highlight bash %}
echo -ne "CONNECT facebook.com:80 HTTP/1.1\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}

پاسخ:

{% highlight html %}
HTTP/1.0 403 Forbidden
Server: httppd
Date: Sat, 02 Jan 2016 16:18:59 GMT
Content-Type: text/html
Content-Length: 2191
Connection: close

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<head>
		<meta http-equiv="Content-type" content="text/html; charset=iso-8859-1">
		<title>ERROR: The requested URL could not be retrieved</title>
		<style type="text/css">
			#logo {
				margin: 0 auto;
				display: block;
			}
			hr {
				height: 1px;
				border: none;
				background: gray;
				margin: 1em 0;
			}
			h1 {
				padding: 0;
				font-size: 1.5em;
				margin: 0;
			}
			h2 {
				padding: 0;
				font-size: 1.2em;
				margin: 0 0 1em 0;
			}
			body {
				font-family: sans-serif;
				padding: 1em 0;
			}
			#content {
				margin: 0 auto;
				max-width: 800px;
				border: 1px solid gray;
				background: #eee;
				padding: 1em 2em;
				-moz-border-radius: 16px;
			}
		</style>
	</head>
	<body>
		<div id="content">

		<h1>ERROR</h1>
		<h2>The requested URL could not be retrieved</h2>
		<hr/>

<P>
While trying to retrieve the URL:
<A HREF="facebook.com:80">facebook.com:80</A>
<P>
The following error was encountered:
<UL>
<LI>
<STRONG>
Access Denied.
</STRONG>
<P>
Access control configuration prevents your request from
being allowed at this time.  Please contact your service provider if
you feel this is incorrect.
</UL>
<P>Your cache administrator is <A HREF="mailto:webmaster">webmaster</A>.
        </div>
    <!--
  -- Unfortunately, Microsoft has added a clever new
  -- feature to Internet Explorer.  If the text in
  -- an errors message is too small, specifically
  -- less than 512 bytes, Internet Explorer returns
  -- its own error message.  Yes, you can turn that
  -- off, but *surprise* its pretty tricky to find
  -- buried as a switch called smart error
  -- messages  That means, of course, that many of
  -- Resins error messages are censored by default.
  -- And, of course, youll be shocked to learn that
  -- IIS always returns error messages that are long
  -- enough to make Internet Explorer happy.  The
  -- workaround is pretty simple: pad the error
  -- message with a big comment to push it over the
  -- five hundred and twelve byte minimum.  Of course,
  -- thats exactly what youre reading right now.
//-->
</body></html>
{% endhighlight %}

**درخواست CONNECT و پورت ۴۴۳**

{% highlight bash %}
echo -ne "CONNECT facebook.com:443 HTTP/1.1\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}

{% highlight html %}
HTTP/1.0 504 Gateway Time-out
Server: httppd
Date: Sat, 02 Jan 2016 16:21:56 GMT
Content-Type: text/html
Content-Length: 2203

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<head>
		<meta http-equiv="Content-type" content="text/html; charset=iso-8859-1">
		<title>ERROR: The requested URL could not be retrieved</title>
		<style type="text/css">
			#logo {
				margin: 0 auto;
				display: block;
			}
			hr {
				height: 1px;
				border: none;
				background: gray;
				margin: 1em 0;
			}
			h1 {
				padding: 0;
				font-size: 1.5em;
				margin: 0;
			}
			h2 {
				padding: 0;
				font-size: 1.2em;
				margin: 0 0 1em 0;
			}
			body {
				font-family: sans-serif;
				padding: 1em 0;
			}
			#content {
				margin: 0 auto;
				max-width: 800px;
				border: 1px solid gray;
				background: #eee;
				padding: 1em 2em;
				-moz-border-radius: 16px;
			}
		</style>
	</head>
	<body>
		<div id="content">

		<h1>ERROR</h1>
		<h2>The requested URL could not be retrieved</h2>
		<hr/>

<P>
While trying to retrieve the URL:
<A HREF="facebook.com:443">facebook.com:443</A>
<P>
The following error was encountered:
<UL>
<LI>
<STRONG>
Connection to 10.10.34.36 Failed
</STRONG>
</UL>

<P>
The system returned:
<PRE><I>    (111) Connection refused</I></PRE>

<P>
The remote host or network may be down.  Please try the request again.
<P>Your cache administrator is <A HREF="mailto:webmaster">webmaster</A>.
        </div>
    <!--
  -- Unfortunately, Microsoft has added a clever new
  -- feature to Internet Explorer.  If the text in
  -- an errors message is too small, specifically
  -- less than 512 bytes, Internet Explorer returns
  -- its own error message.  Yes, you can turn that
  -- off, but *surprise* its pretty tricky to find
  -- buried as a switch called smart error
  -- messages  That means, of course, that many of
  -- Resins error messages are censored by default.
  -- And, of course, youll be shocked to learn that
  -- IIS always returns error messages that are long
  -- enough to make Internet Explorer happy.  The
  -- workaround is pretty simple: pad the error
  -- message with a big comment to push it over the
  -- five hundred and twelve byte minimum.  Of course,
  -- thats exactly what youre reading right now.
//-->
</body></html>
{% endhighlight %}

در مثال بالا با پورت ۸۰ مستقیما خطای ۴۰۳ دریافت کرده ایم. البته این به معنی آن نیست که مربوط به فیلترینگ باشد. چون به هر درخواست با پورت غیر از ۴۴۳ هم همین خطا را می دهد! جالب تر آن که اگر پورتی را در نظر نگیرید، به صورت پیشفرض به پورت ۴۴۳ درخواست می دهد:

{% highlight bash %}
echo -ne "CONNECT facebook.com HTTP/1.1\r\n\r\n" | netcat <server-ip> 80
{% endhighlight %}

حال وقتی پورت ۴۴۳ است آدرس ip از سرور dns دریافت شده، سپس موقع برقراری کانکشن به 10.10.34.36:443 به مشکل برخورده و نتوانسته است نتیجه را دریافت کند در نتیجه خطای 504 Gateway Time-out را داده است. دلیل آن هم مشخص است پورت ۴۴۳ روی 10.10.34.36 باز نیست!

{% highlight bash %}
nmap 10.10.34.36 -p443

Starting Nmap 6.47 ( http://nmap.org ) at 2016-01-02 20:10 IRST
Nmap scan report for 10.10.34.36
Host is up (0.18s latency).
PORT    STATE  SERVICE
443/tcp closed https

Nmap done: 1 IP address (1 host up) scanned in 2.47 seconds
{% endhighlight %}


این تست با متدهای دیگر http هم نتایج جالبی دارد! مثلا با GET به جای ۴۰۳ پاسخ ۲۰۰ گرفته می شود! با محتویات صفحه http://10.10.34.36.

یا برای دامنه گوگل:
{% highlight bash %}
$ echo -ne "CONNECT google.com HTTP/1.1\r\n\r\n" | netcat <server-ip> 80
HTTP/1.0 200 Connection established

^C%
{% endhighlight %}


شاید باز هم اثبات این فرضیه سخت باشد، اما نتایج بالا نشان می دهد برای پورت ها و درخواست های متخلف، منابع پاسخگو متخلفی وجود دارند و یا حتی جنس پاسخ ها فرق می کند. این می تواند نشان دهنده این باشد که هر لایه وظیفه رسیدگی به یک یا چند نوع درخواست می باشد. همچنین پراکسی واسط هنگامی که پورت ۴۴۳ بود، اطلاعی از فیلتر بودن وبسایت فیسبوک نداشت، آدرس ip را resolve کرده بود و نتوانسته بود به آن متصل شود! در حقیقت این می تواند نشان دهنده این باشد که میزان اطلاعات هر لایه (در اینجا پراکسی واسط) محدود است.


## قسمت دوم ...
در قسمت دوم درخواست http را تغییر خواهیم داد تا ببینیم چه پاسخ یا پاسخ‌هایی دریافت خواهیم کرد! نشان می دهیم این پراکسی همان squid است! بیشتر با پورت ۸۰ آشنا می شویم! همچنین ارسال هدر در چند بسته یا سگنت و یا زمان ارسال آن چه تاثیری بر پاسخ خواهد داشت. منتظر قسمت دوم باشید!
