# Editing a Save file

This Project will most likely comprise of multiple game entries as I have trouble deciding which one to proceed with. For now I am going to go with Games released on Windows only. 

## Game 1 : Lethis : Path to progress

This is a city building game. I chose this as most city building games have multiple resources and it would be a nice exercise to meddle with it. First , we look into the installation directory of the game. 
![Screenshot 2024-06-21 153115](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/a446e5a0-aafd-4bbb-ad3f-d363cff3642a)
We can see that there is a file called saves already in it. I have already created two saves in the game. Let us see what these files are...
![Screenshot 2024-06-21 153257](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/e3db8af8-ee5d-43a9-b6c2-1256ca5de9bf)
Ouch! These saves are in xml format which can be easily edited with any text editor. However it might still take some time to search for the money parameter , hence we type `vimdiff newSave1.xml newSave2.xml` to load the vimm text editor in and compare the two files.

![Screenshot 2024-06-21 153415](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/940489c1-2e56-4d03-8574-abd19398069f)

So we found the moneyManager over here. Now we exit vim , go to nano , search for the tag and then edit and save it.

![Screenshot 2024-06-21 154950](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/464c4860-fcb7-47c0-8bb5-561958286a80)

This is the game before. We have 20,000 coins

![Screenshot 2024-06-21 155018](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/fc2643e0-6bc1-402d-ae88-409c813c6d26)

And this is the final result after editing the file. This was a really easy one so look forward to something else in the future.

## Game 2  : Game Dev Story

### Method 1 : Using a tool

I was looking for suitable android games and found this one had a pc port. It is a cute simulation game which has you running a video game company and developing video games with a variety of different genres and types to choose from. 

![Screenshot 2024-06-22 203309](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/dc6879ea-162e-4619-823c-4295722bbffd)

This is what the interface looks like. The objective is to develop the games with your employess. Each game has 4 attributes : Fun , Creativity , Graphics and Sound. During production , the employees will work on the game development and you can see the values that are added each time above thier heads.

Let us try to change the game code in such a way that the values added each time are huge

I will be doing this with a tool (cheat engne) but hopefully will be able to maniuplate save file to do so as well. 

![Screenshot 2024-06-22 203151](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/f39f5127-48ee-45dc-a1ee-eb45ff2ef338)

The Interface for Cheat engine is really easy and it will be redundant to write the steps here. In short we need to pause the game each time , wait for the values to change and then update then perform next search. This process is called Memory Scanning and it is explained quite well [here](https://gamehacking.org/library/70). Or you can refer to the inbuilt cheat engine tutorial.

Now that we have the address , we can proceed with the auto assembler , finding the function that writes to it and injecting our own code here. All of this is very simple with the tool. Here is the final code that we added in assembly : 

![Screenshot 2024-06-22 200923](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/b7c0d2e2-4a3d-4fbb-b510-0bb84368a506)

As you can see , the modified code directly adds the value 100 to the address. This was not the case with the original code which added the value of a 2nd address into it. (I'm sorry for not taking a scrrenshot of the original code).

Now that we have done these changes. we see that : 

![Screenshot 2024-06-22 203006](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/cecb0825-062f-4378-b5eb-b2297348f396)

Yess!!. The conde injection was successful. Now we will try to do it by modifying save file. Although I am not sure 100 % if i can do this (hence i added this method here just in case i cant do it before the deadline)

### Method 2 : Comparing and editing save files.








































## Learnings from the Project
### Game1
1. Vim commands , vimdiff
2. XML format

## Game2

1. Not much as of now.
2. 



