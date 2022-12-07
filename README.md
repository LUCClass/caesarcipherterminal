# Caesar Cipher, Command Line Edition


In this homework assignment, you will modify your Caesar cipher program to take its inputs from the command line. Your program will take two inputs: (1) the encryption key (ie shift amount) and (2) the string to encrypt. Running the program on the command line will look something like the following:


    $ ./caesar 5 "the quick brown fox jumped over the lazy dog"
    ymj vznhp gwtbs ktc ozruji tajw ymj qfed itl

The program in the example above shifts each letter forward by 5 characters.


## Taking Input from the Command Line

The OS passes two parameters to your program: (1) the argument count `argc` in R0 and (2) a pointer to the argument array `argv` in R1. The argument count is straightforward: this is just the total number of arguments passed to your program on the command line. In the example above, the argument count would be 3.

The argument array (called `argv` in C) is a pointer to an array. Each element of the argument array is a pointer to the beginning of one of the argument strings. In the example above, `argv[0]` holds the address of the string "./caesar". `argv[1]` holds the address of the string "5", and so on.

### Simple Access of Command Line Parameters

Let's say we want to just print out the 3rd parameter that was passed to our program. In the example above, that would be the sentence "the quick brown fox jumped over the lazy dog." The simple C program to do this is written below:

    int main(int argc, char **argv) {
        puts(argv[2]);
        return 0;
    }



In assembly, we need to get a pointer to that string and pass that pointer to `puts`. That pointer is element `[2]` of the `argv` array. The base address of the `argv` array is passed to our program in `R1`. The table below shows where to get the various elements of the `argv` array.

|           |      |
|-----------|------|
| `argv[0]` | R1+0 |
| `argv[1]` | R1+4 |
| `argv[2]` | R1+8 |
| `argv[3]` | R1+12|
|     ...   | ...  |

A simple assembly language translation of the C program above is:

    main:
      push {lr}
      sub sp,#8
      str r0,[sp]     ; Save R0 and R1 on the stack so their values
      str r1,[sp,#4]  ; aren't nuked when we call puts
      
      ldr r0,[r1,#8]    ; R0 <- R1 + 8, the address of argv[2]
      bl puts         ; Call puts, passing argv[2] in R0
      
      add sp,#8
      pop {lr}
      bx lr

# Converting Strings from the Command Line to Integers

In the program you are writing, the user will pass a key to your program on the command line. The encryption key is an integer shift amount, but it is passed to your program as a string on the command line. Your program needs to convert that string to an integer by calling `atoi`.

    int main(int argc, char **argv) {
      int shamt = atoi(argv[1]);
      // ...
    }

In assembly, you can do something like the following:

    main:
      push {lr}
      sub sp,#8
      str r0,[sp]     ; Save R0 and R1 on the stack so their values
      str r1,[sp,#4]  ; aren't nuked when we call atoi
      
      ldr r0,[r1,#8]    ; R0 <- R1 + 8, the address of argv[1]
      bl atoi         ; Call atoi, passing argv[1] in R0
                      ; atoi returns the integer value of the shift amount in R0
      
      add sp,#8
      pop {lr}
      bx lr




