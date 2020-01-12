<b>Challenge URL:</b> /dvwa/vulnerabilities/javascript/
<br>
<b>Objective:</b>  Simply submit the phrase "success" to win the level. 
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2014:%20Javascript.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/jssource.png" width="500">

We see that the Javascript code exists in the file <b>vulnerabilities/javascript/source/medium.js</b>, so let's check that source out too. We find the following code:

1 <code>function do_something(e){</code><br>
2 <code>  for(var t="",n=e.length-1;n>=0;n--)</code><br>
3 <code>    t+=e[n];</code><br>
4 <code>  return t</code><br>
5 <code>}</code><br>
6 <code>setTimeout(function(){</code><br>
7 <code>  do_elsesomething("XX")</code><br>
8 <code>},300);</code><br>
9 <code>function do_elsesomething(e){</code><br>
10 <code> document.getElementById("token").value=do_something(e+document.getElementById("phrase").value+"XX")</code><br>
11 <code>}</code><br>

Let's break down the code line-by-line:
<ol type="1">
  <li>This defines the function <code>do_something()</code>, which has one argument <code>e</code>.</li>
  <li>This iterates through <code>e</code> character-by-character in reverse order.</li>
  <li>This appends the current character to a new temporary string.</li>
  <li>This line returns the temporary string, which is <code>e</code> reversed.</li>
  <li>This closes out the <code>do_something()</code> function definition.</li>
  <li>This sets a timeout, which will execute the defined <code>function()</code> after a specified wait.</li>
  <li>This <code>function()</code> code calls the other function <code>do_elsesomething()</code>, defined below, with the argument "XX".</li>
  <li>This sets the timeout for <code>function()</code> to 300 milliseconds.</li>
  <li>This defines the function <code>do_elsesomething()</code>, which has one argument <code>e</code>. </li>
  <li>This sets the "token" value to the value returned by <code>do_something()</code> with an argument of <code>the input argument, concatenated with the value input into the challenge's form, concatenated with "XX"</code>.</li>
  <li>This closes out the <code>do_elsesomething()</code> function definition.</li>
</ol>

<h3><b>Crafting a New Exploit</b></h3>

All told, the above code sets the value of "token" to "XX" concatenated with the input form value, reversed, concatenated with "XX". So in order to win the challenge, we need to set "token" to "XXsseccusXX"!

Let's try that out. In the Firefox developer console, input the following command:

<code>document.getElementById("token").value="XXsseccusXX"</code>

Then input "success" into the challenge's form field, then click the "Submit" button:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/jssuccess.png" width="500">

We got the "Well done!" message yet again. Challenge complete!
