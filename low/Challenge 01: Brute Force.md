<b>Challenge URL:</b> /dvwa/vulnerabilities/brute/
<br>
<b>Objective:</b> Brute force the administrator's password.
<br>
<b>Bonus objective:</b> Find the other four users' usernames and brute force each password.
<br>
<b>Tools needed:</b> Hydra, a remote shell from Challenge 4 (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>The Administrator's Account</b></h3>

Okay, let's navigate to the challenge. We are presented with a classic username/password web form. 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/bruteprompt.png" width="500">

We're obviously assuming that we don't know the administrator's password, though I know we do. That's what we used to log into DVWA, after all. However, for convenience, we won't try and brute force the administrator's username, which is "admin". I tried it for fun, but it took way too much time and I feel that's not what the spirit of the challenge is. So our objective is to find the password for "admin".

I don't know about you, Dear Reader, but I'm not manually brute forcing anything! So let's take a quick step back and examine what tools Kali has to automate brute forcing. Under the Kali menu "Applications" > "05 - Password Attacks", we see plenty - John, Medusa, Ophcrack, pyrit. I personally prefer the command-line cracker Hydra, so let's try that. 

But what arguments do we need to pass to Hydra? I'm guessing we'll need some, right? Let's try running the command <code>hydra</code> in the terminal and find out!

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutehydraparams.png" width="500">

All right! I'll save us all some time and say which ones we need to know about:
<ul>
  <li><code>-l [username]</code>: The username we want to attack. In our case, we don't need to pass in a file. We can just specify "admin" as a string.</li>
  <li><code>-P [wordlist_file]</code>: The password we want to try. We don't know what this is yet, so we'll pull in a wordlist. More on that below.</li>
  <li><code>-s [port]</code>: The port our service is on. This should be port 80, as we didn't slap an SSL certificate on our web server. Yours may vary depending on how you set up your web server.</li>
  <li><code>[service]</code>: The kind of service (ie: what kind of HTTP request) this form uses. More below.</li>
  <li><code>[host]</code>: Our target web server. Mine is "dvwa".</li>
  <li><code>[target_URI]</code>: The relative path to the vulnerable form, with some additional arguments formatted in a specific way. More below.</li>
</ul>

Let's define the missing parts:
<ul>
  <li><code>-P [wordlist_file]</code>: In Kali, wordlists are stored at <b>/usr/share/wordlists/</b>. I think "rockyou" is a phenomenal list, so let's extract the TXT file in "rockyou.tar.gz" to our working directory.</li>
  <li><code>[service]</code>: We can find the service in multiple ways:
    <ul>
      <li>Examining the client-side source code in our browser (Right-click > "Inspect Element");
        <br><br>
        <img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutegetclient.png" width="600">
      </li>
      <li>Clicking the "View Source" button on the bottom right of the page, which pulls the server's PHP file; or
        <br><br>
        <img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutesource.png" width="500">
      </li>
      <li>Analyzing the traffic in Burp Suite. It's good practice if you don't know Burp well, but it's overkill for our purposes.</li>
    </ul>
    <br>
    We can see multiple instances of the word "GET", which makes it clear that <code>[service]</code> should be <b>http-get-form</b>!
    <br><br>
  </li>
  <li><code>[target_URI]</code>: Formatting this is tricky, so I'll tell you that this part will require three parts, separated by "<b>:</b>":
    <ol type="1">
      <li><b>The full target URL</b>. We know the exact URL of the form already: <b>/dvwa/vulnerabilities/brute/index.php</b>.</li>
      <li><b>Any parameters</b>. If we view the client-side source code above, we can see two parameters listed: <b>username</b> and <b>password</b>. We also see an action, <b>Login</b>, which we have to pass in so the form knows what to do with our data.</li> 
      <li><b>Failed login message</b>. We need to pass in a way for Hydra to know if a password isn't valid. We can replicate this by giving false credentials to the form, thereby telling us that the failed login message is "<b>Username and/or password incorrect.</b>".</li>
    </ol>
</ul>

Combining all that together, we get our final command.

<code>hydra -l admin -P rockyou.txt -s 80 dvwa http-get-form "/dvwa/vulnerabilities/brute/index.php:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect.</code>

If it works, our successful username/password pair will be highlighted in green. Let's run it and see what happens! 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutepwno.png" width="500">

Wait, why did we get 16 valid pairs?! Take a second and see if you can find out why.

...

All set?

Here's what happened: When we ran our command, we didn't actually give a way for Hydra to authenticate to our target page. Hence, without us knowing, Hydra actually ran the attack unauthenticated, which means the attack ran againt <b>/dvwa/login.php</b>. Since the failed-login error message for that page isn't the same as our target, Hydra can't really tell what a valid credential set is. So it just throws out the first 16 passwords in rockyou.txt at random for each of Hydra's 16 process threads (see the Hydra parameter screenshot above for why it's 16). 

So is there a way we can give or trick Hydra into using a valid session, so that it can "see" the actual target? 

We know cookies are often used in web authentication, so let's start by examining the cookies our browser has. You can do this by opening the Firefox developer web console (Control+Shift+K) and entering the string <code>document.cookie</code>. We see two fields, <b>security</b> and <b>PHPSESSID</b>. <b>security</b> is our current security setting, "low". <b>PHPSESSID</b> is our session ID, which is actually what we need! 

Let's re-run our command, passing our cookie in the way Hydra expects:

<code>hydra -l admin -P rockyou.txt -s 80 dvwa http-get-form "/dvwa/vulnerabilities/brute/index.php:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect.:H=Cookie: security=low; PHPSESSID=[your_value_here]"</code>

Behold! 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutepwyes.png" width="500">

Let's test our cracked password in the form:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/brutesuccess.png" width="500">

We got the success message. Nice! But we're not quite done yet ...

<h3><b>The Other Accounts</b></h3>

So let's examine that weird white box (or actual image if your setup was <s>better</s>different than mine) below the success message. If we inspect that element as before, we see an interesting line: <code>&#60;img src="./hackable/users/admin.jpg"&#62;</code>. We can logically assume if we try to visit that <b>/users/</b> directory, we may find the other usernames in the format <b>[username].jpg</b>. So after some playing around with the URL in Firefox, we find the directory.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/bruteothers.png" width="500">

Other possible attack vectors include uploading a reverse shell in <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2004:%20File%20Inclusion.md" target="_blank">Challenge 4</a>, SQL injection, or passing in a wordlist of all possible usernames and brute forcing that at the same time as the passwords.

Regardless of how you found them, we find out that the other four usernames are:
<ul>
  <li>"smithy"</li>
  <li>"gordonb"</li>
  <li>"pablo"</li>
  <li>"1337"</li>
</ul>

With this, we can easily modify our earlier Hydra command. We only need to make two changes. We capitalize the <code>-l</code> switch to <code>-L</code> to indicate we're now passing in a file. We then actually pass in a text file with all the usernames instead of the string "admin" (I called mine "users.txt").

<code>hydra -L users.txt -P rockyou.txt -s 80 dvwa http-get-form "/dvwa/vulnerabilities/brute/index.php:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect.:H=Cookie: security=low; PHPSESSID=[your_value_here]"</code>

Let's see the result.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/bruteotherpwyes.png" width="500">

We can test each as before, and they all give us a similar success message. 

Challenge complete. Great work!
