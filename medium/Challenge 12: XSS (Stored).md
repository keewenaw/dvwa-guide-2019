<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_s/
<br>
<b>Objective:</b> Redirect everyone to a web page of your choosing.
<br>
<b>Tools needed:</b> A temporary web server, Burp Suite
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2012:%20XSS%20(Stored).md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssssource.png" width="500">

We see that our former attack vector, the "Message" field, has been locked down pretty well, with multiple functions for input sanitization. However, the "Name" field didn't get the same treatment, only being subjected to a simple, case-sensitive removal of <code>&#60;script&#62;</code>. <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/Challenge%2011:%20XSS%20(Reflected).md" target="_blank">Sound familiar</a>?

<h3><b>Crafting a New Exploit</b></h3>

So we note, like last time, that the improperly-sanitized "Name" field truncates all input to a maximum of 10 characters. However, that's only on the client-side form. If we can intercept the HTTP request in Burp Suite and insert our modified exploit in the "Name" field there, would that work? Let's find out.

Here's our input:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xsssburprepeaterin.png" width="500">

Which seems to work ...

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xsssburprepeaterout.png" width="700">

And if we refresh the page ...

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssssuccess.png" width="500">

We successfully injected our payload and get redirected! We even found a faster way than our easy mode solution!

Challenge complete!
