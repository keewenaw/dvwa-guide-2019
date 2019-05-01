<b>Challenge URL:</b> /dvwa/vulnerabilities/upload/
<br>
<b>Objective:</b> Execute any PHP function of your choosing on the target system (such as phpinfo()	or system()) thanks to this file upload vulnerability.
<br>
<b>Tools needed:</b> A reverse shell, a temporary web server on Kali, netcat, Burp Suite
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Note: This challenge is extremely similar to <a href="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/Challenge%204:%20File%20Inclusion.md" target="_blank">Challenge 4</a>. We'll be using the temporary web server, netcat listener, and PHP reverse shell from before, so make sure those are good to go before proceeding.</i>

<h3><b>Testing the Form</b></h3>

Right off the bat, DVWA presents a simple file upload form.

<img src="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/screenshots/uploadform.png" width="500">

Let's not waste any time, and immediately try to upload Challenge 4's reverse shell.

<img src="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/screenshots/uploaduploadshell.png" width="500">

It can't be this easy, can it? Remember, we already know where the <b>/hackable/</b> directory is in relation to the challenges. Let's try navigating to the newly-uploaded shell. 

<b>http&#58;//dvwa/dvwa/hackable/uploads/php-reverse-shell.php</b>

<img src="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/screenshots/uploadsuccess.png" width="500">

I tested with the <b>whoami</b> command we learned in <a href="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/Challenge%202:%20Command%20Injection.md" target="_blank">Challenge 2</a>, and it works.

I guess it really was that easy!

Challenge complete. Feeling like a hacker yet?
