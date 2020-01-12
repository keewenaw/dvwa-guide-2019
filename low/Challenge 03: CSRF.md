<b>Challenge URL:</b> /dvwa/vulnerabilities/csrf/
<br>
<b>Objective:</b> Make the current user change their own password, without them knowing about their actions, using a CSRF attack.
<br>
<b>Tools needed:</b> Burp Suite (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Testing the Form</b></h3>

We'll begin this challenge the standard way, by examining the form and testing its functionality.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csrfform.png" width="500">

Let's try changing our password to "newpw":

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csrftest.png" width="500">

Interesting, there's no real verification required. We don't need to know the old password or otherwise confirm that we actually wanted to change it, at least on the form. 

Now let's look at the URL, as shown in our browser:

<b>http&#58;//dvwa/dvwa/vulnerabilities/csrf/?password_new=newpw&password_conf=newpw&Change=Change#</b>

We don't see encryption or masking of the password in the URL. 

If you want to work with Burp, you can also examine the request headers, cookies, or other fun bits. Upon analysis, we don't see much that would hinder a password reset (besides the standard cookie <b>PHPSESSID</b>).

I wonder what we can do with this?

<h3><b>The Attack</b></h3>

The attack is actually trivial to implement. All we need to do is craft a new URL with whatever we want the password to be:

<b>http&#58;//dvwa/dvwa/vulnerabilities/csrf/?password_new=</b>[your_new_password]<b>&password_conf=</b>[your_new_password]<b>&Change=Change#</b>

If we can get the "admin" user to visit the malicious URL, the password will be changed to whatever we want. We don't need to actually type in anything to the vulnerable form.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csrfattack.png" width="500">

Note the URL in the screenshot, where I changed the password to "hackedpassword" successfully. We can verify by logging out ...

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csrflogout.png" width="500">

And logging back in with the hacked password ...

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/csrflogin.png" width="500">

It works! Challenge complete.

<h3><b>In the Real World ...</b></h3>

In the real world, when you're conducting a pentest, you'd use social engineering techniques to get a user to click the malicious link for you. Common techniques include spear-phishing emails, IMs, and texts. If the user is authenticated to the vulnerable application, the attack will succeed and the password will be reset without the user knowing. If you want to test this, you'll need to create a new user account that will send the malicious link to the "admin" user via email or a similar method. You'd then want the "admin" user to authenticate to the app and then click the link.

Social engineering attacks are somewhat luck-based, as it relies on the user:
<ol type="1">
  <li>Having an active session on the vulnerable app;</li>
  <li>Getting your message;</li>
  <li>Reading your message;</li>
  <li>Clicking the link; and</li>
  <li>(Ideally) Informing you the link didn't work in the way you pretended it would.</li>
</ol>

It's never guaranteed to work. That's why I'm not showing you how to do it here. If you want to try it anyway, consider it extra credit :)
