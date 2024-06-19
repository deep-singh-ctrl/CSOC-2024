# Introduction Challenges

## Challenge 1 : Create an account
We go to the signup screen and are asked to solve the emperor cipher. However it seems like cipher is not obtained by ROT3 as is the case for caesar cipher.


![Solve a monoalphabetic substitution cipher to create an account](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/59274f4c-3da8-4039-81f9-d5d1b0969f10)


Still this is a ROT-x sprt of cipher so we write a 
python script and try all possible combinations and bruteforce this. We load up our python3 Idle window and type the following script.


![Python script to bruteforce the emperor cipher by trying all shifts from 1 to 25](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/e64f40bf-f718-4491-adb3-eaa76ae50030)

And we got the reuslt for shift 9

![Shell screen showing the output for of the above script with the shift 9 text highlighted](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/4e0a82db-4acf-4400-8180-b508c284fe0f)


## Challenge 1 : Finding Flags

This introduces us to the interface. We simply need to enter the given flag into the window and hit enter.

## Challenge 2 : Great Snakes

We are given a .py file which we need to execute.

![Instructions for the second problem](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/a5c17dc7-262f-46c0-913e-e030191450ad)

We download and load this file and run it from the terminal. 


![Screenshot 2024-06-17 195105](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/ece82648-6ef6-4a4d-bfde-67b1b86676ab)


## Challenge 3 : Network Attacks

Here we need to connect to the server given in the descripton on the specified port and send a JSON object with some specific attributes. Fistly we install the pwntools package by using `pip install pwntools` , next we download the template and make the required changes. 

![Screenshot 2024-06-17 195524](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/89fffa3f-db7f-4dcb-a287-81e3d775aae1)


Just change the clothes parameter to flag as per the instructions and run. This gives us the flag which we can submit.

# General Challenges 

## Encoding

### ASCII

We are given an array of integers which we need to conver to thier ASCII values to get the flag. First we copy the array into the clipboard and load up idle from the terminal. Now we can define a list and iterate over it using the str() function each time to convert the integers to ASCII.




![Python Script for ASCII challenge](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/9d2b5a32-05ff-4b38-b671-28b894e7246f)

This gives us the flag when we run it. Onto the next

### Hex

Given a hex string we need to covert this back to ASCII to get the flag. In a hex string , a pair of characters represent a byte. So for instance if we have a hex string `4a3b` , The pair `4a` is one byte and the second pair `3b` is the second byte. To conver them to ASCII , we only need to look up the byte's integer values against an ASCII table. Fortunately there is already a function to do all that in python. We copy the hex string and go to idle to type the following program : 


![Python Script for Hex problem](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/2ef7574b-e0bd-4858-954f-c99dd36d0e7f)

And we got the flag. Onto the next..

### Base64

This challenge involves coverting a hex string into bytes object and then encoding it into base64. In a base64 encoded string , every character corresponds to 6 bits. So 4 characters are correspond to 3 bytes of data. This encoding is commonly used for images in webpages. We copy the hex string and convert it into a bytes object. then we can call the base64's b64.encode function to encdoe the it into base64. Here's a python program to do all this 


![Python Script for Base64 Problem](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/4e2bc743-3d8e-48f6-9a7b-49fa07ea6646)


### Bytes and Integers

We can see the steps to convert the string to a number written in the question.

![Screenshot 2024-06-17 190244](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/5416970c-1361-412f-97b6-1c185f0814e1)


1. Take the String
2. Convert the letters to their ascii values
3. Conver the ascii values to the hex values
4. Concatenate
5. Convert the hex so obtained into a decimal number

So to decode the message , all we need to do is to proceed in the reverse fashion. We Take the number ,  convert it into hex number , then break it into bytes , then convert the hex to ascii and the ascii to the characters. Fortunately there are already some useful functions we can use in the Crypto.util.number library. Lets import it and use the long_to_bytes() to get the flag.


![Screenshot 2024-06-17 201446](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/37b1dda8-c0bb-4a07-9ac9-d843de068e52)

Now we run it...

![Screenshot 2024-06-17 191540](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/ddb16c23-e116-459a-a08c-799ee31b133c)



So we got an error doing that. We need to install the pycryptodome library first. We head to the terminal and type sudo apt install python3-pycryptodome to get the package.Now we try to run the program again. (I had some issues with pip install so i looked up some articles online and [found this](https://stackoverflow.com/questions/75608323/how-do-i-solve-error-externally-managed-environment-every-time-i-use-pip-3). Now we run the program and found the flag. 


## XOR

### XOR starter

For this we have been given a string 'label' and we wish to xor each of these characters of the string with the number 13 and print the string (str) as flag : `crypto{str}`. We can easily achieve this by writing a small script. 

![Python Script for XOR starter](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/b04aa5ea-ef6a-4a13-83cd-424086fbd6c7)

This gives us our string str and consequently the flag. 

### XOR properties

For this challenge we are given some info (all are hexstrings) and we need to utilise this info to get the flag. So for some reason i cant import the pwntools library properly so i had to rely on my own xor function. Here's the script , it can be achieved without taking the bytes object as well but i stick to using this as i was making edits after i though the pwntools would be imported (it didnt)


![Python Script for Xor properties question](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/5209ac31-b187-417f-a85f-a7028e10ad1d)


```
Explaination : From the XOR properties we know that xor is communtative and associative , also 0 ^ a = a and a ^ a = 0.
Say the keys are k1,k2,k3. now , k1 ^ (k1 ^ k2) = (k1 ^ k1) ^ k2 = 0 ^ k2 = k2.
Similarly we can get k3.
Lastly we can xor the flag with k1,k2,k3 to get the flag.
``` 

running the program will yield the flag

### Favourite byte

We are given that the flag was xored with a byte to get the current hex string. We need to bruteforce with all 256 possible 8bit bytes to get the flag back. so we write a program that takes all bytes between 0 to 255 and xors it with each byte of the hex-string we have. 


![Python Script for Favourite Byte ](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/5f2ed5ac-24fb-48e4-9208-32f9f8fc6244)

It prints a long list of text and we see that the target byte corresponds to 17 in decimal. 


### You either know XOR , you don't

We are given a hex string which is a xor between a flag bytes ad key bytes. Now if we say that the flag is f and the key is k then hex_string : hs = f ^ k. From properties of XOR we know that f ^ k ^ f = k. We know the flag , well at least partially since all the flag formats here are of the type `crypto{}` so we can try to xor the current hex_string with this partial_key. Here's the script to achive this : 


![Screenshot 2024-06-18 120910](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/65424d93-ed70-4db5-b420-c3d908bde35e)


This yields the following output 

![Screenshot 2024-06-18 120858](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/2806f9f0-9d9d-4c80-8fa3-b6f3f4432331)

Now we can make a guess that the real key could be `myXORkey` let's try to xor the hex_string with the new key. However this time lets make sure that the number of bytes in the key and the string are the same. We can achieve this via the while loop or some other function. 


![Screenshot 2024-06-18 121801](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/dbe80300-b4ea-48bf-b04c-de439479bac7)

On running the above program , we got the key.


## Mathematics

### Greatest Common Divisor

This is a fairly easy challenge which illustrates the use of euclid algorithm. The euclid algporithm is a recursive way to find the gcd of two numbers. To clear this level we need to find the gcd of two numbers given. Here's an easy python program to do this 

![Screenshot 2024-06-18 122834](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/3196c1f4-2ed7-48a9-8c6a-34d04b639b66)

Afterwards just type the result into the text box to move ahead.

### Extended GCD

This is an extension of the euclidean algorithm. By the extended euclidean algo , we can write the gcd of two numbers as a linear combination of the two. So for instance we can write : `gcd(a,b) = ax + by` This can be achieved by writing all the steps of the algorithm and then substitiuting the values. [This](https://www.youtube.com/watch?v=hB34-GSDT3k) video was quite helpful. Here's a python program to achieve this. This program updates the values of x and y by at each step of the recursion stack. 

![Screenshot 2024-06-18 132813](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/6ae45909-eeb6-43fd-a78c-8858a3251302)

Afterwards we only need to type the smaller of the two results to proceed to the next level.

### Modular arithmetic 1

This level introduces a notation to represent modulus operations. The concepts are nothing new. 


![Screenshot 2024-06-18 134924](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/6587c767-11e7-471e-af94-9b912cd32e18)

The first one can be caluclated mentally. For the next one we can get the output by writing a small program in python , or we could do this directly from the Linux CLI. 

![Screenshot 2024-06-18 135027](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/f26daaa0-54f2-4dc9-98b9-d3a905c44091)

Just enter whichever is smaller , onto the next.

### Modular arithmertic 2

This article introduces us to fermat's little theorem as a useful theorem when working with modulus operations involving prime exponenents and modulus. The text however is a bit unclear as to what the theorem is and [this gfg article](https://www.geeksforgeeks.org/fermats-little-theorem/) is much more useful. 



![Screenshot 2024-06-18 141452](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/1f6ce02b-61c8-46ba-b7df-3182d1a764ff)

Since we already know that 65537 is prime and the number is raised to power 65536 ( aka 65537 -1) we can easily figure out the remainder by going through the above article. Onto the next

### Modular Inverting

This intoduces us to the concept of multiplicative inverse (In the context of modulus of course). Here since 1 is the identity element for this operation and let us say that we have an element g in the set of integers , then there is a unique d which , if multiplied to g will yield the modulo equal to 1. This is explained better in the text 

![Screenshot 2024-06-18 143702](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/84274bde-74b0-447f-ae8e-ec968da7f4fc)

It's easy to figure out the answer for this ,  we only need to iterate over from 1 and check which element is the inverse. Onto the next


## Symmetric Ciphers

### Keyed Permuations

Some text about AES being a 'keyed permutation' mapping every possible input block to a unique output block. This is important otherwise the output will be impossible to decrypt. To proceed , we need to enter the mathematical term for this one-to-one correspondece , in the format specified (this is very similar to one-one and onto functions aka invertible functions)


![Screenshot 2024-06-18 144719](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/075f461f-309a-48b5-a76b-9536631acd97)

### Resisting bruteforce

The text includes some info about the powerful security that AES 128 provides. To proceed we need to input the most advanced attack against AES encryption (Which is only a slight imporovement from bruteforce). It's given as a hyperlink in the paragraph.

![Screenshot 2024-06-18 153018](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/c2abf305-c7d0-40f1-a265-9dd384aac59e)


### Structure of AES

This gives us an overview of the inside process behind the encryption process of AES. I dont really understand it except the fact that the input array (of 16 bytes) is transformed into a 4*4 matrix and from there on lots of operations are performed to encrypt this and then finally we can transform this 4 * 4 matrix back to a single list of 16 byts.

To proceed we need to write a function matrix2bytes which takes a square matrix of dimension 4 and then transforms it into a single list of 16 bytes size. We can achieve this via a for loop , iterating over each row and appending it to a list using the extend function. Here's a script to do all this 

![Screenshot 2024-06-18 160021](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/8dfa666e-d641-4f80-969e-4a09dd20d3fa)

It's possible there could be other solutions but this one I found is easier to understand and is a lot more intuitive.


## RSA

### Starter 1

Introduces to the concept of modular exponentiation. To solve the problem we can use the pow() function in python to get the modulus easily.

![Screenshot 2024-06-18 200837](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/eaa79c33-984f-448a-8c1b-3ba48e5f8c8a)

Just use the command `print(pow(101,17,65537))` to get the number. Onto the next level.

### Starter 2

Encryption in RSA involves an exponenet `e` a number `N` which is usually the product of 2 primes `p`  and `q` such that `N = p*q`. The N and e are part of the public key in the RSA. Here in this problem to encrypt 12 using N as 17*23 and e as 65537 , we can again use the pow function as `print(pow(12,65537,17*23))` this yield us the number in encrypted form.


![Screenshot 2024-06-18 201218](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/ee949006-7307-4d8f-b28e-28f96bbc63ef)

Onto the next level..

### Starter 3



This segment introduces us to the concept of euler totient function(Represented as &#934(n) ) . The euler totient function has amazing properties when we solve it for prime numbers which have been discussed well in [this gfg article](https://www.geeksforgeeks.org/eulers-totient-function/) We are given 2 very large prime numbers p and q and given that N = p*q ,we need to calculate the totient of N. We can make use of the properties of the function in the article to do so. Here's a small program to calculate it.


![Screenshot 2024-06-18 203849](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/fc83f048-3f95-47c5-ad2e-e51ac6f4bb38)

Just copy paste the outputin the textbox and we are good to go..


### Starter 4

The post is about the modular multipilicative inverse. We are given two numbers (from the previous question) and we are asked to calculate the inverse taking the exponent as 65537 and the totient of the product of the two prime numbers

```
Approach

The totient is coprime to 65537 as 65537 is a prime number. [This](https://www.geeksforgeeks.org/multiplicative-inverse-under-modulo-m/) article on gfg discussess how we can use euclidean extended algo
(done previously in mathematics challenges) to caluclate the inverse for coprime numbers (under logarithmic time). Using this idea , we can modify the program to wrote before
to get the identity d

```

Here's the modified code : 

![Screenshot 2024-06-18 212039](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/a8f6b775-fd14-4d67-a2cd-067e731a027c)

Runningn this program , the value of y gives the required identity element.

### Starter 5

In this problem we are given a cipher (c) , we have the private key from before (d) and we also have the totient of p*q (N). We can decrypt the message M , by using M = C^d^ (mod N). Implementing this in Python program : (Can't fit all the big numbers in one row)

![Screenshot 2024-06-18 213049](https://github.com/deep-singh-ctrl/SDE_Roadmap_2024/assets/172205598/1a46ab19-97f2-4815-a409-03f2b0c1cb2c)

We get the required number from this operation.

### Starter 6

This article introduces to the concept of signatures and how we can verify that the message we sent is intact and has not been tampered with. 

```
Consider my message is M
My public key is P
My private key is p
My friend public key is Q
My friend private key is q

Here's a rough flowchart of what is happening

```

![Screenshot 2024-06-19 203436](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/0073d579-b867-43c1-9e23-56e6cb5a29f1)


To complete the challenge , we need to :

1. Hash the message
2. Encrypt it using our private key













