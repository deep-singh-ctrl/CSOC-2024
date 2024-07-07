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

## Chall_5

For this challenge , again we need to crack the password. The chall_5 comes in a folder with the same name with these files packed in it :

![Screenshot 2024-07-07 075414](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/de406b26-0a83-4132-83c6-bf9b8a9d5c2d)

Note that the chall_5 file is a PE32 type which means that it won't run on Linux , hence we will be using the Windows OS to analyze the file. If the chall_5 file doesn't run..Change the extension to .exe and try again. Also don't worry about the `libgcc_s_seh-1.dll` , `libstdc++-6.dll` and `libwinpthread-1.dll`. These are just c++ modules. Just like before the .exe opens a command prompt terminal and asks for the password. On entering the wrong password , the terminal window closes abruptly. 

To analyze the file further , load the contents of the folder in Ghidra. You should see something like this after the import is successful (just drag and drop for the import).

![Screenshot 2024-07-07 075955](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/9fe17423-edf4-4365-95ee-88fd19001e3e)

Let's first analyze the .exe.0 file. We see that it has only one function (entry). It is not that important to look into it further as the entry function mostly just sets up the terminal and other stuff for the execution (There's like only Push and Add statements). So Let's ignore that and head to .exe file. We firstly look into the main() of the program. Please pardon the monotone. I have tried to add comments wherever i could but the lack of color can make the code difficult to understand. If posible , you can copy this decompiled code into Visual Studio for a much better view :

```
// Main_chall_5_in_Ghidra

int __cdecl main(int _Argc,char **_Argv,char **_Env)

{
  undefined8 uVar1;                            //these vars are probably global or static vars since they initialse before main
  basic_ostream *pbVar2;
  undefined8 local_a8;
  undefined8 local_a0;
  basic_string local_98 [8];        
  basic_string user_input [8];   //since this stores length we can be sure the password length is <= 8
  basic_string<> local_58 [40];
  byte *local_30;
  basic_string *local_28;
  undefined local_19;                      //var declaration ends here
  
  __main();           //this main is compiler added , just makes sure that all vars are nicely initialised.
  obfuscatePassword[abi:cxx11](local_58);     //call obfuscatePassword on local_58 , local_58 is now "0xJam3z" xor with 109
  std::__cxx11::basic_string<>::basic_string();      //initialise user_input
  std::operator<<((basic_ostream *)&_ZSt4cout,"Enter the password: ");     //prompt
  std::operator>>((basic_istream *)__fu0__ZSt3cin,user_input);            // store user input in user_input 
  std::__cxx11::basic_string<>::basic_string(local_98);                 //new string but the initial value is not clear from the assembly or decompiler
  local_19 = 0x6d;                              // local_19 = 109
  local_28 = local_98;          // local_28 is string pointer pointing to local_98 string
  local_a0 = local_98.begin();  // a0 stores the beginnning of the local_98 string
  local_a8 = local_98.end(); //a8 stores the end of the local_98 string
  while( true ) {
    uVar1 = __gnu_cxx::operator!=(&local_a0,&local_a8);           // uvar1 is true if a0 and a8 are not equal
    if ((char)uVar1 == '\0') break;                          // uvar1 is true
    local_30 = (byte *)__gnu_cxx::__normal_iterator<>::operator*((__normal_iterator<> *)&local_a0 ); //typecast to byte the character at a0
    *local_30 = *local_30 ^ 0x6d;         // xor the character with 0x6d(109 in base10) and update its value in local_98
    __gnu_cxx::__normal_iterator<>::operator++((__normal_iterator<> *)&local_a0); // a0++
  }
  uVar1 = std::operator==();  //if local_98 is equal to local_58 then uvar1 is true
  if ((char)uVar1 == '\0') {
    pbVar2 = std::operator<<((basic_ostream *)&_ZSt4cout,"Incorrect");
    std::basic_ostream<>::operator<<((basic_ostream<> *)pbVar2,std::endl<>);
  }
  else {
    pbVar2 = std::operator<<((basic_ostream *)&_ZSt4cout,"Correct");   //if uvar1 was true
    std::basic_ostream<>::operator<<((basic_ostream<> *)pbVar2,std::endl<>);
    system("calc");
  }

  return 0;
}
```


The big question here is what value does local_98 initialise with before the execution starts? Its not immediately clear , I couldn't get it from the assembly either. However it becomes clearer that local_98 is just a copy of the user input (From IDA , here is the main once again)

```
int __fastcall main(int argc, const char **argv, const char **envp)
{
  __int64 v3; // rax
  __int64 v4; // rax
  __int64 v6; // [rsp+20h] [rbp-90h] BYREF
  __int64 v7; // [rsp+28h] [rbp-88h] BYREF
  char v8[32]; // [rsp+30h] [rbp-80h] BYREF
  char v9[32]; // [rsp+50h] [rbp-60h] BYREF
  char v10[40]; // [rsp+70h] [rbp-40h] BYREF
  _BYTE *v11; // [rsp+98h] [rbp-18h]
  char *v12; // [rsp+A0h] [rbp-10h]
  char v13; // [rsp+AFh] [rbp-1h]

  _main();
  obfuscatePassword[abi:cxx11]((__int64)v10);
  std::string::basic_string(v9);
  std::operator<<<std::char_traits<char>>(refptr__ZSt4cout, "Enter the password: ");
  std::operator>><char>(refptr__ZSt3cin, v9);
  std::string::basic_string(v8, v9);
  v13 = 109;
  v12 = v8;
  v7 = std::string::begin(v8);
  v6 = std::string::end(v12);
  while ( __gnu_cxx::operator!=<char *,std::string>((__int64)&v7, (__int64)&v6) )
  {
    v11 = (_BYTE *)__gnu_cxx::__normal_iterator<char *,std::string>::operator*((__int64)&v7);
    *v11 ^= 109;
    __gnu_cxx::__normal_iterator<char *,std::string>::operator++(&v7);
  }
  if ( std::operator==<char>((__int64)v8, (__int64)v10) )
  {
    v3 = std::operator<<<std::char_traits<char>>(refptr__ZSt4cout, "Correct");
    std::ostream::operator<<(v3, refptr__ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
    system("calc");
  }
  else
  {
    v4 = std::operator<<<std::char_traits<char>>(refptr__ZSt4cout, "Incorrect");
    std::ostream::operator<<(v4, refptr__ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_);
  }
  std::string::~string(v8);
  std::string::~string(v9);
  std::string::~string(v10);
  return 0;
}
```

Its  lot clearer than using ghidra. I might use it more from now. Next the logic , here's a algorithm for what the main function does :


1. Call Function obfusactePassword on local_58
2. Take user input
3. Copies user input into local_98
4. Iterates over the length of local_98 using two iterators begin and end
5. On each iteration , take the character from local_98 at begin , typecaste it into byte , xor it with 109 and update
6. Increment the begin iterator
7. Compare the strings local_58 and local_98
8. Success if both are equal else fail

Now lets take a look at the obfuscate function in Ghidra. It takes one parameter of string pointer type and returns a string pointer. Since the argument is a pointer
The maniupalations reflect in the argument.

```
basic_string<> * obfuscatePassword[abi:cxx11](basic_string<> *param_1)  //takes string argument

{
  undefined8 uVar1;
  undefined8 local_50;
  undefined8 local_48;
  allocator local_39;
  allocator *local_38;
  byte *local_30;
  basic_string<> *local_28;
  undefined local_19;
  
  local_38 = &local_39;  //local_38 is pointer to local_39
  std::__cxx11::basic_string<>::basic_string<>(param_1,"0xJam3z",&local_39);  //initialise local_39 to "0xJam3z"
  std::__new_allocator<char>::~__new_allocator();
  local_19 = 0x6d;               //local_19 is 109
  local_28 = param_1;                
  local_48 = std::__cxx11::basic_string<>::begin();
  local_50 = std::__cxx11::basic_string<>::end();
  while( true ) {
    uVar1 = __gnu_cxx::operator!=(&local_48,&local_50);   //iterate over the length of parameter
    if ((char)uVar1 == '\0') break;
    local_30 = (byte *)__gnu_cxx::__normal_iterator<>::operator*((__normal_iterator<> *)&local_48 );
    *local_30 = *local_30 ^ 0x6d;         //xor with 109 and update value
    __gnu_cxx::__normal_iterator<>::operator++((__normal_iterator<> *)&local_48);
  }
  return param_1;
}
```

A breakdown :

1. Intialise param_1 to "0xJam3z" using local_39 as allocator
2. Set local_19 to 109
3. Again iterate over the length of param_1 and xor with local_19(109)
4. Update the values and increment the loop variable
5. Return the update param_1

After the full execution of obfuscatePassword and the main just before the final if condition , the local_58 var stores "0xJam3z" xor with 109. to make local_98 equal to local_58 up till this step , we simply pass it as the password (since the key used for xoring is the same in both cases aka 109). Now let us check this theory :


![Screenshot 2024-07-07 093558](https://github.com/deep-singh-ctrl/CSOC-2024/assets/172205598/c7cf20d4-23bf-4e04-bd10-5d169c4cb674)

This along with an open calculator. 













