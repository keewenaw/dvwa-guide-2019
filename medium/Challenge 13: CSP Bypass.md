<b>Challenge URL:</b> /dvwa/vulnerabilities/csp/
<br>
<b>Objective:</b> Bypass Content Security Policy (CSP) and execute JavaScript in the page.
<br>
<b>Tools needed:</b> Burp Suite (optional)
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="hhttps://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2013:%20CSP%20Bypass.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

If we get the headers as before, we note that the CSP has changed as follows:

<code>script-src 'self' 'unsafe-inline' 'nonce-TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA='</code>

<h3><b>Crafting a New Exploit</b></h3>

What is this "nonce" thing? If we look at some <a href="https://www.troyhunt.com/locking-down-your-website-scripts-with-csp-hashes-nonces-and-report-uri/" target="_blank">online references</a>, we see that this nonce is used to whitelist a script. Ie, if the script we want to run specifies the nonce value as "TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=", the code will consider the script "whitelisted" and will execute it. 

One more thing to check is if the nonce value changes. Without viewing the source code, we can make an educated guess on if it does via two methods. We can first submit multiple requests to check if the nonce value differs. Second, if we know the format of the nonce, we can try reverse engineering it. The <code>=</code> character often signifies a Base64-encoded string, so let's test our hypothesis by putting the value in an <a href="https://www.base64decode.org/" target="_blank">online Base64 decoder</a>. There, we find our nonce of "TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA=" converts to the string "Never going to give you up". Doesn't look like there's any specific field or input that would change here, so if we're lucky, we can insert this nonce into our script header, like so:

<code>&#60;script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA="&#62;console.log("CSP Bypass");&#60;/script&#62;</code>

If we try that payload, we see the expected message in our console!

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/cspsuccess.png" width="500">

We can of course replace the payload with an alert bubble, malicious script, or anything else. Challenge complete!
