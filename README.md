java cProject 3: Introduction to X86-64 Assembly Programming
Grading Form
Goal
In this project you will write programs in x86-64 assembly language. It is important that you learn the x86-64 assembly language since it is the one you use every day in your PC, Mac, or in data.cs.purdue.edu. Also, this is a 64 bit architecture that uses 8 byte addresses and variable-length assembly instructions.
X86-64 Introduction
The X86-64 assembly language was created by AMD and then adopted by Intel. The X86-64 assembly language extends the x86 32 bit architecture to 64 bits. X86-64 is a superset of x86-32. It provides an incremental evolution to migrate from x86-32 bits to x86-64 bits and it is backward compatible.
Here is a good reference that can help you in programming with the X86-64. Section 3.2 on page 5 gives an example of a C program translated to X86-64, and Figure 2 on page 7 gives an explanation of each register (which is also shown below).
x86-64 tutorial
Also see the x86-64 assembly notes section in http://www.cs.purdue.edu/homes/cs250
   
 The X86-64 architecture uses the following register assignment:

 You will find many similarities with the ARM architecture. For instance, there are also 16 registers available to the user, though not all are typically used as general purpose registers. As opposed to using r0, r1, and so on, some registers are named due to backwards-compatibility: instead of receiving/passing arguments via r0, r1, r2, and r3, arguments are received and passed via %rdi, %rsi, %rdx, and %rcx. Some registers are callee saved and can be used as local variables. In addition, %rax is used to return values in functions, similar to how you would load r0 in ARM assembly before returning.
One of the main differences is that in the x86-64 architecture is that the order of the arguments is different than in the ARM. For example, in the instruction:
movq $3, %rsi
The first argument, numerical constant $3, is assigned into the register %rsi so the target register is on the right. In ARM, this would be equivalent to:
mov r2 #3
The x86-64 architecture is backward compatible with x86-32, and as such the 4 least significant bytes of registers %rax, %rbx, %rcx, %dx are compatible with the old x86-32 bit registers %eax, %ebx, %ecx, and %edx. We will only write programs using the 64-bit registers, so most of the instructions will end with "q" which means that they will work with 8 byte words.
The addressing modes in the x86-64 are the following:  Immediate Value
movq $0x501208,%rdi
Direct Register Reference
movq %rax,%rdi
Indirect through a register
movq %rsi,(%rdi )
Direct Memory Reference
movq 0x501208,%rdi
#Put in register %rdi the constant 0x501208
#Move the contents of register %rax to %rdi
#Store the value in %rsi in the address contained in %rdi
#Fetch the contents in memory at address 0x501308 and store it in %rdi
Task 1: Your first X86-64 Assembly Program
Login to data.cs.purdue.edu and create a directory project-3-src where you will put all your code:
Type:
You will do all your work in data.cs.purdue.edu and in the directory ~/cs250/project-3-src.
Type the following program sqr.s that squares a number read from the terminal, then prints the result. Important note: it is easy to miss that your “main'' function will be declared with .globl in x86 as opposed to .global in ARM.
cd
mkdir -p cs250/project-3-src
cd cs250/project-3-src

    # Define global variable a in data section
        .data
        .comm   a,8
        .text
format1:
.string "a="
format2:
        .string "%ld"
format3:
        .string "a^2 is %ld\n"
.globl main
main:
        pushq   %rbp
        movq    %rsp, %rbp
# long a;
movq    $format1, %rdi #
movq    $0, %rax       #
call    printf         #
                       #
movq    $format2, %rdi #
movq    $a, %rsi       #
movq    $0, %rax       #
call    scanf          #
                       #
movq    $format3, %rdi #
movq    $a, %rsi       #
movq    (%rsi),%rsi    #
imulq   %rsi,%rsi      #
movq    $0, %rax
call    printf
leave ret
    printf("a=");
    similar to `bl printf` in ARM
    scanf("%ld",a);
    printf("a^2 is %ld",a*a);
# pops the frame pointer #}
# main()
#
# Save frame pointer
#
#
# #
  See what the program does line by line. You will find it very similar to ARM, such as how the argument registers are used (i.e. %rdi instead of r0, %rsi instead of r1 - library functions such as printf still need a format string and an address to put the value it scans in).
Then assemble代 写data、c++
代做程序编程语言 it using the following command.
      gcc -static -o sqr sqr.s

 ./sqr
The -static flag passed to gcc is used to make the .text section has a predefined loading address instead of being able to load it at random text addresses. The -static flags tells the compiler not to generate position independent code or PIC that is the default. One of the new security features of Linux is to load the program at random memory addresses every time you run it to make programs more difficult to hack. The flag -static will disable this feature in the assembly programs you write to make assembly programming easier.
Question 1. Write the code above into the file sqr.s, compile it and run it. Question 2. Explain what the following instructions do:
pushq %rbp # Save frame pointer movq %rsp, %rbp
......
leave
ret
Question 3.Write a program avg.s in assembly language that reads n numbers, then computes the sum and the average. The numbers are long integer of 8 bytes and the average will be truncated. Assume that the first number you read in will be the value of n, i.e. there will be n more inputs that follow.
./s
n? 5
3
6
8
1
5 SUM=23 AVG=4
Hint: Use the instruction "idivq" to compute the average. It might be helpful to look into commonly used X86-64 opcodes, as it may have some useful functionalities that ARM does not.
Task 2: Using Arrays in X86-64
The following C function computes the maximum of an array
   // Finds the max value in an array
long maxarray(long n, long *a) {
long i=0;
 
         long max = a[0];
     while (i0)
        //*(long*)((8*i+(char*)a)
  long *tmp = a[i];
  if (max 
long a[] = {4, 6, 3, 7, 9 };
 
    extern long maxarray(long, long*);
int main() {
      printf("maxarray(5,a)=%ld\n", maxarray(5,a));
}
 To compile this programs type:
   gcc -static -o maxarray maxarray.c maxarray.s
Question 4. Type the programs maxarray.s and maxarray.c, then test them. Also answer, what do the following instructions from the above code snippet do? Explain.
         movq    %rdx,%rcx
         imulq   $8,%rcx
         addq    %rsi,%rcx
Task 3: Implementing Bubble Sort in X86-64
Question 5. Implement a function bubblesort(long ascending, long n, long * a) in X86-64 in a file bubble.s that will sort an array of integers using bubblesort.
Here is the code in C that implements bubblesort. You have to implement it in X86-64 assembly language in the file bubble.s
   void bubble_sort(long ascending, long n, long * a) { for (int i = 0; i  array[j]) {
          swap = 1
} }
      if (swap) {
         long temp = array[j];
         array[j] = array[j+1];
         array[j+1] = temp;
}
 
    } }
 Then call it from the file bubble.c
   // bubble.c:
#include 
long a[] = {6, 7, 2, 3, 1, 9, 4, 5, 0, -9, 8};
long n = (sizeof(a)/sizeof(long));
extern void bubblesort(long ascending, long n, long * a);
void printArray(long n, long * a) {
   for (int i = 0; i gcc -static -o sqr sqr.s
maxarray: maxarray.s maxarray.c
gcc -static -o maxarray maxarray.c maxarray.s
clean:
rm -f sqr maxarray
 To use the Makefile, type:
  make clean
  make
Turnin
Follow these instructions to turnin project-3:
Make sure that your programs are built by typing "make". Make sure it builds and runs in data.cs.purdue.edu etc.
If you have not created a project-3-src create it. Type:
      cd
      cd cs250
      mkdir project-3-src
Copy your files into project-3-src/ and cd to the parent directory of
project-3-src. Then type:
      turnin -c cs250 -p project-3 project-3-src
Then, you may type "turnin -c cs250 -p project-3 -v" to make sure you have submitted the correct files - remember the -v flag, or it will ask you if you wish to resubmit your lab again.
You will show your programs to the lab instructor and TAs during lab time next week.
Rubric:
Q1. __/10 points
Q2. __/10 points
Q3. __/30 points
Q4. __/10 points

 Q5. __/30 points
Q6. __/10 points
Project 3 Grading Form
     Question
Max
Current
Question 1. Write the code above into the file sqr.s, compile it and run it.
10
Question 2. Explain what the following instructions do:
pushq %rbp # Save frame pointer
movq %rsp, %rbp ......
leave
ret
10
Question 3.Write a program avg.s
30
Question 4. Type the programs maxarray.s and maxarray.c
10
Question 5. Implement a function bubblesort(long ascending, long n, long * a)
30
Question 6. Complete the following Makefile
10
Total: Max 100
        
         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
