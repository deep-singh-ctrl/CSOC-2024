# WEEK 3

## Web gauntlet 1

Edit : On the problem page we see two links. One of them leads to a login page and the other is a webpage with /filter.php appended to the first URL. First let me explain what does filter mean(note that I did not understood this the first time , it took me a while to get this) , the filter webpage contains some SQL tokens such as AND , OR (depending on the round you are playing). To clear this challenge. We need to perform a SQLi attack but we are not allowed to use the keywords mentioned on the filter webpage. With that said , let us start the challenge.

![Screenshot 2024-06-24 120458](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/a489960c-44f6-45c5-9184-00448200c1cd)

### ROUND 1
Here we find that we are unable to login using our picoCTF
login credentials but we can see something interesting happening in the background when we do so

![Screenshot 2024-06-24 120726](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/2b1834ee-c86c-4f99-9bb7-725837f2ea60)

As you can see there is a SQL code written in the back. This indicates that we can maniuplate the login entries and instead of writing our username , we can inject some SQL code.
This vulnerability (or technique) is called SQL injection. Here is something we can enter : 

```
Username :  ' OR 1=1
Password :  ' OR 1=1
```
Why did I enter this? if you look at the query format above , the query in SQL now becomes ;

```
SELECT * FROM users WHERE username = ' ' OR 1=1
```

The WHERE filter in the query is broken , this is true for all rows and should return us all records on the system. But.......it didnt return us anything. It is at this point I realised I need to study SQLi in more detail so i head over to the portswigger.net to do their labs on this..

Okay now that we have some expereince , we can proceed forward. Let us look at the query one more time :

`SELECT * FROM users WHERE username = 'username' AND password = 'password'`

We can comment out the password check by the `--` text. Next we can try to login as admin. So our entries become:

```
Username :  admin' --
Password :  uasdkjnjcnqien( *can be anything)
```

And bingo , first round complete :

### ROUND 2

Here the filter webpage shows the following keywords : 

![Screenshot 2024-06-25 074637](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/66886d81-a579-4563-868a-1c7d638db320)

These are the keywords we can not use this round. If you check Hint no 4 , you will see that this is actually SQLite. SQLite allows us to comment in 2 ways (maybe more). [Refer this](https://www.techonthenet.com/sqlite/comments.php) for more information. Now we can use the same payload as before but we need to use th `/**/` symbol to comment. Here is the final payload we will use :

```
Username : admin' /*
Password : sadiuasndjn( *can be anything)
```

Onto the next round

### ROUND 3

Here are the filters for this round..

![Screenshot 2024-06-25 075127](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/b736dcb7-31ec-47f0-9b9b-bbf43b25eccb)

It seems we can still use `/*` But when we try it , nothing happense. If we look at Hint 3 , we can see why.

![Screenshot 2024-06-25 080138](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/a044cf3e-1299-40f1-87b4-cd872cc729a6)

Unfortunately i cant use the packet inspector i know here (due to some verification error on picoCTF side) So let's just assume that we /* character is there, but not visible to us. Fortunately there is another way to terminate queries using the `;`. Let's try it.. here is the payload

```
Username : admin';
Passwords : dioasdmiasm(* can be anything)
```

okay that worked. Onto the next round

### ROUND 4

Filters here. We can't use admin it seems.

![Screenshot 2024-06-25 080901](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/cf439dff-d13e-4338-ab08-8be6e403f25a)

Now what to do? Here we can use SQL string concatenation to make things work. (just remember this is SQLite and not SQL which means that the syntax will be slightly changed). Here is the final payload

```
Username : adm'||'in';
Password : daksndioa (*anything)
```

#### ROUND 5

Looks like we can use the same payload as we used before , the filter gives me an idea that we could have done the last one using UNION as well.

Finally we get the flag on the filter.php webpage. 

## Web gauntlet 2

This challenge is very similar to the last challenge. All that has changed is the filter.php , some more filters have been added this time. Let us take a look at the filters:
![Screenshot 2024-06-27 192353](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/49308e5a-18e8-4ba9-92a8-6c6c718e065f)
So for the username , since `|` is not filtered , we can use it and our username payload will be the same as before (concatenating adm and in) However this time we can see that the SQL query is diffrent from before.

`SELECT username, password FROM users WHERE username='hello' AND password='asda'`

We need to figure out some way to drop the remaining query after username or make the password paramter return true. Unfortuantely if you look at the filters above , most of our relational and logical operators have been banned. So let's use the list of all Logicaal Operators in SQLite and think of ways to bypass this. [Here is the list](https://www.javatpoint.com/sqlite-operators) , let's try those from which we can make possible queries (Obviously the filtered keyowrds have been ignored). 

## Web gauntlet 3

We can use the same payloads as web gauntlet 2.


## Irish Name Repo 1

This challenge series is very similar to the one which we did above. We can infact , recycle a lot of our payloads in from previous exercises. First we navigate to the login page and supply any username and password. Try to login.

![Screenshot 2024-06-28 123150](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/dc17440c-c8fb-456a-91f4-0499a96ee220)

Just a simple HTML error page. Next try to login using the payload `'` in username. We are doing this to check for possible SQLi injection vulnerability. 

![Screenshot 2024-06-28 122959](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/749e26ea-e85f-4439-8fee-459da5432808)

An error of http 5050 indicates something may have gone wrong on the backend (Aka the SQL query was messed up). Let's try to use our most basic payload here:

![Screenshot 2024-06-28 122835](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/aabcb3ec-0d09-4f80-8f58-1f209d10ce2b)

And we got in , onto the next..

## Irish Name Repo 2

I used the same payload this time and we got access. Onto the last one

## Irish Name Repo 3

This time we only are prompted for a password. We need to enter the correct password or figure out a workaround. Entering any random password is giving a Login error and entering a `'` gives a 5050 error. This means that the system is still vulnerable to SQLi attack. The hint says that the password is encrypted , maybe we could try to figure out something from the http packet inspection. Upon Inspection we find that there is a debug parameter set to 0.

![Screenshot 2024-06-28 124558](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/28d77a08-8b43-461a-8e5b-882cda3b7aba)

Look at the far right. Change it to 2 (maybe some other non zero value could work as well) and then send the request back. 


![Screenshot 2024-06-28 124614](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/8ebc6695-96b9-4c54-ac39-8745caaadd5d)

We can now see the SQL query being made. It seems that our password 'zxzz' is being changed to `mkmm`. This is ROT13 cipher. So all we need to do is to get a payload say `' OR 1=1;` , encrypt it using ROT13 and then send the request back (Since ROT13 can be decrypted by applying the ROT13 on the message again). Here is the final payload

```
Password : ' BE 1=1;
```

And we are in!!. Yay!!.

## Secrets

This website is supposed to have some hidden webpages. However we can't really find any hidden links anywhere so let us try to read the html for the pages. Upon doing so we , notice that the header tag has the file location of the assets insidde the `secret` directory.

![Screenshot 2024-06-28 135030](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/b5be03c7-eb87-4a92-8c9c-a350607969ae)

That looks suspicious. If we try to access the folder by typing  `.../secret/` into the URL , we are led to another Webpage. Let us inspect the element here , 

![Screenshot 2024-06-28 135130](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/9242c7b2-49dc-44fd-8a58-3102116a0eed)

As usual , navigate to the `hidden` subdirectory. 

![Screenshot 2024-06-28 135227](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/d2a75778-5979-4270-9b2f-1adb3d653b7f)

Another webpage and another redrection to `superhidden` subdirectory , let us try to navigate there.
We see that the webpage displays the message that it has the flag , however we can't really see it. But we can look at the HTML for this webpage and see if there is the flag here.

And we got the flag. Onto the next..

















## JaWT Scrathpad

This challenge requires us to login as admin to a site. Unfortunately when we try do so , we get an error. 

![Screenshot 2024-06-26 160928](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/a904b691-cc33-4a10-93fb-ec74cc35ea6d)

We see two hints on the page telling us about checking the cookies and to read up on JWT. Also the URL has hyperlinks to JWT.io and a git repo to John the ripper.

In Burpsuite, we see that we can intercept the cookie and change the value of JWT to the one of admin. However the problem is, We don't know the secret key for which the signature is created. Fortunately , we can bruteforce this key using John the ripper.  

![Screenshot 2024-06-26 160739](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/bae252dd-53f5-4523-b242-1f840af3b10f)

[This Blog](https://blog.pentesteracademy.com/hacking-jwt-tokens-bruteforcing-weak-signing-key-johntheripper-89f0c7e6a87) is useful for knowing how to use John the ripper. The installation instructions are there in the hyperlink John on the login page. 


So now all we need to do is to run john the ripper with the Original JWT token as paramter 1 and the wordlist (we will obviously go wit rockyou.txt) as paramter 2. Here is what we get:

![Screenshot 2024-06-29 111626](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/d36f7767-3c86-4dd7-95ab-e3ab8ecad020)


We can use this to create a forged token. Just login as any user then go to burp and capture the refresh request. change the jwt token to the one with admin as payload and the key as `ilovepico`. Forward this request to get the flag.

EXTRA : I tried to solve this problem with a python script first. 

![Screenshot 2024-06-29 115120](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/2202babb-e904-43c6-adab-d5b9d3d08e06)


However during debugging , i found that the jwt library changes ther order of claims in header. This leads to a diffrent hash being generated owing to the fact that a different base64 text was generated for the header. You could try some jwt crack code on git or modify this one to work as per your needs.


## Intro to Burp

This challenge more or less requires us to play around with the requests. First input anything in the login page.
![Screenshot 2024-06-28 150503](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/238ddef3-6b59-475f-bc66-d819669c1e38)
Now get to the 2fa page. Here no matter what you type , you cant access the flag page. 

![Screenshot 2024-06-28 150509](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/929c07ad-8097-4790-b286-0d9045f929ce)

Lets look at the request in burp:

![Screenshot 2024-06-28 150450](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/62855bd6-90e1-4014-b8d5-1e09506f201f)


I was playing around with the requests such as long input lengths for otp , changing the priority parameter etc and i ended up deleting the otp parameter. This gave the flag when sent.

## Who are you

The webpage wouldn't let us in and the attachd doc is a RFC manual. We see the prompt that only PicoBrowser users are allowed on the site.

![Screenshot 2024-06-28 203717](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c241e363-9542-47c4-8ebb-0ad543a26ca8)

Upon Inspecting the page ource , there doesn't seem to be anything weird going on here.

```
l>
<html lang="en">

<head>
    <title>Who are you?</title>


    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

    <link href="https://getbootstrap.com/docs/3.3/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>

    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>


</head>

<body>

    <div class="container">
      <div class="jumbotron">
        <p class="lead"></p>
		<div class="row">
			<div class="col-xs-12 col-sm-12 col-md-12">
				<h3 style="color:red">Only people who use the official PicoBrowser are allowed on this site!</h3>
			</div>
		</div>
		<br/>
		
			<img src="/static/who_r_u.gif"></img>
		
	</div>
    <footer class="footer">
        <p>&copy; PicoCTF</p>
    </footer>

</div>
<script>
$(document).ready(function(){
    $(".close").click(function(){
        $("myAlert").alert("close");
    });
});
</script>
</body>

</html>
```
Next we load up burpsuite and inspect the http request. We notice an intereesting header.

![Screenshot 2024-06-28 203933](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/631c64dd-aeba-464e-bea2-989bfeda7208)

User-Agent set to Mozilla. Let us try to change it PicoBrowser. Now the website says that the website only worked in 2018. Refer to the attached manual , there is a header under the general header that we can use and set the date to any date in 2018.

![Screenshot 2024-06-28 204123](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/af80820b-26f6-4891-9bd2-30dcbc8f9b98)

Next we see that the site does not allow users who can be tracked. I searched up the manual but could not find any header. Fortunately a quick google search reveals the DNT header. So we add this to the request.

![Screenshot 2024-06-28 204239](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/cc12a280-e960-4318-9e32-a375e391d04c)

The website says that only uers from Sweden are allowed. This means we need to spoof our IP address somehow. Fortunately there is a header called X-forwarded-for we can use.

![Screenshot 2024-06-28 204405](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c7cb58fe-d59e-4cd9-876f-94d9d8ce3082)

Next change up the language in the accept language header to ge the flag.

## Client-Side-Again

As per the hint , let us inspect the JS page source and specifically the JS for the submit button. It seems to bea regular JS but it has been obfuscated and made difficult to reverse read and understand. The var names are hexes , the string names are referenced by the array indices and there are 2 pretty useless functions here. Here is the page orignal source. 

```

<html>
<head>
<title>Secure Login Portal V2.0</title>
</head>
<body background="barbed_wire.jpeg" >
<!-- standard MD5 implementation -->
<script type="text/javascript" src="md5.js"></script>

<script type="text/javascript">
	
	var _0x5a46 = ['0a029}', '_again_5', 'this', 'Password\x20Verified', 'Incorrect\x20password', 'getElementById', 'value', 'substring', 'picoCTF{', 'not_this'];
(function(_0x4bd822, _0x2bd6f7) {
    var _0xb4bdb3 = function(_0x1d68f6) {
        while (--_0x1d68f6) {
            _0x4bd822['push'](_0x4bd822['shift']());
        }
    };
    _0xb4bdb3(++_0x2bd6f7);
}(_0x5a46, 0x1b3));
var _0x4b5b = function(_0x2d8f05, _0x4b81bb) {
    _0x2d8f05 = _0x2d8f05 - 0x0;
    var _0x4d74cb = _0x5a46[_0x2d8f05];
    return _0x4d74cb;
};

function verify() {
    checkpass = document[_0x4b5b('0x0')]('pass')[_0x4b5b('0x1')];
    split = 0x4;
    if (checkpass[_0x4b5b('0x2')](0x0, split * 0x2) == _0x4b5b('0x3')) {
        if (checkpass[_0x4b5b('0x2')](0x7, 0x9) == '{n') {
            if (checkpass[_0x4b5b('0x2')](split * 0x2, split * 0x2 * 0x2) == _0x4b5b('0x4')) {
                if (checkpass[_0x4b5b('0x2')](0x3, 0x6) == 'oCT') {
                    if (checkpass[_0x4b5b('0x2')](split * 0x3 * 0x2, split * 0x4 * 0x2) == _0x4b5b('0x5')) {
                        if (checkpass['substring'](0x6, 0xb) == 'F{not') {
                            if (checkpass[_0x4b5b('0x2')](split * 0x2 * 0x2, split * 0x3 * 0x2) == _0x4b5b('0x6')) {
                                if (checkpass[_0x4b5b('0x2')](0xc, 0x10) == _0x4b5b('0x7')) {
                                    alert(_0x4b5b('0x8'));
                                }
                            }
                        }
                    }
                }
            }
        }
    } else {
        alert(_0x4b5b('0x9'));

  </script>
<div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:gray">
<div style="text-align:center">
<p>New and Improved Login</p>

<p>Enter valid credentials to proceed</p>
<form action="index.html" method="post">
<input type="password" id="pass" size="8" />
<br/>
<input type="submit" value="verify" onclick="verify(); return false;" />
</form>
</div>
</div>
</body>
</html>
```

These are the Obfuscation Techniques used:

* **VAR NAMES** : In JS , the var names can include digits but cannot begin with a digit. The programmer has used a `_` sign to circumvent this
* **STRING ARRAY** : Instead of writing the text , the array indices are used.
* **IIFE** : Changes the order of the String array before verify() function.
* **USELESS FUNCTION** : A useless function is used to return the value of array indices , instead of referencing array indices directly.

To overcome this , let us copy the source code into a text editor. You can change the var names to anything you want. Instead of trying to understand the logic of the IIFE function , just make some changes and print the array after the code has ran into the console. Remove the useless function and use array indices directly. Infact , remove even the string array and use the text directly. Finally after making the changes , here is the Final Code:

```
<html>
<head>
<title>Secure Login Portal V2.0</title>
</head>
<body background="barbed_wire.jpeg" >
<!-- standard MD5 implementation -->
<script type="text/javascript" src="md5.js"></script>

<script type="text/javascript">

var string_array = ['getElementById', 'value', 'substring', 'picoCTF{', 'not_this', '0a029}', '_again_5', 'this', 'Password Verified', 'Incorrect password'];


var temp = function(v1, v2) {
    var val = string_array[v1];
    return val;
};

function verify() {
    checkpass = document.getElementById('pass')[value];
    if (checkpass[substring](0, 8) == 'picoCTF{') {
        if (checkpass[substring](7, 9) == '{n') {
            if (checkpass[substring](8, 16) == 'not_this') {
                if (checkpass[substring](3, 6) == 'oCT') {
                    if (checkpass[substring](24, 32) == '0a029}') {
                        if (checkpass['substring'](6, 11) == 'F{not') {
                            if (checkpass[substring](16, 24) == '_again_5') {
                                if (checkpass[substring](13, 16) == 'this') {
                                    alert('Password Verified');
                                }
                            }
                        }
                    }
                }
            }
        }
    } else {
        alert('Incorrect Password');
    }
}

</script>
<div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:gray">
<div style="text-align:center">
<p>New and Improved Login</p>

<p>Enter valid credentials to proceed</p>
<form action="index.html" method="post">
<input type="password" id="pass" size="8" />
<br/>
<input type="submit" value="verify" onclick="verify(); return false;" />
</form>
</div>
</div>
</body>
</html>

```

From the verify() function , you can reverse engineer the password. I am not writing it here because obviously that is the flag for this level!!

## JS Kiddie 1 (In progress)

First we click on the link are directed to the webpage with a text button and a submit button. Here is the HTML for the webpage. 

```
<html>
	<head>    
		<script src="jquery-3.3.1.min.js"></script>
		<script>
			var bytes = [];
			$.get("bytes", function(resp) {
				bytes = Array.from(resp.split(" "), x => Number(x));
			});

			function assemble_png(u_in){
				var LEN = 16;
				var key = "0000000000000000";
				var shifter;
				if(u_in.length == LEN){
					key = u_in;
				}
				var result = [];
				for(var i = 0; i < LEN; i++){
					shifter = key.charCodeAt(i) - 48;
					for(var j = 0; j < (bytes.length / LEN); j ++){
						result[(j * LEN) + i] = bytes[(((j + shifter) * LEN) % bytes.length) + i]
					}
				}
				while(result[result.length-1] == 0){
					result = result.slice(0,result.length-1);
				}
				document.getElementById("Area").src = "data:image/png;base64," + btoa(String.fromCharCode.apply(null, new Uint8Array(result)));
				return false;
			}
		</script>
	</head>
	<body>

		<center>
			<form action="#" onsubmit="assemble_png(document.getElementById('user_in').value)">
				<input type="text" id="user_in">
				<input type="submit" value="Submit">
			</form>
			<img id="Area" src=""/>
		</center>

	</body>
</html>
```

Look at the top of the HTML page , the browser first sends a AJAX request through the get function of the jquery library. Open up the console for whatever browser you are using and head refresh the page. Make sure that your browser can intercept http and xml requests. Now we can see the bytes list from here. Here it is.

![Screenshot 2024-06-30 161704](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/53dc0f5a-7d20-4c9d-ab70-36ff2c833064)

Or if you want to copy it :
```
bytes = [156,255,80,255,117,10,239,248,152,253,120,232,36,127,116,255,151,235,25,172,215,0,56,102,219,174,30,15,36,188,93,90,249,36,32,45,123,73,191,151,236,241,151,68,144,250,157,130,1,180,20,85,213,2,157,248,68,255,250,13,60,66,249,82,187,157,29,222,29,30,0,252,126,251,95,0,174,72,194,108,29,101,70,21,121,40,26,132,73,119,254,237,73,192,96,219,137,80,89,71,0,145,1,152,0,69,254,71,0,65,68,35,0,0,119,114,13,222,68,119,1,0,78,40,155,111,95,90,164,0,26,2,0,245,186,0,84,0,0,233,145,46,110,49,48,16,78,223,135,64,197,10,0,120,0,243,252,62,144,188,21,61,1,110,148,208,22,114,160,31,156,17,45,59,72,237,74,218,0,8,32,123,136,65,179,150,32,56,206,43,240,9,156,225,69,54,226,158,106,148,62,48,1,232,173,0,239,248,243,206,82,255,241,252,56,55,152,132,108,181,78,254,175,251,60,183,38,231,63,123,204,48,43,13,131,4,113,75,243,215,32,200,144,195,29,233,196,63,3,190,139,207,89,28,107,159,185,101,59,120,121,12,245,116,64,96,250,187,241,234,231,207,213,239,119,191,233,71,205,127,144,40,251,253,173,186,246,10,227,252,202,242,163,74,237,33,75,49,205,74,154,165,126,231,30,231,232,199,118,65,211,98,204,7,250,244,141,155,243,123,82,137,252,35,183,201,132,91,252,37,244,56,188,86,125,103,216,248,215,146,144,149,21,164,233,219,127,127,207,208,30,154,111,203,63,127,141,231,146,5,20,4,81,239,38,36,19,191,63,61,183,223,215,205,210,239,168,135,148,201,39,248,212,191,160,151,116,19,150,99,249,141,111,188,0,225,193,61,73,140,160,56,23,53,48,5,99,100,175,250,125,151,253,12,150,85,41,72,206,97,52,79,88,196,130,26,157,254,185,181,42,146,217,255,24,125,155,88,111,116,167,62,238,36,52,95,57,54,126,233,184,143,46,183,234,73,183,108,163,228,218,233,129,44,169,191,74,0,30,126,245,10,249,245,241,65,191,245,73,209,50,140,26,72,132,223,181,204,200,123,185,186,183,218,175,228,249,75,180,91,229,252,193,203,187,253,52,166,28,117,119,13,238,134,74,227,127,71,251,237,50,191,61,76,230,90,241,178,221,233,202,254,211,228,156,60,202,241,71,49,24,90,187,3,245,247,159,124,157,250,227,18,150,50,49,101,86,235,162,234,57,124,108,116,245,226,190,28,43,129,220,86,245,85,107,38,215,223,119,242,72,140,213,103,209,194,70,30,96,111,204,128,234,55,184,247,205,49,227,5,220,101,80,171,155,217,87,33,26,173,127,187,128,253,215,111,203,54,210,243,29,237,148,204,235,202,131,191,191,211,157,54,147,104,188,87,4,251,25,17,185,219,247,124,135,228,176,223,135,196,157,130,215,206,124,122,136,248,28,23,175,56,104,209,253,47,161,236,61,252,147,140,86,102,185,82,110,231,91,251,245,216,243,254,236,176,127,134,31,135,152,251,90,0,216,127,102,56,99,56,64,204,61,95]
```
Next lets look at the function assemble_png. This function takes the user input as u_in. It then assigns this to the key if the length of input is 16. Next the decryption alogrithm is run and finally the result array is converted to a base64 URL for the image. 

Here are the steps in detail :

* *Decryption* : Run the algorithm for decryption. The key here is supposed to be a 16 element long string consisting of digits from 0 to 9 as is evident from the line where the shifter is set to the ascii of the element -48 which is the ascii of '0'

* *Conversion to typed Array* : The result array of the last step is converted to an array where each of the entries is a bytes object

* *Conversion to string* : Each element of the result array is converted to a character. Note that since the bytes array does not contain any element greater than 255 , all entries of this string will have a unicode (in decimal) less than equal to 255.

* *Base64 encoding* :The string is then encoded into base64 before being sent to as png data.


To get the flag , we need to reverse engineer all of this. We know that the first 8 bytes or the magic number of a png header are fixed. This means that we can possibly try to guess some values of the shifter in the loop and thus lower down our hit and trials for the key. 

Some additional Useful programs : you can try to write a array searcher for the reverse engineering process, Here is simple implementation in C++

```
#include <bits/stdc++.h>
using namespace std;

main(){
    int choice;
    int finder;
    vector<int> bytes = {156,255,80,255,117,10,239,248,152,253,120,232,36,127,116,255,151,235,25,172,215,0,56,102,219,174,30,15,36,188,93,90,249,36,32,45,123,73,191,151,236,241,151,68,144,250,157,130,1,180,20,85,213,2,157,248,68,255,250,13,60,66,249,82,187,157,29,222,29,30,0,252,126,251,95,0,174,72,194,108,29,101,70,21,121,40,26,132,73,119,254,237,73,192,96,219,137,80,89,71,0,145,1,152,0,69,254,71,0,65,68,35,0,0,119,114,13,222,68,119,1,0,78,40,155,111,95,90,164,0,26,2,0,245,186,0,84,0,0,233,145,46,110,49,48,16,78,223,135,64,197,10,0,120,0,243,252,62,144,188,21,61,1,110,148,208,22,114,160,31,156,17,45,59,72,237,74,218,0,8,32,123,136,65,179,150,32,56,206,43,240,9,156,225,69,54,226,158,106,148,62,48,1,232,173,0,239,248,243,206,82,255,241,252,56,55,152,132,108,181,78,254,175,251,60,183,38,231,63,123,204,48,43,13,131,4,113,75,243,215,32,200,144,195,29,233,196,63,3,190,139,207,89,28,107,159,185,101,59,120,121,12,245,116,64,96,250,187,241,234,231,207,213,239,119,191,233,71,205,127,144,40,251,253,173,186,246,10,227,252,202,242,163,74,237,33,75,49,205,74,154,165,126,231,30,231,232,199,118,65,211,98,204,7,250,244,141,155,243,123,82,137,252,35,183,201,132,91,252,37,244,56,188,86,125,103,216,248,215,146,144,149,21,164,233,219,127,127,207,208,30,154,111,203,63,127,141,231,146,5,20,4,81,239,38,36,19,191,63,61,183,223,215,205,210,239,168,135,148,201,39,248,212,191,160,151,116,19,150,99,249,141,111,188,0,225,193,61,73,140,160,56,23,53,48,5,99,100,175,250,125,151,253,12,150,85,41,72,206,97,52,79,88,196,130,26,157,254,185,181,42,146,217,255,24,125,155,88,111,116,167,62,238,36,52,95,57,54,126,233,184,143,46,183,234,73,183,108,163,228,218,233,129,44,169,191,74,0,30,126,245,10,249,245,241,65,191,245,73,209,50,140,26,72,132,223,181,204,200,123,185,186,183,218,175,228,249,75,180,91,229,252,193,203,187,253,52,166,28,117,119,13,238,134,74,227,127,71,251,237,50,191,61,76,230,90,241,178,221,233,202,254,211,228,156,60,202,241,71,49,24,90,187,3,245,247,159,124,157,250,227,18,150,50,49,101,86,235,162,234,57,124,108,116,245,226,190,28,43,129,220,86,245,85,107,38,215,223,119,242,72,140,213,103,209,194,70,30,96,111,204,128,234,55,184,247,205,49,227,5,220,101,80,171,155,217,87,33,26,173,127,187,128,253,215,111,203,54,210,243,29,237,148,204,235,202,131,191,191,211,157,54,147,104,188,87,4,251,25,17,185,219,247,124,135,228,176,223,135,196,157,130,215,206,124,122,136,248,28,23,175,56,104,209,253,47,161,236,61,252,147,140,86,102,185,82,110,231,91,251,245,216,243,254,236,176,127,134,31,135,152,251,90,0,216,127,102,56,99,56,64,204,61,95};
    while(true){
        cout << "Enter value to find \n";
        cin >> finder;
        if(finder == -1)
            break;
        auto it = find(bytes.begin(), bytes.end(), finder);


        if (it != bytes.end()) {
        cout << "Element found at index: " << distance(bytes.begin(), it) << endl;
        } else {
        cout << "Element not found." << endl;}
    }

}

```

We will need this a lot so pay attention to it : 

Next we need to know the magic numbers for png header. Here they are , from wikipedia : 

![Screenshot 2024-06-30 162953](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/aaa9ebf3-f61e-417d-8c8a-53107e6491cc)

Just Convert these to decimals to work upon easily :

```
137 80 78 71 13 10 26 10

```

Now look at this code block of the JS : 

![Screenshot 2024-06-30 163205](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/2934fa1e-42d5-47ea-9c66-bb5edcbb597a)

See how the values of the result array are update? You can start with putting key to all 0s to get a feel of the algorithm. Remeber we need to set up our values in such a way that we have string of digits from 0 to 9 and the header that is result[0] , result[1] , etc match the header of the PNG file. 












