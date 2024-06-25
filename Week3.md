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





