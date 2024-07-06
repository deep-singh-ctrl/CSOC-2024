# WEEK 4.1 Low Level

## CHALL_1

We are given an executable ELF file and we are asked to get the password for it. My initial guess was to go with john. However i could not really find the password hash from the hexdump of the program. 
The next guess was to use `gdb` aka the GNU debugger and get a list of all functions the program uses :

![Screenshot 2024-07-04 204841](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c9a816ed-6cd3-4965-86cc-103a77c3ab8f)

Of the many seemingly unimportant functions , there is one of interest - `check`. Now i am not very good at assembly language so i cant read the from the dissassembler and understand the working of the function. 
So we jump to our might tool ghidra. With ghidra we can get a kind of C source from the assembly code. Here is the code for the check function we obtained from the debugger :

```

int check(char *param_1)
{
  size_t sVar1;
  int result;
  
  sVar1 = strlen(param_1);
  if ((((int)sVar1 == 10) && (*param_1 == '1')) && (param_1[4] == '9')) {   //1...9.....
    result = 1;
  }
  else {
    result = 0;
  }
  return result;
}
```

I have just a few changes for var names and data types(possible ones).Also , please ignore the comment on the if line. Now if you look closely at the if statment :

```
if ((((int)sVar1 == 10) && (*param_1 == '1')) && (param_1[4] == '9')) 
```
This runs 3 simple conditionals

1. The length of the string input must be 10
2. The character at 0th index of the string must be '1'
3. The character at 4th index of the string must be '9'

Now you can use any combination of password of the type `1---9-----` , Just replace the dashes by any random character. Here is the final output :

![Screenshot 2024-07-04 205535](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/7e2502d8-d5b3-4800-9a6b-7ea2e8450c77)


## Chall_2

This one requires us to reverse engineer the c++ code given. This time I am going to load the file straight up into ghidra without going for the debugger. Once you do that :

1. Expand the function folder on the left
2. Look for functions main and checkPassword.

Once we open the main function in the decompiler , we get this piece of code (please note that the memory addresses may be diiferent for your computer)

``` #73-79
undefined8 main(void)

{
  undefined4 uVar1;
  basic_ostream *pbVar2;
  char *pcVar3;
  basic_string local_58 [8];
  basic_string local_38 [10];
  
  std::__cxx11::basic_string<>::basic_string();
                    /* try { // try from 0010144b to 0010148a has its CatchHandler @ 00101501 */
  pbVar2 = std::operator<<((basic_ostream *)std::cout,"Enter the password:");
  std::basic_ostream<>::operator<<((basic_ostream<> *)pbVar2,std::endl<>);
  std::getline<>((basic_istream *)std::cin,local_58);
  std::__cxx11::basic_string<>::basic_string(local_38);
                    /* try { // try from 00101492 to 001014ce has its CatchHandler @ 001014f0 */




  uVar1 = checkPassword();             //interesting stuff
  if ((char)uVar1 == '\0') {
    pcVar3 = "Login failed";
  }
  else {
    pcVar3 = "Login successful";
  }




  pbVar2 = std::operator<<((basic_ostream *)std::cout,pcVar3);
  std::basic_ostream<>::operator<<((basic_ostream<> *)pbVar2,std::endl<>);
  std::__cxx11::basic_string<>::~basic_string((basic_string<> *)local_38);
  std::__cxx11::basic_string<>::~basic_string((basic_string<> *)local_58);
  return 0;
```

There is a call to checkPassword function and on the basis of its output is the program displaying login successful or not. So next step is to load that function in the dissassembler. I will post a simplified (sort of) Working for the function later , but let us look at the function at its entirety now.

```
undefined4 checkPassword(void)

{
  long lVar1;                             //var definition
  char *pcVar2;
  undefined8 uVar3;
  undefined8 uVar4;
  undefined4 unaff_R12D;
  bool bVar5;
  basic_string<> local_68 [32];
  basic_string<> local_48 [35];
  allocator<char> local_25;
  int local_24;
  int local_20;
  int local_1c;                        //var definiton ends
  
  std::allocator<char>::allocator();                                                                     //algo starts
                    /* try { // try from 001012a2 to 001012a6 has its CatchHandler @ 001013df */
  std::__cxx11::basic_string<>::basic_string((char *)local_48,(allocator *)&DAT_00102005);
  std::allocator<char>::~allocator(&local_25);
  local_20 = -7;
  local_24 = std::__cxx11::basic_string<>::length();
  lVar1 = std::__cxx11::basic_string<>::length();
  if (lVar1 == -local_20) {
    std::__cxx11::basic_string<>::basic_string();
    for (local_1c = 0; local_1c < local_24; local_1c = local_1c + 1) {
      if (local_1c == local_24 + -1) {
                    /* try { // try from 00101321 to 0010138d has its CatchHandler @ 001013f9 */
        std::__cxx11::basic_string<>::operator+=(local_68,"k");
        std::__cxx11::basic_string<>::operator+=(local_68,"car");
      }
      pcVar2 = (char *)std::__cxx11::basic_string<>::at((ulong)local_48);
      std::__cxx11::basic_string<>::operator+=(local_68,*pcVar2);
    }
    uVar3 = std::__cxx11::basic_string<>::end();
    uVar4 = std::__cxx11::basic_string<>::begin();
    std::reverse<>(uVar4,uVar3);                                                //algo ends


    uVar3 = std::operator==();                                                //check the strings
    bVar5 = (char)uVar3 != '\0';
    if (bVar5) {
      unaff_R12D = 1;
    }
    std::__cxx11::basic_string<>::~basic_string(local_68);
    if (bVar5) goto LAB_001013ce;
  }
  unaff_R12D = 0;
LAB_001013ce:
  std::__cxx11::basic_string<>::~basic_string(local_48);
  return unaff_R12D;                                                      // end of function.
```

One very big problem with this code is that the functions length() and allocate() its not exactly clear on which string are they called. We can solve this problem by keeping track of the assembly code. We need to constantly look out for the RDI register since that is the one in which the string is loaded before the function is called.
Firstly the function defines several variables. I will explain their use as the need arises:

Next step is the algorithm. Firstly you need to take a closer look at the allocate function. There is some information here which is not present in the function code. The allocate function allocates memory to the local_48 variable but also intialises it to the memory value which is referenced by `&DAT_00102005`. 

```
                             DAT_00102005                                    XREF[1]:     checkPassword:00101298 (*)   
        00102005 64              ??         64h    d
        00102006 65              ??         65h    e
        00102007 63              ??         63h    c
        00102008 00              ??         00h

```

So the initial value of local_48 is equal to "dec" followed by the null byte to represent end of string. Next the two length() functions are called. As i have explained before , to figure out on which strings these functions are called , you need to look sequentially at the Assembly code. Just for the sake of brevity , I am skipping that part out. Next comes the if conditions which checks if the length of the function parameter is equal to 7. Next you have a while loop which appends the characters from local_48 to local_68 except at the last call , when it appends "kcar" , before doing so. Next the local_68 string is reversed and compared against the local_70 variable and if they are found to be equal , then the function returns true aka 1.

Here is the somewhat simplified code as I said before. It has some comments :

```js
int local_24;
  int local_20;
  int i;
  
  string local_48 = "dec";                                 // with name local_48 and = "dec\0" 
                                   //for obfuscation maybe , the local_25 object was destroyed. So omiited

  local_24 = local_48.length(); //length of the local_48 string which is 3
  lVar4 = local_70.length();    //length of the local_70 string which is a copy of param;
  if (lVar4 == 7) {                                                                      //lvar4 == 7(length of local_70 aka the argument)

    for (i = 0; i < 3; i = i + 1) {                                      //for loop on length of some local_48
      if (i == 2) {                                               //if reached last index
                    
        local_68 += "k";                                  //append "kcar"  before appending the charcter at i
        local_68 += "car";
  
      }
      pcVar5 = local_48.charAt(i);                  //isnt correct syntax , but you get the point , it will add first "d" then "e" ....
    }

 local_68 += pcVar5;                     //out of the loop , local_68 is now dekcarc;
      
    }
    _Var1 = local_48.end(); 
    _Var2 = local_48.begin(); 
    std::
    reverse(_Var2,_Var1);                //local_68 is now "cracked"
    
    if(local_68 == param_1) // compare local_70 to local_68
    bvar6 = true;    //if true
    if (bVar6) {
      unaff_R12D = 1;     // function return value is 1 , otherwise its zero.
```

You dont need to know the full code , so i have cut down on the variable declarations and the rest of the if logic. Also don't get confused by the sudden introduction of the local_70 variable , it is only a copy of the paramter we passed as value to the function. 

Now the local_68 contains the string "dekcarc" which when reversed is "cracked". `This means that the password should be cracked`. Let's see :

![Screenshot 2024-07-06 124138](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/5d5ff722-a19d-4ced-93c8-f47bcc4ad4aa)








