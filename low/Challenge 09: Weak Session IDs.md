<b>Challenge URL:</b> /dvwa/vulnerabilities/weak_id/
<br>
<b>Objective:</b> Work out how the ID is generated and then infer the IDs of other system users.
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

Here's what we start with:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/weakform.png" width="500">

Right away, the text tells us to look at our cookies, specifically one called "dvwaSession". And when I think cookies, I think the Firefox developer console. Let's pull that up via the keyboard with <code>Control+Shift+K</code>. Let's click the "Generate" button and enter the text <code>document.cookie</code> in the console. Here's what I see:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/weakinitial.png" width="700">

<i>Note that I actually input <code>document.cookie.split(";")[0]</code> to hide my session ID. Just a privacy thing for me. You don't have to do that, and it impacts nothing besides what you see in my screenshots.</i>

Oh no, <b>dvwaSession</b> is set to "1". I think you know exactly how this is going to go!

All the same, let's alternate between clicking the "Generate" button and viewing our cookie a few times:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/weakmultiple.png" width="500">

<b>dvwaSession</b> becomes "2", then "3", then "4", <i>ad infinitum</i>.

So there's our answer. <b>dvwaSession</b> gets initialized as "1", then gets incremented by 1 each time you click "Generate". If there were other users, you'd keep following the above process, either manually or via a script of some sort. If you found a gap, something like <b>dvwaSession</b> going from "4" to "6", you could deduce another user has <b>dvwaSession=5</b> and plan further attacks from there (maybe use a cross-site scripting vulnerability to steal their cookie?).

It's nice to have a quick and easy one for once, isn't it? Challenge complete.
