# WEEK 3

## Web gauntlet 1

Before procedding forward with this challenge we will donwload burpsuite on our pc. Next we see that there are two links associated on the problem page.Let's click on them.

![Screenshot 2024-06-24 120458](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/a489960c-44f6-45c5-9184-00448200c1cd)



One of the webpages is called filter , the other is a login webpage. Here we find that we are unable to login using our picoCTF
login credentials but we can see something interesting happening in the background when we do so

![Screenshot 2024-06-24 120726](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/2b1834ee-c86c-4f99-9bb7-725837f2ea60)

As you can see there is a SQL code written in the back. This indicates that we can maniuplate the login entries and instead of writing our username , we can inject some SQL code.
This vulnerability (or technique) is called SQL injection. Here is something we can enter : 

```
Username :  ' OR 1=1
Password :  ' OR 1=1
```
Why did I enter this? if you look at the query format above , the query in sql now becomes ;

```
SELECT * FROM users WHERE username = ' ' OR 1=1
```

The where filter in the query is broken , this is true for all rows and should return us all records on the system. Let us run this and capture the respoinse using burp suite.
