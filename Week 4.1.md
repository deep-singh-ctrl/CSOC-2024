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
