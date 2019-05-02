<b>Challenge URL:</b> /dvwa/vulnerabilities/csp/
<br>
<b>Objective:</b> Bypass Content Security Policy (CSP) and execute JavaScript in the page.
<br>
<b>Tools needed:</b> Burp Suite (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Discovering the CSP/b></h3>

Okay, here's what we see:

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/cspform.png" width="500">

We know from the first link that we can discover the current CSP by viewing the HTTP response header the server sends back to us. We can do this via Burp Suite, and I'd actually recommend that. Firefox also allows us to view headers via the Firefox developer console. Since it takes less time to configure, let's pull the developer console up and navigate to the "Network" tab. 

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/cspnetworktab.png" width="500">

It immediately tells us to perform a request, so why don't we input a test URL in the form and see what happens?

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/cspnetworktest.png" width="500">

We see a bunch of 200 response codes, which helps. Let's double click on the POST request to see what we learn.

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/cspfindcsp.png" width="500">

In the bottom right corner, we see a response header called "Content-Sewcurity-Policy". That's what we need! Examing the contents tells us the CSP:

<code>script-src 'self' https://pastebin.com  example.com code.jquery.com https://ssl.google-analytics.com ;</code>

This tells us we can load Javascript from the following locations:
<ul>
  <li>Scripts from within DVWA (<a href="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/Challenge%2005:%20File%20Upload.md" target="_blank">Challenge 5</a>, anyone?);</li>
  <li>Anything hosted on the domain pastebin.com;</li>
  <li>Anything hosted on the domain example.com;</li>
  <li>Anything hosted on the domain code.jquery.com; or</li>
  <li>https://ssl.google-analytics.com/.</li>
</ul>

We could upload a Javascript file using the upload feature of Challenge 5, but we already did something similar. Let's try something else. Of the other options, Pastebin allows us to upload any files we want, anonymously. Sounds good to me!

<h3><b>Bypassing the CSP/b></h3>

Let's upload some example code to Pastebin.

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/csppastebinupload.png" width="500">

By way of explanation, I uploaded the code snippet <code>console.log("CSP Bypass");</code> to Pastebin. This snippet will make the string "CSP Bypass" visible in our browser's console log, if our attack works. I told Pastebin that I uploaded a Javascript file ("Syntax Highlighting"), made the upload an unlisted post ("Post Exposure"), and told Pastebin to delete the upload after an hour ("Paste Expiration"). 

You should get a success screen similar to this:

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/csppastebinuploadsuccess.png" width="500">

Click the "Raw" button above the top right corner of the "RAW Paste Data" field. 

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/csppastebinuploadraw.png" width="500">

This removes all the Pastebin-added stuff and give us only what we uploaded. That's what we can use to check if our exploit works!

Let's navigate back to the challenge and open up the developer console (Press the keys<code>Control+Shift+K</code> simultaneously). Enter our raw Pastebin link into the form field. Click the "Include" button and check the console log.

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/cspsuccess.png" width="500">

We did it! Our message was successfully logged to the console and we can see it. Challenge complete!
