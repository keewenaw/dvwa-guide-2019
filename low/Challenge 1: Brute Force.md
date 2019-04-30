<b>Challenge URL:</b> /dvwa/vulnerabilities/brute/
<br>
<b>Objective:</b> Brute force the administrator's password.
<br>
<b>Bonus objective:</b> Find the other four users' usernames and brute force each password.
<br>
<b>Tools needed:</b> Hydra, a remote shell from Challenge 5 (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudy/dvwa-guide-2019/blob/master/low/README.md">Readme</a>?</i>

<h2><b>The Guide<b></h2>

<h4><b>The Administrator's Account</b></h4>

Okay, let's navigate to the challenge. We are presented with a classic username/password web form. We're obviously assuming that we don't know the administrator's password, though we do. That's what we used to log into DVWA, after all. However, for convenience, we won't try and brute force the administrator's username, "admin". I tried it for fun, but it took way too much time and I feel that's not what the spirit of the challenge is. So our objective is to find the password for "admin".

I don't know about you, Dear Reader, but I'm not manually brute forcing anything! So let's take a quick step back and examine what tools Kali has to automate brute forcing. John, Medusa, Ophcrack, Hydra, pyrit, and others. My personal preference is with Hydra, so let's try that. 
