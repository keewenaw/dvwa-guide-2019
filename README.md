# dvwa-guide-2019
Solutions and various notes for the Damn Vulnerable Web App (DVWA) pentesting tool, intended to be accurate as of 04/30/2019.

<b><i>Please note the following restrictions and caveats. Continuing to read or interact with this repo in any manner signifies consent to these terms.</i></b>
<ol type="1">
<li>I will always assume you have the following already set up:</li>
  <ol type="a">
  <li>A fully-updated version of Kali, with Firefox, Burp Suite, and whatever other tools you find interesting configured appropriately. I used Kali 4.19.0 x64 in a VM.</li>
    <ol type="i">
    <li>Kali download: https://www.kali.org/downloads/</li>
    </ol>
  <li>A fully-updated web server with XAMPP installed and configured. I used Ubuntu 18.04 LTS with GNOME, also in a VM.</li>
    <ol type="i">
    <li>Ubuntu download: https://www.ubuntu.com/download</li>
    <li>XAMPP download: https://www.apachefriends.org/download.html</li>
    </ol>
  <li>Connectivity between (a) and (b). That is, you should be able to visit the IP of your web server in your Kali browser and see and interact with the XAMPP dashboard without issue. I also made a quick addition to my /etc/hosts in Kali, associating the IP of my web server to the hostname "dvwa". That's up to you though.</li>
  <li>DVWA installed and configured correctly on your web server. I put mine in /dvwa/, but I believe the default folder is named something different.</li>
    <ol type="i">
      <li>DVWA download: http://www.dvwa.co.uk/</li>
    </ol>
  </ol>
  I will not be writing a guide on how to do any of these. With all due respect, if you can't get this far, pentesting probably isn't for you just yet. Don't feel bad though! I'm an IT security professional and still regularly run into issues, or have to re-look up how to do the simplest things. It's just the way the world works for people like us. I'll be happy to provide hints if you need them, in private. Otherwise, I'd recommend coming back to this when you're comfortable doing all of the above yourself.

<li>This is not comprehensive by any means. I'm mostly just uploading my notes as I get to each module, and I have no real intention to complete all aspects of the DVWA. It's all for fun, as things like this should be.<li>

<li>Don't use my text or my particular solutions in any other forms of media, or attempt to pass off anything I write here as your own. I understand that solutions are often done in a similar manner as what I put here, so I understand the commands and tools you use may be the same as mine. That's fine. Just don't copy my other notes or stuff that is clearly mine.</li> 

<li>These notes should be for personal use only. Commercial or governmental use is expressly prohibited without my written consent.</li>

<li>Don't use any of this for illegal purposes. I understand that not all IT security people feel the need to stay white-hat. However, I myself am purely white-hat, and I expect you to be the same. Don't use my notes - or the skills you learn by using my solutions - for anything that could even be concievably mistaken as illegal activity. If you wouldn't do it in front of an FBI agent or your mom, don't do it at all. I will go out of my way to help law enforcement if they reach out to me about something I post here.</li>
</ol>

Any questions? Want me to help with anything? Let me know. And have fun!

-Mark

<i>Terms and conditions: This is provided for free and can be used in any manner you see fit, ONLY IF you clearly and prominently credit me as the original author (either by my Github handle @mrudy or my Twitter handle @keewenaw). If you use this, you take all responsibility for anything you may do with it or anything it does to your systems. Doing anything with this repo or code signifies acceptance of these conditions.</i>
