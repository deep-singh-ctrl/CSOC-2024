# Bandit Writeup

## Level 0 :

Login to the server using ssh on port 2220 with username bandit0. ssh bandit0@bandit.labs.overthewire.org -p 2220

## Level 0-1 :

The password is stored in readme file in bandit0 subdirectory in home. cat readme. Paste password when trying to login using bandit1 username. Code same as above. Make sure to logout first using logout command.

## Level 1-2 :

Password for the next level is stored in a file called - (special character). cat - doesnt work instead it just repeats whatever i type on terminal. Use the full path of the file to avoid : cat ./- . copy the password and onto the next level

## Level 2-3 :

Password in a file name called “spaces in this filename”.  I used cat “spaces in this filename” , to deal with this but its also possible to escape the spaces with a backlash : cat spaces\ in\ this\ filename 

## Level 3-4 :
Password in a hidden file inside inhere directory. Move to inhere directory using cd inhere. Use ls -a , to show hidden files. Finally use cat .hidden to reveal the password. (Hidden file names are preceded with a dot. )

## Level 4-5 :

Password in a human-readable file. Use ls to display all filenames. We see they have a hyphen so we use cat ./-file0x and iterate through all the files one by one replacing x. The files which are not human readable show up as garbage. 

##  Level 5-6 :

Password stored in some file in the inhere directory. This directory contains many sub directory. Additionally we are given that the file is 1033 bytes and is non executable. We write find . -type f -size 1033c ! -executable to return non-executable files in the file hierarchy of size 1033 bytes. The result throws up the location of the desired file.

##  Level 6-7 :
Password stored somewhere on the server. We start with pwd , currently we are in the home directory. We go one step up to the root directory by using cd .. . From here we are given that the file is owned by user bandit7 and by group bandit6 and also is of 33 bytes. We type these options on the find command. find . -type f -size 33c -group bandit6 -user bandit7. Many of the directories show permission denied as we don’’t have read permissions. We manually scroll till we find a file location. We use cat command on that file location to get the password.

##  Level 7-8 :
The password is next to the word millionth. Simply using cat shows that the file is huge and it would be time consuming to search for the word manually. We type nano data.txt. This opens up the data.txt file in the file editor. We hit ctrl+w and search for the world millionth. Hitting ctrl+w and enter again shows a message that this is only one occurrence of the word. We copy the password and proceed.

##  Level 8-9 :

The password is the line of text that occurs only once. Firstly tried to use uniq -c data.txt and scroll to the last entry to get the line that occurs only once. However it returned 1 prefixed to various passwords. Found out that uniq only compares adjacent lines. So we need to use sort command with uniq. We enter sort data.txt | uniq -u. This returns only a single line. Later confirmed that this was indeed the password as it occurred only once(using nano).

##  Level 9-10 :
The password is preceded by ‘=’ characters and is the one of the few human-readable lines of text. We type strings data.txt | grep “=”. This passess the output of strings data.txt (the human readable part) to the grep command to search for “=” sign. The output is a few lines of text which can be easily scanned for the password.

## Level 10-11 :

The password is in the data.txt file. However to access it we need to decode the text first. To convert the base64 text into ASCII , we use base64 -d data.txt. This returns the ASCII equivalent of the text along with the password. 

## Level 11-12 :

The password is in data.txt and has been encoded using rot13 algorithm. We can use tr command to translate the text. Since tr can only work on one key at a time , i have used piping twice to achieve the translation - cat data.txt | tr ‘a-z’ ‘nopqrstuvwxyzabcdefghijklm’ | tr ‘A-Z’ ‘NOPQRSTUVWXYZABCDEFGHIJKLM’  

## Level 12-13 :

The password is in data.txt which is a hexdump for a file that has been compressed multiple times. We proceed in reverse fashion to get to the password. First create a new directory in /tmp using mktemp -d /tmp.  Then copy the data.txt file to the new directory using cp command ,  call it dat. We convert this to binary using xxd -r dat trans.  Now we use file trans to check for the file type. This is a gzip compressed data. Simply running gzip -d trans does not work so rename this to trans.gz using mv command. Now run gzip -d trans.gz. We get a new file. Similar as above this is a dzip2 compressed file. We can decompress this using dzip2 -d trans.dzip2. Repeat the steps till we get a POSIX tar archive(GNU) upon running the file command. We decompress this 3 times , using tar -xvf trans to get a file called data8.bin. This is gzip compressed , we decompress it as before. Finally we get an ascii type file , containing the password.

## Level 13-14 :

For this level , the password is stored in the /etc/bandit_pass directory in a file called bandit14. But the current user , bandit13 does not has read permissions for this file. We firstly need to login into the bandit14 using the private key. We run the command ssh -i sshkey.private bandit14@bandit.labs.overhtewire.org -p 2220.  This logs us into the server as bandit14. Now we navigate to where the file is using the cd command and copy the password. Onto the next level.

##  Level 14-15 :

For this level , we need to send the current level password to the localhost on server 30000. We first need to establish a connection to the localhost on the specified port. So we use the nc command. nc localhost 30000. After this we paste the password and we obtain the pass for the next level.

##  Level 15-16 : 
Similar to the previous level , we need to send the current level password to localhost using ssl encryption on port 30001. So first we open up an ssl connection to the localhost on port 30001 by entering openssl s_client -connect localhost:30001 . This outputs i presume ,logs pertaining to the handshake process , (no error seems to be reported) , we then paste the current level password and onto the next level.

## Level 16-17 : 

Firstly we need to determine which of the ports from 31000-32000 on the local host are open. We can use the nmap command to do so. nmap -p 31000:32000 localhost. This command outputs 5 ports which are open. Next we test whether the ports can understand ssl by the openssl command. We type openssl s_client -connect localhost: <ports> and cycle through each port we got throught the portscan earlier. From the 4th port , we get an rsa key which we can use to login as bandit17. We navigate to the /tmp directory and create a temporary directory using mktemp -d command as in level14. Next we create a file named pass17 and copy the rsa key into it. We use the commands chmod o-r pass17 and chmod g-r-w pass17 to remove the excess permissions. Finally we login as bandit17 as we did before - ssh -i /tmp/<randomd-directory>/pass17 bandit17@bandit.labsoverthewire.org -p 2220.

##  Level 17-18 :

We are given two files password.old and password.new . The password is the only line of difference between these two. We run the diff command as diff passwords.old passwords.new . The second line of output is the password we want. We copy this and then move onto the next level.  

##  Level 18-19 :

We cant login as bandit19 even after entering the correct password as the server logs us out immediately. The user bandit19 must have edited their .bashrc file to make sure we are logged out as soon as we login. The password is located in a file the home directory. We can access this using the ssh as we have the password , without logging in. 

## Level 19-20 :

We run the ls command. We have a file in the home directory of bandit 19 which is called bandit20-do. We type file bandit20-do to check which kind of file is this. It says its a setuid ELF 32bit LSB executable. This should help us access the password for level 20 by teemporarily  giving us user 20 privileges. We type ./bandit20-do cat /etc/bandit_pass/bandit20 to get the password for the next level. 

## Level 20-21 : 

The home directory contains another executable file that establishes a connection with localhost on the port that we pass as argument. Firstly i tried portscanning to check for all open ports on localhost. However none of the ports work as expected , that is , they dont output the password for the next level when the current level pass is passed as input ( or dont establish connection in some cases). It seems as though we firstly need to create a tcp server that is listening on one port not in use. We can make a onetime server that sends data and then terminates using the nc command. We can use piping to send the password for the current level. We type echo “current pass” | nc -l -p 1234 &. The & sign send this to the background. Next when we connect on port 1234 using suconnect , we get the password.

## Level 21-22 :

We navigate to /etc/cronjob.d . Out of the many files here , one catches interest which is cronjob_bandit22. We type cat cronjob_bandit22 and the output is the path of a directory. We navigate to the said directory and vie the contents of the file through the cat command. In the contents of the file , basically the password for the next level is redirected to a file in /tmp subdirectory. So we just move to there and get the password.

## Level 22-23 :

As usual , check the code for the script file cronjob_bandit23.sh. 

```
myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

According to the script. Currently the password for bandit22 is being stored in a file named  whatever the md5 hash of the string ,  gives in the /tmp subdirectory. This script would be executed for the bandit23 also. Hence there should be a file in the /tmp subdirectory corresponding to the pass for level 23 and all we need is to get the name (the hash) for it. We just copy the command and run as bandit23 echo I am user bandit23 | md5sum | cut -d ' ' -f 1 .  We ge the password and move to the next level.

## Level 23-24:

As before ,we open up the cronjob_bandit24.sh for inspection to get the following script

```
 #!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
Done
```

This script seems to suggest that all the scripts in the cd/var/spool/bandit24/foo directory are executed and then deleted when this script runs.(since whoami returns bandit24). We check and confirm that we do have write permissions for /var/spool/bandit24 so we can create our own script to get the password. So we need to write some script to get the pass for level24. Lets first navigate to the /tmp directory and create a radom subdirectory name , say xyz. Now we create a file named pass.sh with the following code

```
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/xyz/pass25
```
 
Since pass25 is not owned by bandit24 , we will grant all permissions to the files in the xyz subdirectory to avoid any permission issues using chmod 777 -R /tmp/xyz .  Now since the cronjob_bandit24 is executed every minute , we simply check after sometime to find the password in pass25 file.

## Level 24-25 :

For this we need to enter the current level password followed by the four digit code (which we need to bruteforce) into the localhost server on port 30002. First we open up the connection using the nc command. nc localhost 30002.  Then we type the password one by one and the 4 digit code. This will take a long time so will try to write some script. Head to the /tmp/xyz directory , we will store all the input attempts on a txt file and their results on another txt file. Code for brute.sh script (which we will execute)

```
#!/bin/bash

For i in {0000..9999}
do
	Echo “Pass25” $i > attempt.txt
done
```

Now in the terminal we can do nc localhost 30002 < attempt.txt > result.txt

Finally we will filter out the result which does not contain “Wrong!” in the line of result.txt using the grep command inverted search. grep -v “Wrong!” result.txt

## Level 25-26 :

This was the weirdest level ever. Firstly we run the ls command and we see that we already have the sshkey for level 26. We try to login by entering ssh -i bandit26.sshkey bandit26@bandit.labs.overthewire.org -p 2220. This logs us in but logs us out immediately. We are given that the shell for bandit26 is not /bin/bash so we check the /etc/passwd file for bandit26 using grep. Now we see that that instead of the shell being /bin/bash it is /bin/showtext. This file contains a script that executes more command on a file called text.txt , followed by exit. We need to stop the more from getting executed fully so we make the terminal size smaller and then run the login trial again. We press v to enter the visual mode from the command mode in vim and then we type :shell. This allows us  access as bandit26. We can then get the password by going into the usual /etc/bandit_pass subdirectory.

## Level 26-27 : 

We are already given a setuid executable in the home directory. Just execute this and get the pass for level 27.

## Level 27-28 :

We need to clone a git repository and access it to get the next level password. But we don't have write permission for the home directory so lets create a temporary directory in /tmp/xyz name. Now we can clone the git repository using the git clone command as- git clone ssh://bandit27-git@localhost/home/bandit27-git/repo. The password is inside the README file for this repo folder (it did take many tries before i realized the url i am typing is wrong).

## Level 28-29 : 

As before we cloned the git repository and we access the readme file inside of it. It seems as if the credentials have been censored , so we check the git log for the file. One of the change is written as hide info leak. To get the details of the commit git log -p , to get the details for the commits. And we get the password. 

## Level 29-30 :

As before we copy the repository into our temporary directory. We see that the password is not in production is written in the readme file. Checking the commits does not reveal the password either. We check the git branches. By typing git branch, the branchches are displayed. We checkout the branches one by one using the git checkout and ls command , until we find the password in the …/dev branch. 

## Level 30-31 :

Clone the repository again. We check for the branches(we are currently in the master branch and there aren't any other branches) and the commits(there is just one commit). So we check if there have been any special commits with the git tag command. There is one called secret so we use the git show command and we have the password.

## Level 31-32 : 

So we clone the repository again as before by making the temporary directory. The README.md says we need to push a file into the remote repository with the text may i come in , so we issue the command echo “May I come in?” > key.txt. However nothing seems to happen. We check the repository once for for any possible hidden files( ls -a) and we find a .gitignore file. We enter cat .gitignore to see that the *.txt* has been written, which could be why our file is being ignored. We remove this file using rm command. Now we can push the changes in the repository. First we implement the git add . command. After which we give the name to the commit using the git commit -m “Key.txt added”. Finally we can push the change using the git push master origin. 

## Level 32-33: 

We are greeted by something called the uppercase shell. We cant execute any commands here as all our commands are lowercase and the shell converts all our input into uppercase. I have little idea how this level works but apparently the $0 is a special variable in linux that is a reference to the shell. So we type that in the terminal and we access the password for the next level as we do usually by entering cat /etc/bandit_pass/bandit33. 



# Challenge 2 : Programming 

## Write a Program for Russian Roulette

Implementation of Russiona roulette in C

```
//Russian Roulette in C
#include <stdio.h>
#include <stdlib.h>

main(){
    srand(time(0));                                        //set seed for rand function
    int pistol_rounds[6] = {0,0,0,0,0,0};		
    int choice;
    int loaded = rand()%6;			
    pistol_rounds[loaded] = 1;		//bullet currently in some chamber(0-5)


    printf("Welcome to Russian Roulette!!");
    while(1){
        printf("Enter 1 to spin the revolver and 2 to shoot");
        scanf("%d",&choice);
        if(choice == 1){         /reset the chambers to 0 , then pick one random chamber and load it
            loaded = rand()%6;
            for(int i = 0;i<6;i++)
                pistol_rounds[i] = 0;
            pistol_rounds[loaded] = 1;
        }
        else if(choice == 2){        //current revolver chamber is 0
            if(pistol_rounds[0] == 1){
                printf("The revolver was loaded!You have died. \nGame over");
                break;
                }
            else{
                printf("You managed to survive! Congrats!.\nGame over");
                break;
            }
        }
        else
            printf("Please enter valid choice");
    }
}
```

## Implementing any Sorting Subroutine

```
//Sorting Routine (Insertion Sort)
#include <stdio.h>
#include <stdlib.h>

main(){
    srand(time(0));
    int array[100];             //initialize some array with random entries
    for(int i = 0 ;i<100;i++)
        array[i] = rand();

    printf("Current array is \n");  //print current array
    for(int i = 0;i<100;i++){
        printf("%d , ",array[i]);
    }

    for(int i = 0;i<100;i++){    //sort the array by inserting the smallest element at appropriate position
        for(int j = i;j<100;j++){
            if(array[i]>array[j]){
                int tmp = array[j];
                array[j] = array[i];
                array[i] = tmp;
            }
        }
    }

    printf("\n\n\n\n\n\n\nNow array is \n");   //print the array
    for(int i = 0;i<100;i++){
        printf("%d , ",array[i]);
    }
}
```






