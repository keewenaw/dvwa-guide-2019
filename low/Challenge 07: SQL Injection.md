<b>Challenge URL:</b> /dvwa/vulnerabilities/sqli/
<br>
<b>Objective:</b> There are 5 users in the database, with IDs from 1 to 5. Steal their passwords via SQLi.
<br>
<b>Tools needed:</b> john
<br><br>
<i>Did you remember to read this section's <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/README.md">README</a>?</i>

<h2><b>The Guide</b></h2>

<h3><b>Examining the Form</b></h3>

Okay, we see a form that takes user input in the form of a "User ID". 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqliform.png" width="500">

The challenge told us there are five users with user IDs 1, 2, 3, 4, and 5. Let's test this form by entering one of these IDs.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqliformtestnormal.png" width="500">

Okay, the form takes our input and returns the user's ID, first name, and last name. What happens if we test for a SQL injection (AKA a "SQLi") by inputting the time-honored classic, a single quote character (<code>'</code>)?

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqliformtestsqli.png" width="700">

An error! You'll see something like this when user input isn't correctly sanitized before being used in a server-side SQL query. Not only that, we learn from the error that the web server is using MariaDB. That should be enough for us to get started.

<h3><b>Crafting a SQL Injection Exploit</b></h3>

Now that we know there's a potential SQLi, let's see what we can do with it. Reviewing the MariaDB documentation tells us that the special character <code>%</code> <a href="https://mariadb.com/kb/en/library/like/" target="_blank">functions as a wildcard</a>. Let's input that next and see what happens. 

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestwildcard.png" width="500">

Interesting, nothing happens. We'll need to find away to break out of the query the code wants us to make. Let's tweak our query a little bit:

<code>%&#39; or &#39;0&#39;=&#39;0</code>

The second part evaluates if zero is equal to zero. I really hope I don't have to explain why that's true! Since we combine our wildcard and this comparison with a logical OR, this query will always evaluate to "true", and we should get some form of output:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestor.png" width="500">

Note how our query replaces the ID field for all users, but it returns all users anyway. Honestly, we don't care much about the ID field. We know we can easily verify who has what ID by inputting user IDs the challenge gave us into the form field, like during our intial testing. More importantly, we just broke out of the intended query and now have an avenue to do whatever we want!

Let's start seeing where DVWA keeps the passwords. If we're lucky, DVWA will store them in the "password" column - seems logical, right? Let's build that query and try it out.

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select * from password #</code>

<i>Note the addition of the <code>#</code> character at the end of the statement. That character <a href="https://www.techonthenet.com/mariadb/comments.php" target="_blank">starts a comment</a>. It truncates the query and ensures we have full control over our future queries.</i>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestrandomcolumn.png" width="500">

No luck on that specific column, but it does confirm the database name of "dvwa", which helps.

Reviewing the MariaDB documentation tells us more useful info. It appears that we can pull all the column names from a MariaDB <a href="https://mariadb.com/kb/en/library/information-schema-columns-table/" target="_blank">built-in table</a> called "information_schema.COLUMNS". If we can do that, we may be able to pull the correct name of the column where the app stores passwords!

Let's redo our query to account for this new information:

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select COLUMN_NAME from information_schema.COLUMNS #</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestinfoschema.png" width="500">

Hm, an odd error. If you look at the docs again, it's basically telling us the amount of columns we're trying to pull don't equal the amount of columns we're displaying. Remember, in our previous testing, we're overwriting the "ID" output with our query. We still display "First name" and "Surname", so maybe we need to try pulling two columns? Let's try again:

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select TABLE_NAME, COLUMN_NAME from information_schema.COLUMNS #</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestinfoschemarev1.png" width="500">

Progress! We get output again, and by God we get all of the output. We definitely need to clean this up somehow. It's not as hard as you think. Based on the order of our <code>SELECT</code> clause above (<code>TABLE_NAME</code>, then <code>COLUMN_NAME</code>), we know that the info with a label of "First name" is our <code>TABLE_NAME</code> and "Surname" is our <code>COLUMN_NAME</code>. Let's filter on (what we logically assume is) our proper table name of "users".

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select TABLE_NAME, COLUMN_NAME from information_schema.COLUMNS where TABLE_NAME = &#39;users&#39; #</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestinfoschemarev2.png" width="500">

Partway down this output, we see what we want:

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestinfoschemarev2close.png" width="500">

We now know the usernames are stored in a column named "user", and passwords are stored in the column "password". Both exist in the table "users" in the "dvwa" database. Ha, turns out we weren't too far off in the beginning! Let's rebuild our query with this new info.

<code>%&#39; or &#39;0&#39;=&#39;0&#39; union select user, password from dvwa.users #</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlitestinfoschemarev3.png" width="500">

We've got all the hashes! We only need to break them and we're done.

<h3><b>Cracking the Hashes</b></h3>

For offline password cracking, I prefer the tool "John the Ripper" (AKA "john"). We can start using it by running <code>john</code> in a Kali terminal.

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlijohnparams.png" width="500">

The only useful parameters right now are: 
<ul>
  <li><code>--wordlist=[wordlist_file]</code>: We already had the "rockyou" wordlist from <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2001:%20Brute%20Force.md" target="_blank">Challenge 1</a>. We'll be using that again.</li>
  <li><code>--format=[hash_format]</code>: There's a useful site <a href="https://www.tunnelsup.com/hash-analyzer/" target="_blank">available here</a> that accepts a hash as input and outputs the algorithm believed to create it. john also has similar functionality, but I don't think it's reliable enough to use consistently. Plopping our hashes here tells us they're likely created with the MD5 algorithm. The john equivalent is "Raw-MD5".</li>
</ul>

I've stored my hashes in a text file called "hashes.txt" in my working directory. Let's give it a shot!

<code>john --wordlist=rockyou.txt --format=Raw-MD5 hashes.txt</code>

<img src="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/screenshots/sqlijohncrack.png" width="500">

Perfect! If we needed help tying usernames and passwords together, we can add these cracked passwords to a wordlist. Adding the usernames we discovered in our last SQL query to a second wordlist, we can use Hydra to brute force them like we did in <a href="https://github.com/mrudnitsky/dvwa-guide-2019/blob/master/low/Challenge%2001:%20Brute%20Force.md" target="_blank">Challenge 1</a>.

That was a little rougher than expected, but hey. Challenge complete!
