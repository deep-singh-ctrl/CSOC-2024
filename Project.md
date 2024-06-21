# Editing a Save file

This Project will most likely comprise of multiple game entries as I have trouble deciding which one to proceed with. For now I am going to go with Games released on Windows only. 

## Project 1 : Lethis : Path to progress

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

## Game 2  : 




