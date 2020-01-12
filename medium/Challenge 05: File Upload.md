<b>Challenge URL:</b> /dvwa/vulnerabilities/exec/
<br>
<b>Objective:</b> Execute any PHP function of your choosing on the target system (such as phpinfo() or system()) thanks to this file upload vulnerability.
<br>
<b>Tools needed:</b> A reverse shell, netcat, Burp Suite
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2005:%20File%20Upload.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/uploadsource.png" width="500">

We see that some verification code has been added, ensuring that the uploaded file has a type of "jpg" or "png".

<h3><b>Crafting a New Exploit</b></h3>

The exploit here is pretty easy - as long as we can spoof or modify the file type in our HTTP request, we should be good to go! We even have the perfect tool for this, Burp Suite.

In Burp Suite, let's enable traffic interception ("Proxy" > "Intercept" > "Intercept is On" button is enabled). Let's then try uploading our shell from before, <code>php-reverse-shell.php</code>. When you see your request intercepted, send it to the Burp Repeater by clicking "Action" > "Send to Repeater". There, look for the <b>Content-Type</b> header, and change it from <code>application/x-php</code> to <code>image/png</code>.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/uploadrepeater.png" width="500">

Click the "Go" button when done. In the "Response" section, navigate to the "Render" tab. We can then see the same message as before, meaning our shell was uploaded successfully! 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/uploadrender.png" width="500">

You can turn off Burp interception now. If we start <code>netcat</code>, then navigate to the uploaded file in our browser, we should get a shell:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/uploadsuccess.png" width="500">

Great, we have a shell we can interact with. Challenge complete!
