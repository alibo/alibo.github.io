---
layout: post
comments: true
title: فیلترینگ ایران چگونه کار می‌کند؟! - قسمت دوم
tags: فیلترینگ
---


## ۷. فیلتر براساس مقدار Host:
یکی از ویژگی‌های خوب پروتکل HTTP این هست که می توانید چندین سایت را بر روی یک سرور میزبانی کنید چون در [RFC]() آن تعریف شده که مقدار فیلد Host را حتما در درخواست قرار دهید. در نتیجه می توانید دامنه مورد نظر خودتان را در درخواست قرار داده و وب سرور تشخیص می دهد که این درخواست مربوط به کدام وب سایت است. اما همین موضوع موضوع اصلی فیلترینگ را تشکیل می دهد! اکثر وبسایت‌ها از روی مقدار فیلد Host فیلتر می شوند. در مثال زیر درخواست به سرور bing داده شده و مقدار Host هم bing.com قرار داده شده، همانطور که انتظار داشتیم وب سرور bing جواب می دهد و از ما می خواهد که به www.bing.com برویم! 

``` bash
$ echo -ne "GET / HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80

HTTP/1.0 301 Moved Permanently
Location: http://www.bing.com/
Server: Microsoft-IIS/8.5
X-MSEdge-Ref: Ref A: 8338052CCCF84E4A914F5EF55226C305 Ref B: DFDD5198A41D0B3C8F78E5BE9D5ED858 Ref C: Tue Jan 19 03:27:48 2016 PST
Date: Tue, ** Jan 2016 **:**:** GMT
Content-Length: 0
Connection: close
```

اما اگر با dropbox این را تست می کنیم:

``` bash
$ echo -ne "GET / HTTP/1.1\r\nHost: dropbox.com\r\n\r\n" | netcat dropbox.com 80
```
و جواب:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title></title></head><body><iframe src="http://10.10.34.36?type=Invalid Site&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```
صفحه فیلترینگ! اما این به معنی این نیست که تمام subdomain های آن هم فیلتر شده باشد به عنوان مثال www فیلتر است:

``` bash
$ echo -ne "GET / HTTP/1.1\r\nHost: www.dropbox.com\r\n\r\n" | netcat dropbox.com 80
```

اما برای www2، blog و api فیلتر نیست:

``` bash
echo -ne "GET / HTTP/1.1\r\nHost: www2.dropbox.com\r\n\r\n" | netcat dropbox.com 80
```

``` bash
echo -ne "GET / HTTP/1.1\r\nHost: api.dropbox.com\r\n\r\n" | netcat dropbox.com 80
```


``` bash
echo -ne "GET / HTTP/1.1\r\nHost: blog.dropbox.com\r\n\r\n" | netcat dropbox.com 80
```
مثلا برای درخواست آخر جواب این است:

``` html
HTTP/1.0 301 Moved Permanently
Server: CloudFront
Date: Tue, ** Jan 2016 **:**:** GMT
Content-Type: text/html
Content-Length: 183
Location: https://blog.dropbox.com/
X-Cache: Redirect from cloudfront
Via: 1.1 53657f22d99084ad547a21392858391b.cloudfront.net (CloudFront)
X-Amz-Cf-Id: p********_*******************************************Q==
Connection: close

<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>CloudFront</center>
</body>
</html>
```
## ۷. فیلتر کلمات کلیدی در  path برای سایت های خارج از کشور

علاوه بر فیلترینگ بر مقدار فیلد Host، بر اساس path یا مسیر صفحه نیز فیلترینگ داریم! بگذارید با یک مثال آن را نشان دهم:

``` bash
$ echo -ne "GET /xxx HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat 
```

و جواب:

``` html
bing.com 80
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title>FR1-6
</title></head><body><iframe src="http://10.10.34.34?type=Invalid Keyword&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```

اگر در آدرس iframe هم دقت کنید مقدار `type=Invalid Keyword` نوشته شده! (شاید برای آمارگیری؟!؟) 

اما شاید بگویید این فرق مربوط به بعضی از سایت‌ها مثل موتورهای جستجو است. بگذارید این بار با apple تست کنیم:

``` bash
$ echo -ne "GET /xxx HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

و جواب:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title>FR1-8
</title></head><body><iframe src="http://10.10.34.34?type=Invalid Keyword&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```

حتی اگر این مقدار در وسط مسیر باشد و یا با کاراکترهایی مثل + یا space جدا شده باشند نیز فیلتر می شود:

``` bash
echo -ne "GET /search/xxx/page/1 HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

``` bash
echo -ne "GET /search/salam+xxx/page/1 HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

``` bash
echo -ne "GET /search/salam   xxx    /page/1 HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

این مورد را قبلا جادی هم در این [پست]() اشاره کرده است!

اینکه چه کلیدواژه‌هایی فیلتر می شوند مشخص نیست! ولی نکته جالب تر اینکه علاوه بر کلیدواژه ها، دامنه ها نیز اگر در آدرس مسیر باشند، فیلتر می شوند!

``` bash
"GET /search/facebook.com/page/1 HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

``` bash
"GET /search/www.dropbox.com/page/1 HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```
جواب هر دوی این ها:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title></title></head><body><iframe src="http://10.10.34.36?type=&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```

اما دامنه‌هایی که توی قسمت قبلی فیلتر نشدند اینجا هم فیلتر نمی شوند:

``` bash
$ echo -ne "GET /search/www2.dropbox.com/page/1 HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80

HTTP/1.0 301 Moved Permanently
Server:
Date:
Referer:
Location: http://www.apple.com/search/www2.dropbox.com/page/1
Content-Type: text/html
Connection: close
```

**اما فقط سرورهای خارجی!**
 برگردیم به قسمت دوم فرضمون! تمام اینها تنها برای سرورهای خارجی درست است، برای اینکار من از وبسایت های دیجیکالا، آپارات، دیجیاتو و شهرداری تهران و یک سرور شخصی داخل ایران موارد بالا را دوباره تست کردم، برای خلاصه تر شدن فقط آپارات را در مثال ها آوردم:

``` bash
$ echo -ne "GET /search/www.dropbox.com/page/1 HTTP/1.1\r\nHost: aparat.com\r\n\r\n" | netcat aparat.com 80
```

``` bash
echo -ne "GET /xxx HTTP/1.1\r\nHost: aparat.com\r\n\r\n" | netcat aparat.com 80
```

``` bash
echo -ne "GET /search/salam+xxx/page/1 HTTP/1.1\r\nHost: aparat.com\r\n\r\n" | netcat aparat.com 80
```
جواب درخواست اول و مشابه دو تای دیگر:

``` bash
HTTP/1.0 301 Moved Permanently
Date: Tue, ** Jan 2016 **:**:** GMT
Content-Type: text/html
X-Powered-By: Aparat Framework/1.0.1
Location: http://www.aparat.com/search/www.dropbox.com/page/1
Vary: Accept-Encoding
Server: Toofun/1.0.1
Set-Cookie: apr_lb_id=m1; path=/; domain=.aparat.com
Cache-Control: private
Connection: close
```

## ۸. فیلتر کلمات کلیدی در query string ها برای سایت های خارجی

مانند مورد قبل در مورد query string ها هم فیلترینگ براساس کلید واژه ها هم وجود دارد:

``` bash
$ echo -ne "GET /search?baz=biz&p=xxx&foo=bar HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

``` bash
$ echo -ne "GET /search?baz=biz&p=dropbox.com&foo=bar HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

و جواب هر دو:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title>FR1-8
</title></head><body><iframe src="http://10.10.34.34?type=Invalid Keyword&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```
**اما فقط سرورهای خارجی!**
در اینجا هم فقط سایت‌هایی که بر روی سرورهای خارج از کشور میزبانی می شوند، این نوع فیلترینگ اعمال می شود:

``` bash
$ echo -ne "GET /search?baz=biz&p=xxx&foo=bar HTTP/1.1\r\nHost: aparat.com\r\n\r\n" | netcat aparat.com 80
```

``` bash
$ echo -ne "GET /search?baz=biz&p=dropbox.com&foo=bar HTTP/1.1\r\nHost: aparat.com\r\n\r\n" | netcat aparat.com 80
```

جواب درخواست آخر و مشابه درخواست اول:

``` bash
HTTP/1.0 301 Moved Permanently
Server: nginx
Date: Tue, 19 Jan 2016 12:34:44 GMT
Content-Type: text/html
X-Powered-By: Aparat Framework/1.0.1
Location: http://www.aparat.com/search?baz=biz&p=dropbox.com&foo=bar
Vary: Accept-Encoding
X-XSS-Protection: 1; mode=block
X-Content-Options: nosniff
Set-Cookie: apr_lb_id=m6; path=/; domain=.aparat.com
Cache-Control: private
Connection: close
```


## ۹. کلیدواژه‌های خاص برای دامنه‌های خاص!

در حین تست‌هایی که انجام می‌دادم، بعضی از کلیدواژه‌ها مثل sex رفتار متفاوتی داشتند به عنوان مثال درخواست های زیر:

``` bash
echo -ne "GET /sex HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

``` bash
echo -ne "GET /?baz=biz&q=sex&foo=bar HTTP/1.1\r\nHost: bing.com\r\n\r\n" | netcat bing.com 80
```

``` bash
echo -ne "GET /?baz=biz&q=sex&foo=bar HTTP/1.1\r\nHost: google.com\r\n\r\n" | netcat google.com 80
```

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: ask.com\r\n\r\n" | netcat ask.com 80
```

``` bash
echo -ne "GET /sex HTTP/1.1\r\nHost: ask.com\r\n\r\n" | netcat ask.com 80
```

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: yahoo.com\r\n\r\n" | netcat yahoo.com 80
```

درخواست های بالا همه فیلتر شده اند:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title></title></head><body><iframe src="http://10.10.34.36?type=Invalid Pattern&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```

اما درخواست های زیر خیر:

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: google.com\r\n\r\n" | netcat google.com 80
```

جواب:

``` html
HTTP/1.0 301 Moved Permanently
Location: http://www.google.com/?baz=biz&p=sex&foo=bar
Content-Type: text/html; charset=UTF-8
Date: Tue, 19 Jan 2016 13:23:10 GMT
Expires: Thu, 18 Feb 2016 13:23:10 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 249
X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
Connection: close

<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/?baz=biz&amp;p=sex&amp;foo=bar">here</A>.
</BODY></HTML>
```

در مثال بالا مشخص است برای وبسایت گوگل فقط `q` فیلتر می شود ولی این برای وبسایت هایی مثل ask و bing صادق نیست!

جالب تر اینکه وبسایت یاهو نتیجه برعکس با گوگل دارد! یعنی `q` فیلتر نیست اما `p` فیلتر می شود!

``` bash 
echo -ne "GET /?baz=biz&q=sex&foo=bar HTTP/1.1\r\nHost: yahoo.com\r\n\r\n" | netcat yahoo.com 80
```

جواب:

``` html
HTTP/1.0 301 Moved Permanently
Date: Tue, 19 Jan 2016 13:29:16 GMT
Via: https/1.1 ir39.fp.gq1.yahoo.com (ApacheTrafficServer)
Server: ATS
Location: https://www.yahoo.com/?baz=biz&q=sex&foo=bar
Content-Type: text/html
Content-Language: en
Cache-Control: no-store, no-cache
Y-Trace: BAEAQAAAAADae076.UzsZAAAAAAAAAAAcdmGw2dxkKkAAAAAAAAAAAAFKa_bJNHKAAUpr9sk1207_6T2AAAAAA--
Content-Length: 393
Connection: close

<HTML>
<HEAD>
<TITLE>Error</TITLE>
</HEAD>

<BODY BGCOLOR="white" FGCOLOR="black">
<!-- status code : 301 -->
<!-- Error: GET -->
<!-- host machine: ir39.fp.gq1.yahoo.com -->
<!-- timestamp: 1453210156.000 -->
<!-- url: http://yahoo.com/?baz=biz&q=sex&foo=bar-->
<H1>Error</H1>
<HR>

<FONT FACE="Helvetica,Arial"><B>
Description: Could not process this "GET" request.
</B></FONT>
<HR>
</BODY>
```

و این درخواست ها هم هیچ کدام فیلتر نمی شوند:

``` bash
echo -ne "GET /sex HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

``` bash 
echo -ne "GET /?baz=biz&q=sex&foo=bar HTTP/1.1\r\nHost: apple.com\r\n\r\n" | netcat apple.com 80
```

جواب درخواست آخر و مشابه بقیه:

``` bash
HTTP/1.0 301 Moved Permanently
Server:
Date:
Referer:
Location: http://www.apple.com/?baz=biz&q=sex&foo=bar
Content-Type: text/html
Connection: close
```

برای ویکیپدیا:

``` bash
echo -ne "GET /?baz=biz&p=sex&foo=bar HTTP/1.1\r\nHost: wikipedia.org\r\n\r\n" | netcat wikipedia.org 80
```

``` bash
echo -ne "GET /?baz=biz&q=sex&foo=bar HTTP/1.1\r\nHost: wikipedia.org\r\n\r\n" | netcat wikipedia.org 80
```

``` bash
echo -ne "GET /sex HTTP/1.1\r\nHost: wikipedia.org\r\n\r\n" | netcat wikipedia.org 80
```

جواب درخواست آخر و مشابه بقیه:

``` bash
HTTP/1.0 301 Moved Permanently
Server: Varnish
Location: https://wikipedia.org/sex
Content-Length: 0
Accept-Ranges: bytes
Date: Tue, ** Jan 2016 **:**:** GMT
X-Varnish: 3231138038
Age: 0
Via: 1.1 varnish
X-Cache: cp3007 frontend (0)
Set-Cookie: WMF-Last-Access=19-Jan-2016;Path=/;HttpOnly;Expires=Sat, ** Feb 2016 12:00:00 GMT
X-Client-IP: ***.***.***.***
Set-Cookie: GeoIP=IR:**:**********:***.***:***.***:v4; Path=/; Domain=.wikipedia.org
Connection: close
```


همانطور که مشخص است با رفتار عجیبی برای بعضی از کلمات کلیدی مواجه هستیم! شاید اینکار برای جلوگیری از فیلتر محتوای آموزشی در سایت های دیگر با محتوای پورن انجام شده! شاید مربوط به کدهای قدیمی فیلترینگ است! دلیل قطعی آن هنوز برای من مشخص نیست همچنین چه کلمات کلیدی دیگری چنین رفتاری دارند.

## ۱۰. تنها اولین مقدار Host

اگر کمی غیر استاندارد کار کنیم و به جای یک Host از دو فیلد Host استفاده کنیم، سیستم فیلترینگ طبق انتظار فقط اولین آن را در نظر می گیرد ( و اگر مطلب قبلی را مطالعه کرده باشید به آن دوباره درخواست جدید می دهد!)

``` bash
echo -ne "GET / HTTP/1.1\r\nHost: google.com\r\nHost: dropbox.com\r\n\r\n" | netcat dropbox.com 80
```

در درخواست بالا چون اولین مقدار Host برابر  google.com است، پروکسی واسط به google.com درخواست داده و پاسخ از سمت سرور گوگل می‌آید:

``` html
HTTP/1.0 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Tue, 19 Jan 2016 14:34:37 GMT
Expires: Thu, 18 Feb 2016 14:34:37 GMT
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
```

اما اگر عبارت بالا را برعکس کنیم چه می شود:

``` bash
echo -ne "GET / HTTP/1.1\r\nHost: dropbox.com\r\nHost: google.com\r\n\r\n" | netcat dropbox.com 80
```

و جواب:

``` html
HTTP/1.0 403 Forbidden
Connection: close

<html><head><meta http-equiv="Content-Type" content="text/html; charset=windows-1256"><title></title></head><body><iframe src="http://10.10.34.36?type=Invalid Site&policy=MainPolicy " style="width: 100%; height: 100%" scrolling="no" marginwidth="0" marginheight="0" frameborder="0" vspace="0" hspace="0"></iframe></body></html>
```
بله فیلترینگ! 

**سوال:** با توجه به مثال‌های گفته شده، فکر می کنید جواب این درخواست چیست و از کدام سرور جواب داده می شود؟!

``` bash
echo -ne "GET / HTTP/1.1\r\nHost: google.com\r\nHost: bing.com\r\n\r\n" | netcat yahoo.com 80
```

## تنها فیلتر کردن درخواست های GET و POST

## ۶. تغییر پورت مبدا و TProxy

در [ساختار سگمنت TCP]() پورت مبدا و مقصد تعریف شده تا دریافت کننده و ارسال کننده پکت در کامپیوترهای میزبان مشخص شود. اما هنگامی که از NAT یا پروکسی استفاده می شود پورت مبدا عموما تغییر می کند. همچنین IP نیز تغییر می کند. برای اینکار از [TProxy]() استفاده می شود تا مشخصات پکت اصلی ثابت بماند. اما متاسفانه در مورد فیلترینگ ایران با وجود اینکه IP ثابت می ماند پورت مبدا تغییر می کند. برای اینکار اینبار از دو کد ساده با Node.js برای اتصال tcp سرور و کلاینت استفاده کرده ام. کد سرور که در پست قبلی هم استفاده شد:

``` js
var net = require('net');

var server = net.createServer(function(socket) {

    console.log('new connection! %s:%s', socket.remoteAddress, socket.remotePort);

    socket.on('data', function(data) {
        console.log('Received data: %s', data);
        socket.write('Echo: ' + data + '\n');
    });

});

server.listen(process.argv[2], '0.0.0.0', function() {
    address = server.address();
    console.log("opened server on %j", address);
});
```

و کد سمت کلاینت:

``` js
var net = require('net');

var client = new net.Socket();

var options = {
	'host': process.argv[2],
	'port': process.argv[3],
	'localPort': parseInt(process.argv[4])
};

client.connect(options, function() {
	console.log('Connected - local port: ' + process.argv[4]);

	console.log('Requesting:');
	client.write('GET / HTTP/1.1\r\nHost: <server-ip>\r\nConnection: keep-alive\r\n\r\n');
});

client.on('data', function(data) {
	console.log('Received: ' + data);
});

client.on('close', function() {
	console.log('Connection closed');
});

client.on('error', function(err) {
	console.log(err);
});
```

به جای `<server-ip>` آدرس ip سرور قرار می دهیم. حال با دستور زیر کد را سمت کلاینت اجرا می کنیم:

``` bash
node client.js <server-ip> 80 40001
```

به پورت ۸۰ سرور از پورت ۴۰۰۰۱ در سیستم فعلی، متصل شده ام و اما نتیجه در سمت سرور:

``` bash
new connection! ***.***.***.***:51690
Received data: GET / HTTP/1.1
Host: ***.***.***.***
Cache-Control: max-age=0
Connection: keep-alive
```

از پورت ۴۰۰۰۱ به ۵۱۹۶۰ تغییر کرده!! اگر من چندین بار این را اجرا کنم با پورت های مختلفی به سرور متصل شده ام:

``` bash
new connection! ***.***.***.***:3129
```

``` bash
new connection! ***.***.***.***:13241
```

نکته جالب تر اینکه این فقط برای پورت ۸۰ صادق است! به عنوان مثال من سرور و کلاینت را با پورت ۸۱ و ۱۰۸۰ دوباره امتحان می کنم:

``` bash
node client.js <server-ip> 81 40001
```

سمت سرور:

``` bash
new connection! ***.***.***.***:40001
Received data: GET / HTTP/1.1
Host: ***.***.***.***
Connection: keep-alive
```

و حالا پورت ۱۰۸۰:

``` bash
node client.js <server-ip> 1080 40001
```

سمت سرور:

``` bash
new connection! ***.***.***.***:40001
Received data: GET / HTTP/1.1
Host: ***.***.***.***
Connection: keep-alive
```

هر دوی این ها پورت مبدا درستی دارند.

## ۶. فیلتر کردن براساس هدر حتی پس از درخواست اول

## ۷. بافر کردن هدر و timeout


## بازی با درخواست های http و نتایج آن

## پورت ۸۰ پورتی خاص!
- buffer
- query string
- path

