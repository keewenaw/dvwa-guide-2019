<b>Challenge URL:</b> /dvwa/vulnerabilities/csrf/
<br>
<b>Objective:</b> Make the current user change their own password, without them knowing about their actions, using a CSRF attack.
<br>
<b>Tools needed:</b> Burp Suite
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2003:%20CSRF.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/csrfsource.png" width="500">

The new code checks to see if the HTTP-Referer header matches the server's name (ie, the HTTP request "comes from" the same server as the newly-requested page). If so, it assumes the request is valid and carries on. Otherwise, it produces and error.

<h3><b>Crafting a New Exploit</b></h3>

Rather than show you the exact steps to follow, we'll make this a summary of how to use this in the real world. So pretty clearly, if we can set the HTTP-Referer header to the value "dvwa" or "http&#58;//dvwa/dvwa/vulnerabilities/csrf/", we can circumvent this new check. Since we generally can't force the end user to go to the password reset page first, we can instead try crafting a custom HTTP request in Javascript via the <code><a href="https://developer.mozilla.org/en-US/docs/Web/API/Document/referrer" target="_blank">document.referrer</a></code> property. 

For our exploit, we can create a malicious script and trick the end user into visiting and executing it via some social engineering or another exploit. That malicious script would first set <code>document.referrer</code> to the vulnerable server, then would execute our previous payload, setting the password to whatever we specify. As before, there is a lot of luck at play, so it's not guaranteed to work.

Challenge complete.
