<b>Challenge URL:</b> /dvwa/vulnerabilities/weak_id/
<br>
<b>Objective:</b> Work out how the ID is generated and then infer the IDs of other system users. 
<br>
<b>Tools needed:</b> None
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/README.md" target="_blank">README</a>?</i>

<h2><b>The Guide</b></h2>

<i>Since this challenge is very similar to the easy mode challenge, I'd highly recommend re-reading <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2009:%20Weak%20Session%20IDs.md" target="_blank">our notes from before</a>. We'll be using a lot of the analysis and code from before.</i>

<h3><b>What's Changed</b></h3>

So let's click the "generate" button a few times, then analyse <b>dvwaSession</b>:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/weakcookie.png" width="500">

Even without viewing the source, this number format will look familiar to any experienced *nix user - it's Unix time! It doesn't appear to have any further parameters, so let's run our generated cookie values though an <a href="https://www.epochconverter.com/" target="_blank">epoch time converter</a> and view the result:

<table style="width:100%">
  <tr>
    <th><b>Cookie Value</b></th>
    <th><b>Human-Readable Time (GMT)</b></th>
  </tr>
  <tr>
    <th>1557161489</th>
    <th>2019-05-06 16:51:29</th>
  </tr>
  <tr>
    <th>1557161505</th>
    <th>2019-05-06 16:51:45</th>
  </tr>
    <th>1557161513</th>
    <th>2019-05-06 16:51:53</th>
  </tr>
  </tr>
    <th>1557161521</th>
    <th>2019-05-06 16:52:01</th>
  </tr>
</table>

That does indeed match up with the times I clicked the button! Upon reviewing the source code, our intuition is verified:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/medium/screenshots/weaksource.png" width="500">

In the real world, predicting other users' cookies becomes a lot more difficult in most circumstances. We'll need to know the exact time they logged on, in order to convert that time into Unix time and steal their session. Or we'd have to find a way to kick a user off their session, then allow them to log in at a time we already know.

Challenge complete.
