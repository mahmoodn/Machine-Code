Firts code:

Simply want to execute ADD instruction in C code.
```
$ cat machine.c
void main()
{
   __asm__("add %ecx, %eax");
}
```

By default, AT&T format is used for assembly representation. Therefore, that line means `eax <- eax+ecx`. In order to use Intel representation, `-masm=intel` should be used in gcc command as below:

```
$ gcc -masm=intel -g machine.c -o machine
```

OPCODE of the ADD instruction is `01 c1` which means `00000001 11000001` where
```
OP=000000   -->   add
D=0         -->   REG field is the source operand
W=1         -->   16,32,64-bit registers.
MOD=11      -->   register to register instruction
REG=000     -->   eax code
R/M=001     -->   ecx code
```


To test, that launch GDB and follow these steps.

1- Tell it to use intel representation with `set disassembly-flavor intel`. 

2- Use `disass /r main` to see the assembly code of the source file. 

3- Put a breakpoint at the beginning of the main function with `break main` or `b main`. 

4- Run the program inside GDB with `run` or `r`. 

5- Print registers with `info registers` or `i r`. 

6- Continue to the next line with `next` or `n` 

7- Verify the content of ecx.

```
(gdb) set disassembly-flavor intel
(gdb) disass /r main
Dump of assembler code for function main:
   0x00000000004004ed <+0>:     55      push   rbp
   0x00000000004004ee <+1>:     48 89 e5        mov    rbp,rsp
   0x00000000004004f1 <+4>:     01 c1   add    ecx,eax
   0x00000000004004f3 <+6>:     5d      pop    rbp
   0x00000000004004f4 <+7>:     c3      ret
End of assembler dump.
(gdb) break main
Breakpoint 1 at 0x4004f1: file machine.c, line 3.
(gdb) run
Starting program: /home/mahmood/Documents/./machine

Breakpoint 1, main () at machine.c:3
3          __asm__("add %ecx, %eax");
Missing separate debuginfos, use: debuginfo-install glibc-2.17-196.el7.x86_64
(gdb) info registers eax ecx
eax            0x4004ed 4195565
ecx            0x400500 4195584
(gdb) next
4       }
(gdb) info registers eax ecx
eax            0x4004ed 4195565
ecx            0x8009ed 8391149
(gdb) q
A debugging session is active.

        Inferior 1 [process 35165] will be killed.

Quit anyway? (y or n) y
```
