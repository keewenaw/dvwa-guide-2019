<b>Challenge URL:</b> /dvwa/vulnerabilities/captcha/
<br>
<b>Objective:</b> Change the current user's password in a automated manner because of the poor CAPTCHA system.
<br>
<b>Tools needed:</b> Burp Suite
<br>
<b>Additional notes:</b> This module requires keys for Captcha v2. V3 will not work.
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2006:%20Insecure%20CAPTCHA.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by running through the new workflow in Burp Suite, as before.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/cburptest.png" width="500">

So we see the addition of a new POST parameter called <b>passed_captcha</b>, which gets set to "true" if we pass the Captcha check.

Checking the challenge source code seems to confirm that's all the additional verification that occurs:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/csource.png" width="500">

<h3><b>Crafting a New Exploit</b></h3>

It seems we can trivially replicate the same attack as before, as long as we include the string <code>&passed_captcha=true</code> in our parameters. So let's send our new request to the Repeater and try that:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/cburpredo.png" width="1000">

We got the success message, so let's logout and login again with our new password:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/clogout.png" width="500">

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/clogin.png" width="500">

It worked! Challenge complete!
