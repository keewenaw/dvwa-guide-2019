<b>Challenge URL:</b> /dvwa/vulnerabilities/exec/
<br>
<b>Objective:</b> Remotely, find out the user of the web service on the OS, as well as the machine's hostname via RCE.
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge 02: Command Injection.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/execsource.png" width="500">

We see that the developer added basic filtering on input. Specifically, they find the character strings <code>&&</code> and <code>;</code> in the user input and replace them with <code>null</code> characters. A good attempt, since it removes some of the most common methods of chaining commands. However, from our previous analysis, we know there are many more ways to chain commands, one of which uses the character <code>&</code>. So the filter shouldn't prevent us chaining our commands via <code>&</code> and we can still extract the information and beat the challenge.

<h3><b>Crafting a New Exploit</b></h3>

If you recall, our final exploit for the easy challenge looked like this:

<code>127.0.0.1; echo "\nUser: $(whoami)"; echo "Hostname: $(hostname)"</code>

So with our new filters in place, we can adjust our exploit to something like the below:

<code>127.0.0.1 & echo "\nUser: $(whoami)" & echo "Hostname: $(hostname)"</code>

If our intuition is correct, we should be able to run the above and still get our required information. Let's try now:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/execsuccess.png" width="500">

Perfect! The information isn't in the best format, but we got it and it matches our previous result.

Challenge complete.
