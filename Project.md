# Editing a Save file

This Project will most likely comprise of multiple game entries as I have trouble deciding which one to proceed with. For now I am going to go with Games released on Windows as it is easier to locate savefiles for such games. 

Edit : *...This edit was added after Week 3 when the Game1 was hacked and Game2 was hacked using the tool...* 

I fail to find suitable games to hack over here. Here are the problems I have encountered so far :

* **TOO EASY** : Save files are literally just a JSON or XML which you can edit in notepad. So it doesn't really pose a challenge.
* **DONE ALREADY** : These save files are usually encoded into some base64 or other formats and they have already been edited by community memebers with detailed step by step guides online.
* **CLOUD SAVES** : How will you edit save files when they are not even on your PC? Most modern games have a multiplayer element and thus employ cloud saves which uses advanced techniques to get around.
*  **UMM WHATIS THAT?** : Binary + Random save location of bytes each time. Encrypted possibly. Makes it impossible to compare the hexdumps to figure out the requisite byte to edit.

So I might go for older games. Possibly late and early 2010s as the current gaming scenario is very much against save file editing. Its not that the games can't be hacked. You can very easily use tools such as cheat engine or Sqular or scanmem (for linux). However there is nothing to analyse if you go down that route. So I will probably go for older games now. 

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

This time we will compare and try to figure out at which byte the money value is stored in the save file. Before we proceed with that , [her is a video]() on the details of little endian and big endian way to store data in the RAM. Now we can start.. I have already made 4 save files from the game with minimal changes. Starting from a money value of 450 at save 1 , we decrease in arithmetic progression to 360 at save 4 , subtracting 30 each time. 
![Screenshot 2024-06-22 205335](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c70ab8d1-fd0e-4fb8-8dc0-8acf294a3916)

Let me show you the complete contents of each folder in linux...

![Screenshot 2024-06-22 210528](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/2363bd12-fc38-4a09-a5c0-52486d1b8436)

The game has 2 options , either you can save manually or the gameautosaves. We will be working with 0001 for each because I assume that is the manual save. Let us start with vimdiff. This is what we get

![Screenshot 2024-06-23 123037](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/1820236b-f17b-44e3-81f8-8ec99f784ae9)

Thankfully there is not a lot of changes reflected here. The save games are all binary formats and we are basically comparing thier hexdumps. Still , to make our lives easier , we will use the `comm` command preceeded by sorting thhese hexdumps to get only the lines where the changes have been made (aka , we need lines unique to file 1 and file 2) First we will sort all the files 

```
sort sav1 > ssav1
sort sav2 > ssav2
sort sav3 > ssav3
sort sav4 > ssav4
```

Next we will use the comm command to record all the differences , so we can use `comm -3 ssav1 ssav2 > diff_1_2` to get differences stored in a file called diff_1_2 (differences betwwen sav1 and sav2)

Next we will concatentenate all these results. Here's a bash script to do this :

![Screenshot 2024-06-23 130406](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c51511dc-18eb-4a2e-a8a0-5dbb1a04cac8)

So why did i do all this? Why did i store all these different lines in a seperate file? For one , it makes it easier to browse through the changes (without focussing on the lines which are the same for the files) , secondly , i did this to eliminate false positives. That is , suppose i search for a value of 450 on sav1 and found the value at some address and at some offset. I can then run through the file and see if this address changes throughout the other files , if it does , then that is a promising address otherwise we can ignore it. Unfortunately searching the files for the values does not yield anything useful(the search results are all at different addresses)

![Screenshot 2024-06-23 140810](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/80db1bbb-6e1c-4901-b3f2-dc8962fd7b46)

Save1

![Screenshot 2024-06-23 140817](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/22824a0f-46e0-4197-b074-6a305e9e3e93)

Save2

![Screenshot 2024-06-23 140827](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/03c72ba3-7702-474b-adb0-2cb9bc90e690)

Save3

![Screenshot 2024-06-23 140834](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/ab37d225-665a-4aec-9ed3-df1d28d50d8b)

Save4

Please ignore the value in searchbox (it automatically swithces to the latest search but the address remains same as the last search) Now you can see that i am running a pretty big number of checkboxes (8bit , 16bit , float ,double , arrays , little and big endian) but i dont find a common memory address which i can pinpoint as the location where money is stored. 














































## Learnings from the Project
### Game1
1. Vim commands , vimdiff
2. XML format

## Game2

1. Not much as of now.
2. 



