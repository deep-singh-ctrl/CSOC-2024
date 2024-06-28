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

We need to figure out some way to drop the remaining query after username or make the password paramter return true. Unfortuantely if you look at the filters above , most of our relational and logical operators have been banned. So let's use the list of all Logicaal Operators in SQLite and think of ways to bypass this. [Here is the list](https://www.javatpoint.com/sqlite-operators) , let's try those from which we can make possible queries (Obviously the filtered keyowrds have been ignored). Firstly i tried this query 

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


## Intro to Burp

This challenge more or less requires us to play around with the requests. First input anything in the login page.
![Screenshot 2024-06-28 150503](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/238ddef3-6b59-475f-bc66-d819669c1e38)
Now get to the 2fa page. Here no matter what you type , you cant access the flag page. Lets look at the request in burp:

![Screenshot 2024-06-28 150509](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/929c07ad-8097-4790-b286-0d9045f929ce)

I was playing around with the requests such as long input lengths for otp , changing the priority parameter etc and i ended up deleting the otp parameter. This gave the flag when sent.







