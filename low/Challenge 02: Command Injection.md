<b>Challenge URL:</b> /dvwa/vulnerabilities/exec/
<br>
<b>Objective:</b> Remotely, find out the user of the web service on the OS, as well as the machine's hostname via RCE.
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Probing the Code</b></h3>

When we start the challenge, we see what looks to be a ping tool:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/execintro.png" width="500">

Seems simple enough. Let's quickly test it with a valid IP.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/exectestgood.png" width="500">

And with an invalid input:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/exectestbad.png" width="500">

So it seems that the code takes our input, passes it as an argument to the command <code>ping</code>, and executes the string "<code>ping [input]</code>". If it works, we'll see the unformatted output of the command. If it doesn't, we'll see nothing. That's useful information, since we can easily tell if our future command injection is working sucessfully based on the output we see.

Now that we know how the code works, we need to figure out two things: which commands to execute, and how to chain them together in a way that all commands execute and display their output successfully.

<h3><b>Which Commands to Execute</b></h3>

We have two pieces of information to collect: the name of the current user of the web service, and the machine's hostname. We can Google this info rather easily, or test it on our Kali box. For the user, we can run the command <a href="https://en.wikipedia.org/wiki/Whoami" target="_blank"><code>whoami</code></a> without arguments. For the hostname, we use the appropriately-named <a href="https://ss64.com/nt/hostname.html" target="_blank"><code>hostname</code></a> without arguments. 

Even more conveniently, we also learn that the <code>whoami</code> and <code>hostname</code> commands are the exact same in both Windows and Linux! If that wasn't true and we were doing a black-box pentest, we would need to figure out what OS the web server uses. We could run a command unique to each OS and examine any output to see what fails. Good examples of commands to test with include <code>powershell</code> (works on Windows, not Linux) or <code>ifconfig</code> (works on Linux, not Windows). There are other ways to find the OS as well; play around and see what works for you.

<h3><b>How to Chain Commands</b></h3>

Again using our good friend Google, we learn we can chain commands in multiple ways. In Linux, we can use characters like <code>;</code> for standard chaining, <code>&#38;&#38;</code> if we want the second command to only run if the first worked, or <code>&</code> to run the first command in the background while the second runs. In Windows, we usually use <code>&#38;</code>. If this was a black-box test, we could iterate through all options to see what selection returns output and determine the OS that way. Or we could just use <code>&#38;</code> since it works on both OSes. We do run the risk of having command output returning in odd orders if we have a Linux OS, due to how threads terminate. Personally, as long as we get all required data, I'm okay with that. So let's start with <code>&#38;</code>.

<h3><b>The Attack</b></h3>

Let's take what we learned and putting it into our actual attack string. We know we need:

<ul>
  <li>A valid IP address to close out the <code>ping</code> command built into the code;</li>
  <li>The command <code>whoami</code> to find out the user;</li>
  <li>The command <code>hostname</code> to find out the hostname; and</li>
  <li>The chaining character <code>&#38;</code> to combine the above.</li>
</ul>

Putting that together, we get our attack string:

<code>127.0.0.1 &#38; whoami &#38; hostname</code>

Let's try it out.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/execattack.png" width="500">

Success! Line 1 shows the output of <code>whoami</code>, "daemon". Line 4 shows the output of <code>hostname</code>, which I had specified as "ubuntuwebtest". Our hostname also leaks some useful information, that we're running Ubuntu on our web server. You can test this result as you see fit, but I won't show that here. 

If we wanted cleaner output, we could change how we chain the commands:

<code>127.0.0.1; echo "\nUser: $(whoami)"; echo "Hostname: $(hostname)"</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/execattackclean.png" width="500">

If we want to be thorough, we could verify our answer on the test web server itself:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/execconfirm.png" width="500">

We did it! Challenge complete.
