# TLE-Computer-Manual
An instruction manual for my computer built in The Life Engine - an online evolution and ecosystem simulator. It is an 8-bit system with
outstanding **80 Bytes** of program memory, **8 Byte** hard drive and a binary display that can show you any number from 0 to 255 !

![The computer](/Images/Computer.png)

## The *F+-* Language
The code is written in *F+-*, a language created specially for this machine. Its name refers to adding and removing food from the memory, 
which is how you write commands. Also, addition and subtraction are the only supported arithmetic operations. Despite this limitation, 
multiplication and division algorithms can be written by the user.

> Commands are 20-bit numbers saved in the program memory. Placing food inside a memory bit marks it as **1** (green), no food means **0** (red).
> There are 32 rows in the memory, each corresponding to a single line of code. They are executed from the bottom up and once the end is reached,
> it returns to row 0. A command cannot be edited while it is being executed because it can make a bit stuck as a **0**. 
> 
> ![Zeroes and ones](/Images/Program_command.png)

Variables are stored in main memory. There are 8 slots available, therefore at most 8 different numbers (variables) can be stored at the same time.
In most programming languages variables can be assigned memorable names but in *F+-* they are simply numbered. Memory slots are referenced through 
their 3-bit indices, with index 000 on top and 111 at the bottom of the memory. It is important to note that only the contents of 111 are displayed,
so writing a number to 111 is the way to display it.

### Available commands
- [Addition](#addition)
- [Subtraction](#subtraction)
- [Go to](#goto)
- [Copy](#copy)
- [Write](#write)

---

#### Addition
The most basic operation. It comes in 3 variants, 2 of which are interchangeable.

1. Reads numbers A and B from the memory, performs addition and writes the result to a specified memory slot.

| Command | Is A const | Index A | unused | Index B | Is B const | Out |
| ---- | --- | --- | ----- | --- | --- | --- |
| 0100 |  0  | AAA | 00000 | BBB |  0  | OOO |

2. Reads number A from the memory and B from the command, adds them together and writes the result to a spacified memory slot.

| Command | Is A const | Index A | Number B | Is B const | Out |
| ---- | --- | --- | -------- | --- | --- |
| 0100 |  0  | AAA | BBBBBBBB |  1  | OOO |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | Out |
| ---- | --- | -------- | --- | --- | --- |
| 0100 |  1  | AAAAAAAA | BBB |  0  | OOO |

Keep in mind, that if the result exceeds 255 it will overflow and "wrap around", returning A + B - 256 instead.

---

#### Subtraction
I don't think it needs explanation. Just like addition, has 3 variants.

1. Reads numbers A and B from the memory, subtracts B from A and writes the result to a specified memory slot.

| Command | Is A const | Index A | unused | Index B | Is B const | Out |
| ---- | --- | --- | ----- | --- | --- | --- |
| 1100 |  0  | AAA | 00000 | BBB |  0  | OOO |

2. Reads number A from the memory and B from the command, subtracts B from A and writes the result to a spacified memory slot.

| Command | Is A const | Index A | Number B | Is B const | Out |
| ---- | --- | --- | -------- | --- | --- |
| 1100 |  0  | AAA | BBBBBBBB |  1  | OOO |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | Out |
| ---- | --- | -------- | --- | --- | --- |
| 1100 |  1  | AAAAAAAA | BBB |  0  | OOO |

Negative results are not possible and they will overflow, returning 256 - A + B instead.

---

#### Goto
A really handy command that gives more control over the program. After this command is executed, the program counter will be set to a specified
5-bit number. It can be used to create all kinds of loops (infinite, for, while) and conditional statements (if, else).
One more thing: the command index is written in reverse (00001 is 16, 10000 is 1). It is a unique feature designed to strenghten the minds of programmers.

| Command | unused | Go to (reversed)| unused |
| ---- | ---- | ----- | ------- |
| 0001 | 0000 | GGGGG | 0000000 |

> Executing *00010000000000000000* will restart the program by setting the counter to 0.
> 
> Creating a *goto* command pointing to itself will stop the program (cannot be undone).

---

#### If
The key to more complex programs, is usually used together with *goto*. It looks for inequality between two numbers and **skips the next command** if A > B.
Has 3 variants analogous to subtraction.

1. Both A and B are read from the main memory.

| Command | Is A const | Index A | unused | Index B | Is B const | unused |
| ---- | --- | --- | ----- | --- | --- | --- |
| 0010 |  0  | AAA | 00000 | BBB |  0  | 000 |

2. A is read from the memory while B is defined in the command.

| Command | Is A const | Index A | Number B | Is B const | unused |
| ---- | --- | --- | -------- | --- | --- |
| 0010 |  0  | AAA | BBBBBBBB |  1  | 000 |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | unused |
| ---- | --- | -------- | --- | --- | --- |
| 0010 |  1  | AAAAAAAA | BBB |  0  | 000 |

> This example shows how to build an *if else* function
> ```
> 1    if A > B
> 2    goto 7
> 3    ...
> 4    code to execute if A > B
> 5    ...
> 6    goto 10
> 7    ...
> 8    code to execute if A <= B
> 9    ...
> 10   rest of the code
> ```

---

#### Copy
There is no special function to copy a number.

---

#### Write
write

---
