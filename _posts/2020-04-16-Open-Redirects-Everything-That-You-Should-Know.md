---
title: "Open Redirects - Everything That You Should Know"
layout: post
---

![banner](https://i.ibb.co/7trzHRB/g1632.png)

Hey There! In this post I'll be going to explain everything that is necessary for a layman(not really) to understand Open Redirects. Let's start!


> Open Redirect or Open Redirection is a situation in which a website redirects or sends the user to another website by taking parameter value as the destination. 

Example:
- URL - `http://site.com/redir?url=http://www.google.com`
- parameter name - `url`
- parameter value - `http://www.google.com`
- destination(the webiste to which you will be redirected) - `http://www.google.com`

### Javascript Based Redirection

The URL - `http://site.com/redir?url=http://www.google.com` will send you or redirect you to `http://www.google.com`.

Now, let's take a look at the code which is the cause of our redirection.

{% highlight javascript %}
var url = 'http://site.com/redir?url=http://www.google.com';
var param = new URL(url);
window.location = param.searchParams.get('url');
{% endhighlight %}

What's happening is that, the code is taking the parameter value from the URL which is `http://www.google.com` and then it's assigning the value to <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/location" target="_blank">`window.location`</a> and that's how you are getting redirected to `http://www.google.com`. And this is what we call a - Javascript Based Redirection.

`window.location` is the sink here, whereas `param.searchParams.get('url');` is the source.

**Note:** When you're trying to fuzz the parameters, remember that Javascript Based Redirections give you `200` and not `3xx` as a response code. Also, it's usefulness is only restricted to DOM XSS.

### Header Based Redirection

Header Based Redirection are the redirections triggered by the server side scripts like php, java, etc. And, this redirection is the OG as it gives `3xx` as a response code and it can uplifted up to make SSRFs work.

Let's see an example php code that does this redirection :
{% highlight php %}
$redirect_URL = $_GET["url"];
header("Location:".$redirect_URL);
{% endhighlight %}
As usual, the parameter value is getting stored into the location header which leads us to our redirection. It can be chained with vulnerabilities like SSRF, OAuth token disclosure and CLRF Injection. It can also be used for phising.

Functionalities you should look upto are - login, signup, register, logout.

### List of Quality Bypasses

Here's a short list of bypasses(payloads) that I've collected from this <a href='https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html' target=_black>source</a> after going through some HackerOne reports and have tried on different targets to bypass the filters.

- `https:www.google.com`
- `HtTp://google.com`
- `http\x3A\x2F\x2Fgoogle.com`
- `//google。com`
- `x00http://google.com`
- `////216.58.214.206`
- `/\216.58.214.206`
- `x20http://www.google.com`
- `https://www.google.com`
- `hthttp://tp://www.google.com`
- `。/www.google.com`


### Dorks & Parameter Names

Some useful google dorks:

- `site:target.com AND inurl:url=http(s)`
- `site:target.com AND inurl:u=http(s)`
- `site:target.com AND inurl:redirect?http(s)`
- `site:target.com AND inurl:redirect=http(s)`
- `site:target.com AND inurl:link=http(s)`

Some parameter names that need attention while looking for Open Redirects from <a href="https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html" target=_black>Pentester Land</a>:

- `?next=`
- `?url=`
- `?dest=`
- `?redirect=`
- `?returnTo=`
- `?go=`
- `?redirect_uri`
- `?continue=`
- `?return_path=`
- `?externalLink=`
- `?URL=`

More Resources -

- <a href='https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html' target=_blank>https://pentester.land/cheatsheets/2018/11/02/open-redirect-cheatsheet.html</a>
- <a href='https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.md' target=_blank>https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.md</a>
- <a href='https://blog.detectify.com/2019/05/16/the-real-impact-of-an-open-redirect/' target=_blank>https://blog.detectify.com/2019/05/16/the-real-impact-of-an-open-redirect/</a>


That's all for this post, you can reach out to me on <a href="https://twitter.com/0xNanda">twitter</a> and tell if you liked it or not, suggestions and criticism would be welcomed.