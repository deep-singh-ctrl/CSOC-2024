# Writeup for week 1 of CSOC
## Forensics at picoCTF
### Information

The first step is to download the cat.jpg image. The hint says to check the content of the file. I checked whether or not the extension is valid by running the `file cat.jpg ` and `hexdump -C cat.jpg | head -30` commands (These were used previously for bandit). The result says JPEG image data using JFIF standard , Seems okay (according to gpt). Next step is to check the file content. So we enter `head -50 cat.jpg`  (dont want all the lengthy binary info to clog my screen). We run the result in gpt which points that the info is the metadata for the image. Looking up the metadata using `exiftool cat.jpg command`, we see that the only suspicious looking thing is the license if the image. It could be base64 encoded as it has numbers from 0-9 and the alphabets. We run the base64 command with -d option as echo `"License” | base64 -d.` This gives us the flag for the level.

### Matryoshka Dolls

Okay so I did knew you could hide messages in images thanks to lsb but you can hide whole files in them? That was new (from hint 1 ). Downloaded the image and then checked the file extension with the file command. It says png data but according to gpt its okay. I looked up some articles online on how to hide files in images and then get the files back. An article by makeuseof stood out. We can extract the image for the zipped files by simply using the unzip command. We type `unzip dolls.jpg`. This gives us a directory called base_images. We enter the directory and are greeted by another image. Presumably this also has some file hidden in it. So we run the unzip command again and we got another image!!. We repeat the process about 4 times before we finally get a file called flag.txt with the required password (or flag whatever)

### Tunnel Vision

Firstly downloaded the file and then proceeded to use `hexdump -C` and `file` command on it. The file command simply says data , while the header for the hexdump says BM.& , never heard of this extension before(and we can’t seem to get a hold of it even after google searching. It says BM is bitmap but surely it would run on a image viewer if that were the case). So i google around a bit and found a tool called image magick, ran the file through it but it reported an error(a header error, interesting).

 So i checked the hexdump for the file again and compared it to the standard BMP file format from a website. I found that at offset of 10 , the hex was `ba d0 00 00`, instead of the expected `36 00 00 00`(16 byte little endian). So i changed this value to match. Next , again this `ba d0 00 00` hex came up at offset of 14 when the hex should have been 40 (in decimal) or `28 00 00 00`. So i changed this again (so that means that data offset and size of info header were changed.) next we try to open this file , it opens but we don't get the flag in the image. It could be possible that the width and height of the image were altered too. So i changed the current width of about 336 pix (checked from exif tool ) to about 1000. Opening it in image magick gave a very skewed image but thankfully with the zoom in tool , the flag was retrievable. Onto the next level…

### Macrohard_weakedge

Firstly download the file on the system. Firstly tried to open the file as a normal ppt. After repairing the ppt opens , but its nothing unusual. So i get back to the linux CLI and check it with `exiftool` ,this yields some surprising results. Zip required version? Zip flag? Quickly unzip the file using the `unzip` command. We get a bunch of directories containing .xml and _rels subdirectories. I firstly thought that the flag could be in either file so i typed out `grep -r “pico” *` to check directories and subdirectories for pico string. No satisfactory output. Onto checking the files manually….one of the subdirectory was written slideMasters containing a file called hidden. This had a string which looked like base64 encoded. After removing the spaces using the `grep`  and `tr` command , we decode the text and we got the flag for this level. Onto the next….

### Enhance!!

 
Downloaded the file and opened it. Its a simple image with a circle at the middle of a black circle. Exiftool confirms this file is an svg file. Now we check the file contents with the cat command. Below the codes for ellipse and circle , we find a heading called text with information on text position , colour , font size and style. Scrolling down we see that there is something before the } bracket. Again there is text after the { bracket before it. This is obviously the flag , i tried to change the font colour , size and offset to get the flag to display but that did not work , So we can reconstruct the message with this code only. The flag is revealed when we piece together everything.

### Advanced-potion-making

 
Download the file. Firstly i used exiftool to analyse it , the report came back but the file extension and type could not be identified. So next i used binwalk to analyse this file for anything which could be embedded in it. Zlib compressed data…next step is to use binwalk with -e command to get whatever is on the file. It gives us an extracted directory with 2 files , 5B and 5B.zlib. Trying to open the 5B file hangs up my webshell so i simply extract the 5B.zlib usin binwalk. This gives us another directory with files 0 and 0.zlib. Extracted this 0.zlib gives the same 2 files again , This is a dead end.

 We back out then check the hex for the with hexdump -C command .PB in ASCII with the initial hex as 89 50 42 11. We run this search in wikipedia and the closest match we get is .png so this could be png file with a corrupted header. Hence we correct the header in an online hex editor to match a png. Run it in image magick and then it shows that the IHDR chunk is incorrect , we compare the IHDR chunk with the standard file formats and found that the size of chunk should be 13 bytes. So we make that change in the hex editor. Next we open the image in image magick and it opens but with a big red bg with no flag. I thought the flag might be written in red colour so it blends with the background. So we load this in the MS paint , use the bucket option with white and touch the background at random place and voila!!!! The flag is revealed!!!


### Hideme

  
Downloaded the file , checked with file command to ensure it is png (yes it is). Next step is to use exiftool to analyse the file further , exiftool shows that there is a trailing text after IEND data chunk. We run the` hexdump -C flag.png | tail` to make check this and we find some weird words like secret/flag ? could it be possible some files have been embedded into the image? Used the binwalk -e to extract anything embedded in the image. Found a directory called secret ; hmm..... click on that to find another flag.png. Now is this the same flag.png as we encountered before ? We used the `diff` command with two flags we have ( I renamed the flag inside secret as flag2) and we get that indeed the two images are different. Now since the webshell can't open images , i opened this image from an online tool and got the flag..


### File extensions

This was so easy? Literally run file command to discover this is a png, next go to windows explorer and change the file extension from txt to png and open it in one photo viewer (or any pic viewer of choice) and voila!!! got the flag.

### MSB

Another png , firstly checking with file command and `exiftool` we indeed confirm this to be a png file. Next we check up on the hexdump and check for the file header (signature) and the IHDR , IEND etc chunks , they all seem to be okay. So what might be causing the distortion in the upper part of the image when the lower one seems to be okay? I thought that perhaps there is some pattern in how the pixels have changed rgb values so i manually started analyzing the rgb values of the left border (white part) with the distorted values above it. I could see that the bits did appear to change but the cahnge was only in the 1st bit of the pxiels. The challenge is also called MSB ,  so i decided to apply MSB techniques on this. I found an online tool called StegOnline that can do MSB on png images to look for hidden strings and files. And yes indeed the image did contain lots of string revealed by the MSB. We simply download the file and do a ctrl+f to search for pico. We got the flag!!.

### File-Types (Could not complete)

Still trying to figure out this one. I kept on unzipping different file formats till i reached a dead end where it wont unzip anymore an the flag is nowehere in the files retreived. So yeah..stuck on this one for now.



# OSINT

## Try hack me room- Sakura


### Task 1
  

Login using google account , then click on join room and check for hint. Finally typed “Let’s Go!” to complete the task.

  

### Task 2

  

Initial guess is to go for the metadata but since that doesn't yield much useful information about the possible attacker name , we go and look inside the content of the file. Upon close inspection , we can see that the attacker has left their linux username in the file name…

  

### Task 3

  

Running the username through sherlock helps us unravel various possible sites (mostly all are false positives though , except for github) , On github we can see that the attacker has created a public pgp key. We know from bandit that public keys and private keys are a way to login to the computer without the need for a password. After looking up a abut about keys online , it turns out you can find the email associated with with key by using the gpg tool (i used it online on the key , and got the email address). The name part is troublesome….I used spokeo to perform a reverse email check but got a false match. Using google search for the username brings up some linkedin profiles name aiko abe, these do match the length requirements as per the hint in the answer. So we got lucky when it was accepted…yay!!.

  

### Task 4

  

For this task we need to dive deeper into the attacker’s github and also reveal information about their crypto wallets.. (I just recalled that the sherlock search we did earlier revealed some websites related to crypto…so that could be real helpful). Back to the github , we navigate to the repositories that the attacker has created. We see some commits for ETH. That is suspicious..We also see a hex number in the commit which was deleted and replaced by a new line. (There is also a repository related to bitcoin but with no commits by the person on it). Given the evidence we can conclude that the attacker was using ETH (Ethereum) crypto and the hex is the attacker’s address. Also the mining pool the attacker was using is also in the commits.(Can conclude all this when we compare the two texts)..

  
  

Now we can review the transaction history of the attacker with their ethereum address (new stuff learnt). If we look at the tokens the attacker was trading , we can see the other currency he was using , it was Tether.. Onto next

  
  

### Task 5

  

The image shows the previous twitter handle of the attacker, we can google search this. The top result is the twitter handle of the attacker

  

Next the URL;The question hints towards accessing dark web for the question (Some kind of pastebin as the tweets also suggest , “Look DEEP —- PASTE” But I did not install tor or anything like that to get this info as i am still new to this and i don’t feel like i can protect myself properly while on the dark web. The hint for this question gives us access to the info as well as the URL. Next we can enter her SSID into a website (wigle) and search for the BSSID. We also need a closer geographical approximation of the attacker’s home (At this point its easy to say its somewhere in central japan as the twitter pictures also hint towards). Upon looking at the wifi SSID’s again , we see that the city is Hirosaki. This pinpoints us to the attacker’s home..

  
  

### Task 6

  
First we need to get the airport the attacker has boarded the flight to Japan from.
The white building in the background really stands out. A google image search after upscaling the resolution a bit with AI , reveals that it is the Washington monument. Now we can search the airport nearest to it.

There’s another image and a caption about a layover. This must be the airport where the attacker got off , a Yandex image search reveals its the Tokyo Haneda airport. We can get the IATA code by searching in google. The lake name i got by manually searching central Japan in google maps, Its a large lake so hard to miss. And finally for the name of the city , we already got it before from the Wi-Fi SSID , there was a record for city Wi-Fi .

## OSINT challenges

### Task 6

  

Pretty straightforward. I just did a reverse image search and found one [link on wikipedia](https://en.wikipedia.org/wiki/Al-Qaeda_in_Iraq#/media/File:WaziriyaAutobombeIrak.jpg). On clicking there i could see that the photo was a reference to a terrorist car bombing in iraq and not pakistan as the tweet mentioned. (Although i was curious whether the image could be AI generated or created using photoshop or something.. and the metadata be tampered with…but i think, given the huge number of citations this image has on several news articles , it should be genuine and credible enough.)

  
  

### Task 4

  

We are given a picture of a resort and we need to figure out where this resort is located. Simply searching for the resort gives a lot of false positives and it is difficult to analyse each and every one of the results in google earth , so we search the image and filter for resorts. One article on a [website](https://oanresort.wixsite.com/chuuk/about) hosted by wix about tourism looks promising. On opening the website ,we found that the image is an exact match. It is the oan ( eoan ) resort in the federated states of micronesia.

I google searched oan resort and copied the address from google maps , then got the lat. and long. From [this site](https://www.latlong.net) , (Latitude : 7.440000 and Longitude : 151.882996)

  

Finally its time to get the camera direction when taking the picture; I did this by switching to google earth in 3D and manually fixing the direction to get the image to match , the direction of facing camera is approximately west.

  
  

###  Task 3

  
The somalian president visited turkey , this means that the photo must have been taken in turkey somewhere. Reverse image search in bing reveals that the turkish president has met many world leaders at this place. ( [This](https://www.theportal-center.com/wp-content/uploads/2021/02/Atalayar_Farmajo-y-Erdogan-portada.jpg) is the image in question with a different angle , the building is again repeated in images [here](https://menaaffairs.com/turkish-president-hosts-ethiopian-prime-minister-in-official-visit-to-ankara/) , [here](https://www.jordannews.jo/Section-91/Africa/Libyan-parliament-rejects-budget-plan-1747) and [here](https://1tv.ge/lang/en/news/georgian-pm-congratulates-erdogan-on-re-election/)). One of the articles mentios a meet in ankara which is turkey’s capital. This narrows our search down. We search for important govt. Buildings in ankara and found a close match called the new presidential palace. Further , [this article](https://turkishminute.com/2017/08/28/video-erdogan-putin-said-turkeys-presidential-palace-is-sign-of-a-great-state/) confirms that the building is indeed the new presidential palace built in ankara.

### Task 14

  
The best thing about working with CCTV footage is that we have the exact date and time already at the top left corner of the screen. So all we have to do is lookup earthquakes that took place in 2016 and then narrow down monthwise , day-wise and then time-wise. Since wikipedia does not has the exact time these earthquakes took place , we need to look them up separately on news sites. And….we got nothing. This time let us use more precise search engines instead of google. We used perplexity.ai for any earthquakes on september 24 , 2016 between 2 am and 2:30 am. It returned one earthquake that was registered and it took place in romania and moldova with moldova being hitten harder. Finally we do an image search on screenshot of the image on bing and find that yes indeed this is a footage from chisinau , the capital of moldova.

  

The magnitude of the earthquake was 5.8 on the richter (though there might be some error as different sites have put forth different numbers for the magnitude).

  

Okay now for the geo-location. My first thought was that moldova is a pretty small country so i can search all of chisinau for something similar (but that was not a good approach as chisinau is still as big as jodhpur). The next thing was to look for any names of the buildings and try to narrow things down , however even after upscaling and zooming , i could not read any names (not to mention that moldovans use cyrillic script so even harder to make out names)

  

So we are left with assumptions..The tall building at the back which is a bit curved stands out the most. The first assumption was that this is a hotel. From here we search for hotels in chisinau. Unfortunately its harder than i thought to verify each and every one of them , so we go back making another assumption…the one story building with some parking space and lots of lights on it. I thought this looked a bit like a petrol pump so we look into petrol pumps of chisniau but still dont get a close match.

  

At this point i was pretty frustrated with the problem so i looked up the video solution online. It turns out the building with bright lights was a car dealership ( also learnt how you can narrow down your geographic searches by making use of other details such as trees , curvature and width of road etc).

  

The footage was taken from a building called the V continental centre. After we have located the car dealership the rest is simply to make use of google earth and align the building to the footage view. We get the coordinates as roughly 47.01751635911457, 28.852802652150654.

### Task 26

The video was taken on february 24 and there is snow in the video. This means that the video is somewhere from the northern hemisphere. Let’s perform an online reverse video search on this video. In the meantime , one of the pictures is interesting..It contains advertisement for a mobile app called payme in some language. Type out this text in google translate reveals that the language is uzbek; So we have a closer geo-approximation of uzbekistan (but we cant be sure that the video is also shot in uzbekistan as the post-soviet countries do share some inter-country railway lines).

  
  

Ok now to do the reverse image searches for the images we have. The first one represents some sort of a fruit/vegetable market. We do a reverse image search for this using google and some of the images we see match the curve rooftop of the buidiing we have , this is supposedly taken from chorsu bazaar in uzbekistan however we cant be super sure about the exact location.Doing the reverse image search on yandex revealed a [travel blog](https://kitv.livejournal.com/139725.html) with a similar looking image , but wait [what’s that!](https://ic.pics.livejournal.com/kitv/16379271/328844/328844_original.jpg) That looks like the tall building in the background!!. That is apparently hotel chorsu. Searching for it in english in google maps didnt help (so i switched to uzbek and yes indeed typing chorsu mehmonxasi fetches us a building that looks exactly the same). Finally we look at the banner on chorsu hotel and compare it to the one in the image. This will allows us to figure out in which the direction the camerperson was facing. We keep moving in the direction to find the chorsu bazaar. Hence we can conclude the photo was taken [near](https://www.google.com/maps/place/Chorsu+mehmonxonasi/@41.3237177,69.2347169,156m/data=!3m1!1e3!4m7!3m6!1s0x38ae8b57079fd6b5:0x333df9d25a7c2a09!4b1!8m2!3d41.3225763!4d69.2345789!16s%2Fg%2F11vd_mqyd9?entry=ttu) chorsu bazaar blue dome (facing the hotel chorsu)


The next image shows some sort of  a rollercoaster , we can use yandex images (since yandex delivers kinda better results for this exercise).  The image was taken from Park Lokontiv Anhor (An amusment park in uzbekistan).

I tried to locate the other two images in the following manner.

First the image with the samsung billboard. We can see some interesting buildings , rent a car at the bottom left , navien in the top right and samsung galaxy at the top (however searching for all of these led to dissapointing results as its very hard to map out which place it is). The next image is also difficult to maekout due to the lack of characterstic details. I can only say that both pictures wer taken somewhere near # Shaykhontohur Street , as the person , seems to travelling on foot and both the pictures earlier were taken close to each other.

Examining the metadata we can conclude that the person took a huge number of pictures , this suggests he is likely a tourist. Judging by the speed of the train we can rule out high speed options. The departing station was obviosuly tashkent but the place of boarding remains unknown..)

