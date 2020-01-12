<b>Challenge URL:</b> /dvwa/vulnerabilities/javascript/
<br>
<b>Objective:</b> Simply submit the phrase "success" to win the level. 
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Examining the Form</b></h3>

We're presented with a simple form, pre-filled with the string "ChangeMe". 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/jsform.png" width="500">

Well, why don't we do the obvious and try submitting "success"?

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/jsformtestfail.png" width="500">

Hm, no luck. We do get a useful error that states "Invalid token". So that means the form must be defining and setting a token in some manner. Let's examine the source code and see what we can find out. Inspect the form by right-clicking it and selecting "Inspect Element". 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/jssource.png" width="500">

Okay, we can notice two things:
<ol type="1">
  <li>The form does indeed have a hidden "token" field.</li>
  <li>Javascript code exists to set "token".</li>
</ol>

<h3><b>Reverse Engineering the Javascript</b></h3>

If you're anything like me, you saw the Javascript and your eyes started to glaze over. Bit manipulation, obfuscated variable and function names ... it could take hours to reverse engineer. 

Hold up though, back the fun bus up a sec. If we examine the code more closely, we see two things that make our job much easier:

<ol type="1">
  <li>A commented out line: <code>MD5 code from here https://github.com/blueimp/JavaScript-MD5</code></li>
  <ul>
    <li>This tells us most of the worst code is simply an implementing of the MD5 hashing algorithm, and can safely be "ignored" for now.</li>
  </ul>
  <li>Most of the code we care about exists in two comparatively-easy functions, <code>rot13()</code> and <code>generate_token()</code>.</li>
</ol>

Let's pull them apart.

<h4><b><code>rot13()</code></b></h4>

Here's the code, slightly cleaned:

1 <code>function rot13(inp) {</code><br>
2 		<code>return inp.replace(/[a-zA-Z]/g,function(c){</code><br>
3       <code>return String.fromCharCode(</code><br>
4         <code>(c<="Z"?90:122)>=(c=c.charCodeAt(0)+13)?c:c-26</code><br>
5       <code>)</code><br>
6     <code>}</code><br>
7   <code>);</code><br>
8 <code>}</code><br>

Let's discuss the functionality line-by-line.

<ol type="1">
  <li>We're defining a function called <code>rot13()</code> with one argument, <code>inp</code>. We can assume <code>inp</code> is our plaintext (ie, what we input in the form), which will be encrypted by the <a href="https://en.wikipedia.org/wiki/ROT13" target="_blank">ROT13 cipher</a>.</li>
  <li>We're replacing any alphabetic character (lowercase or uppercase) in <code>inp</code> with whatever <code>function()</code> returns.</li>
  <li>This is where <code>fromCharCode()</code> returns, with what it returns being defined in the next line.</li>
  <li>This is just an implemention of the ROT13 cipher, which "rotates" a single alphabetic character 13 spaces according to the cipher specifications.</li>
  <li>We're closing out the <code>fromCharCode()</code> function definition and returning a single-character output as a string.</li>
  <li>We're closing out the <code>function()</code> function definition, which gives us the replacement single-character string as encrypted by the ROT13 implementation.</li>
  <li>We're closing out the <code>replace()</code> function definition, so we can iterate through all characters in <code>inp</code>.</li>
  <li>We're completing the <code>rot13()</code> function definition, so we can return the full ROT13-encrypted result of <code>inp</code> to the calling code.</li>
</ol>

In short, this function takes a string as input and returns the ROT13-encrypted version of it.

<h4><b><code>generate_token()</code></b></h4>

Here's the code:

1 <code>function generate_token() {</code><br>
2		<code>var phrase = document.getElementById("phrase").value;</code><br>
3		<code>document.getElementById("token").value = md5(rot13(phrase));</code><br>
4	<code>}</code><br>

Let's discuss the functionality line-by-line.

<ol type="1">
  <li>We're defining a function <code>generate_token()</code> that accepts no arguments.</li>
  <li>We're defining a variable <code>phrase</code> and setting it to whatever text we had input in the form.</li>
  <li>We're setting the value of "token" to our input, once the input has first been encrypted with the previously-defined <code>rot13()</code> cipher, then has been hashed with the previously-defined <code>md5()</code> hash function.</li>
  <li>We're completing the <code>generate_token()</code> function definition.</li>
</ol>

In short, this function takes our input, modifies it, and sets "token" to the modified value.

<h3><b>Breaking the Form</b></h3>

So if we've analyzed the code correctly, all we need to do is encrypt the string "success" with the ROT13 cipher. Then we hash the result with the MD5 hashing algorithm. Finally, we set the value of "token" to our hash.

Let's do that step-by-step:
<ol type="1">
  <li><a href="https://www.ROT13.com/" target="_blank">rot13</a>("success") = "fhpprff"</li>
  <li><a href="https://www.MD5hashgenerator.com/" target="_blank">md5</a>("fhpprff") = "38581812b435834ebf84ebcc2c6424d6"</li>
</ol>

We can then go back to our form and open up the Firefox developer console by pressing the <code>Control+Shift+K</code> keys simultaneously. Input the following line in the console:

<code>document.getElementById("token").value = "38581812b435834ebf84ebcc2c6424d6"</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/jssettoken.png" width="500">

Then submit the form with the word "success":

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/jssuccess.png" width="500">

We got the success message! 

Challenge complete, and DVWA's "low" mode is completely broken!
