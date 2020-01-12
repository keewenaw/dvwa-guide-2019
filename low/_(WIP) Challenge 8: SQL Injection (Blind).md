<b>Challenge URL:</b> /dvwa/vulnerabilities/sqli_blind/
<br>
<b>Objective:</b> Find the version of the SQL database software through a blind SQL attack.
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

THIS IS A WORK IN PROGRESS, TO BE COMPLETED LATER.

<h3><b>Examining the Form</b></h3>

Here's our form:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlibform.png" width="500">

We know from the related <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2007:%20SQL%20Injection.md" target="_blank">Challenge 7</a> that there are five user IDs: 1, 2, 3, 4, and 5. So let's test the form by inputting "1":

<img src="https://github.com/keewenaw/dvwa-guide-2019/blob/master/low/screenshots/sqlibtestt.png" width="500">

Now just for fun, let's try a user ID that we know doesn't exist, like "6".

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlibtestf2.png" width="500">

So we get a message of "User ID exists in the database." for valid, "true" entries, and "User ID is MISSING from the database." for invalid, "false" entries. 

<h3><b>Discovering the SQL Injection</b></h3>

In our last section, we discovered that the returned message differs based on whether the input was "valid" or not. That's incredibly important information. You see, the key issue with blind SQL injection (SQLi) attacks is that we can't directly view the result of the query; it usually gets filtered or processed before something is returned to the end user. So by having clearly defined "true" and "false" messages, the server will still leak information. As long as we can craft a SQLi exploit that can return a a true/false answer, we can leverage that to pull arbitrary information. It'll take longer than your basic SQLi, but is still effective. So let's start building a query that gives is what we want.

For basic SQLi attacks, we could have a query like:

<code>1' or (select version()) #</code>

But since we can only get a "true"/"false", we can approach this in two ways:

<ol type="1">
  <li>Iterate through every version number possible and do a string comparison. For example, we could do something like <code>test 5.0.0.0, 5.0.0.1, ... , 5.1.0.0, ... until we get a match</code>. In my mind, this isn't feasible since we don't know the exact format of the version number. What if it's X.X.X.X, or XX.XX.XX.XX, or XX.XXXXXX? Since we don't really have a way of knowing when doing a blind test, this option doesn't seem effective.</li>
  <li>Iterate through each character in the version number. We could try something like <code>grab the first character of the version number, then iterate through all alphanumeric ASCII characters until we find a match. We then move to the second character of the version number, then third, and so on until we get a blank space (implying the end of the version string)</code>. Since this is format-agnostic, this seems like the best place to start.</li>
</ol>

Further, since we don't know the version length, plus since we do know the range of alphanumeric ASCII characters is somewhat large, this sounds like a prime opportunity to build a script to automate this process!

THIS IS A WORK IN PROGRESS, TO BE COMPLETED LATER.
