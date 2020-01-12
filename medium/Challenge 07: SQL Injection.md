<b>Challenge URL:</b> /dvwa/vulnerabilities/sqli/
<br>
<b>Objective:</b> There are 5 users in the database, with IDs from 1 to 5. Steal their passwords via SQLi.
<br>
<b>Tools needed:</b> john
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2007:%20SQL%20Injection.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqlisourceserver.png" width="500">

We see the addition of the PHP function <code><a href="https://www.php.net/manual/en/mysqli.real-escape-string.php" target="_blank">mysqli_real_escape_string()</a></code>. Essentially, this method cleans ("sanitizes") the input, removing any characters an attacker traditionally uses to trigger a SQL injection (SQLi) attack. From <a href="https://www.w3schools.com/php/func_mysqli_real_escape_string.asp" target="_blank">this link</a>, we know the characters the function removes include:

<blockquote>NUL (ASCII 0), \n, \r, \, ', ", Control-Z</blockquote>

Now let's examine the client-side source code to see how things get passed to the function:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqlisourceclient.png" width="700">

We see that the variable <code>id</code> gets set to whatever we select in the dropdown, then gets sent to the server in a POST request.

See what the weakness is yet? Take a few seconds to review the source code and come up with a potential avenue for exploitation. Then let's move on to the next section.

<h3><b>Crafting a New Exploit</b></h3>

So what did you come up with? The key is in the actual query in the server-side source code. Look at how the <code>$id</code> parameter gets inserted into the query - there are no single quotes! If we remember the easy mode challenge, our final exploit looked like this:

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select user, password from dvwa.users #</code>

The only character that's in our exploit that will get removed by <code>mysqli_real_escape_string()</code> is the single-quote character <code>'</code>. But we only inserted the single quote to break out of our last server-side query, which had quotes already built into it. But this challenge removed them, meaning we don't need quotes at all! If we remove them, we don't need to worry about any processing by <code>mysqli_real_escape_string()</code>. So let's modify our exploit as follows:

<code>% or 0=0 union select user, password from dvwa.users #</code>

Okay, we have a starting point for a new exploit. So how do we pass it to the <code>$id</code> parameter, especially when the developer added the dropdown menu to protect the value? Personally, for modifying in-transit HTTP requests, I love Burp Suite.

Let's boot Burp up and turn on interception ("Proxy" tab > "Intercept" tab > "Intercept is on" button is enabled). Then let's trigger the code by selecting something from the challenge dropdown at random, then clicking the "Submit" button. When we see the request in the "Intercept" tab, click "Action" > "Send to Repeater" to move it to the "Repeater" tab for modification. (You can turn off interception now as well.) When viewing the request in the repeater module, we see the <code>id</code> parameter on the bottom. Let's replace the value with our exploit and click "Go".

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqliburprepeaterfail.png" width="500">

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqliburprepeaterfailreply.png" width="500">

Hm, we got an error. The error seems to start at the <code>%</code> character in our exploit. What happens if we replace it in our query with a valid <b>id</b>, something from the dropdown, like so?

<code>1 or 0=0 union select user, password from dvwa.users #</code>

Let's try that:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqlirepeaterfixed.png" width="500">

It works! Let's examine the raw response and extract the hashes:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqlirepeatersuccess.png" width="500">

Our hash pairs are:
<ul>
  <li>admin / 5f4dcc3b5aa765d61d8327deb882cf99</li>
  <li>gordonb / e99a18c428cb38d5f260853678922e03</li>
  <li>1337 / 8d3533d75ae2c3966d7e0d4fcc69216b</li>
  <li>pablo / 0d107d09f5bbe40cade3de5c71e9e9b7</li>
  <li>smithy / 5f4dcc3b5aa765d61d8327deb882cf99</li>
</ul>

We can crack them with <code>john</code>, like before, to get the passwords:

<code>john --wordlist=rockyou.txt --format=Raw-MD5 hashes.txt</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/sqlihashescracked.png" width="500">

The cracked passwords match what we found before. Challenge complete!
