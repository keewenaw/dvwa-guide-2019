<b>Challenge URL:</b> /dvwa/vulnerabilities/upload/
<br>
<b>Objective:</b> Execute any PHP function of your choosing on the target system (such as phpinfo()	or system()) thanks to this file upload vulnerability.
<br>
<b>Tools needed:</b> A reverse shell, netcat
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Note: This challenge is extremely similar to <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2004:%20File%20Inclusion.md" target="_blank">Challenge 4</a>. We'll be using the PHP reverse shell and netcat listener from before, so make sure those are good to go before proceeding.</i>

Right off the bat, DVWA presents a simple file upload form.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/uploadform.png" width="500">

Let's not waste any time, and immediately try to upload Challenge 4's reverse shell, <b>php-reverse-shell.php</b>.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/uploaduploadshell.png" width="500">

It can't be this easy, can it? The form tells you exactly where the newly-uploaded shell is! Let's try navigating to it. 

<b>http&#58;//dvwa/dvwa/hackable/uploads/php-reverse-shell.php</b>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/uploadsuccess.png" width="500">

Looks like the shell worked first try. I tested with the <code>whoami</code> command we learned in <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2002:%20Command%20Injection.md" target="_blank">Challenge 2</a>, and we got the same output as before. I guess it really was that easy!

Challenge complete! Feeling like a hacker yet?
