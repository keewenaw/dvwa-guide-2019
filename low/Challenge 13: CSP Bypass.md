<b>Challenge URL:</b> /dvwa/vulnerabilities/csp/
<br>
<b>Objective:</b> Bypass Content Security Policy (CSP) and execute JavaScript in the page.
<br>
<b>Tools needed:</b> Burp Suite (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Discovering the CSP</b></h3>

Okay, here's what we see:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/cspform.png" width="500">

We know from the first link under "More Information" that we can discover the current CSP by viewing the HTTP response header the server sends back to us. We can do this via Burp Suite, and I'd actually recommend that due to the multiple useful features Burp offers. Firefox also allows us to view headers via the Firefox developer console. Since it takes less time to configure, let's pull the developer console up by pressing the keyboard keys <code>Control+Shift+K</code> simultaneously. Navigate to the "Network" tab. 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/cspnetworktab.png" width="700">

It immediately tells us to perform a request, so why don't we input a test URL in the form and see what happens?

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/cspnetworktest.png" width="700">

We see a bunch of 200 response codes, which helps. Let's double click on the POST request to see what we learn.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/cspfindcsp.png" width="700">

In the bottom right corner, we see a response header called "Content-Security-Policy". That's what we need! Examining the contents tells us the full CSP:

<code>script-src 'self' https://pastebin.com  example.com code.jquery.com https://ssl.google-analytics.com ;</code>

This tells us we can load Javascript from the following locations:
<ul>
  <li>Scripts from within DVWA (<a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2005:%20File%20Upload.md" target="_blank">Challenge 5</a>, anyone?);</li>
  <li>Anything hosted on the domain pastebin.com;</li>
  <li>Anything hosted on the domain example.com;</li>
  <li>Anything hosted on the domain code.jquery.com; or</li>
  <li>https://ssl.google-analytics.com/.</li>
</ul>

We could pretty easily upload a Javascript file to DVWA, but we've already done that. Let's try something different. Of the other options, Pastebin allows us to upload any files we want, painlessly and anonymously. Sounds good to me!

<h3><b>Bypassing the CSP</b></h3>

Let's upload some example code to Pastebin.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csppastebinupload.png" width="700">

By way of explanation, I uploaded the code snippet <code>console.log("CSP Bypass");</code> to Pastebin. This snippet will make the string "CSP Bypass" visible in our browser's console log, if our attack works. I told Pastebin that I:
<ol type="1">
  <li>Uploaded a Javascript file ("Syntax Highlighting");</li>
  <li>Made the upload an "unlisted" post (only people who know the direct link of the upload will know it exists; done via "Post Exposure"); and</li>
  <li>Told Pastebin to delete the upload after an hour ("Paste Expiration").</li>
</ol>

After clicking the "Create New Paste" button, you should get a success screen similar to this:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csppastebinuploadsuccess.png" width="700">

Click the "Raw" button above the top right corner of the "RAW Paste Data" field. 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csppastebinrawdata.png" width="500">

This removes all the Pastebin-added stuff and give us only what we uploaded. That's what we can use to check if our exploit works!

Let's navigate back to the challenge and open up the developer console again. Enter our raw Pastebin link into the form field. Click the "Include" button and check the console log.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/cspcomplete.png" width="700">

We did it! Our message was successfully logged to the console, and we can verify we see it. Challenge complete!
