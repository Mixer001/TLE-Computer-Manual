# TLE Computer Manual
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
- [Mix'n'match](#mix'n'match)

---

#### Addition
The most basic operation. It comes in 3 variants, 2 of which are interchangeable.

1. Reads numbers A and B from the memory, performs addition and writes the result to a specified memory slot.

| Command | Is A const | Index A | unused | Index B | Is B const | Out |
| ---: | --- | --- | ----- | --- | --- | --- |
| 0100 |  0  | AAA | 00000 | BBB |  0  | OOO |

2. Reads number A from the memory and B from the command, adds them together and writes the result to a spacified memory slot.

| Command | Is A const | Index A | Number B | Is B const | Out |
| ---: | --- | --- | -------- | --- | --- |
| 0100 |  0  | AAA | BBBBBBBB |  1  | OOO |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | Out |
| ---: | --- | -------- | --- | --- | --- |
| 0100 |  1  | AAAAAAAA | BBB |  0  | OOO |

Keep in mind, that if the result exceeds 255 it will overflow and "wrap around", returning A + B - 256 instead.

---

#### Subtraction
I don't think it needs explanation. Just like addition, has 3 variants.

1. Reads numbers A and B from the memory, subtracts B from A and writes the result to a specified memory slot.

| Command | Is A const | Index A | unused | Index B | Is B const | Out |
| ---: | --- | --- | ----- | --- | --- | --- |
| 1100 |  0  | AAA | 00000 | BBB |  0  | OOO |

2. Reads number A from the memory and B from the command, subtracts B from A and writes the result to a spacified memory slot.

| Command | Is A const | Index A | Number B | Is B const | Out |
| ---: | --- | --- | -------- | --- | --- |
| 1100 |  0  | AAA | BBBBBBBB |  1  | OOO |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | Out |
| ---: | --- | -------- | --- | --- | --- |
| 1100 |  1  | AAAAAAAA | BBB |  0  | OOO |

Negative results are not possible and they will overflow, returning 256 - A + B instead.

---

#### Goto
A really handy command that gives more control over the program. After this command is executed, the program counter will be set to a specified
5-bit number. It can be used to create all kinds of loops (infinite, for, while) and conditional statements (if, else).
One more thing: the command index is written in reverse (00001 is 16, 10000 is 1). It is a unique feature designed to strenghten the minds of programmers.

| Command | unused | Go to (reversed)| unused |
| ---: | ---- | ----- | ------- |
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
| ---: | --- | --- | ----- | --- | --- | --- |
| 0010 |  0  | AAA | 00000 | BBB |  0  | 000 |

2. A is read from the memory while B is defined in the command.

| Command | Is A const | Index A | Number B | Is B const | unused |
| ---: | --- | --- | -------- | --- | --- |
| 0010 |  0  | AAA | BBBBBBBB |  1  | 000 |

3. Similar to *variant 2* but this time A is a constant and B comes from the memory.

| Command | Is A const | Number A | Index B | Is B const | unused |
| ---: | --- | -------- | --- | --- | --- |
| 0010 |  1  | AAAAAAAA | BBB |  0  | 000 |

> This example shows how to build an *if else* function
> ```
> 1    if A > B
> 2    goto 7
> 3     ...
> 4     code to execute if A > B
> 5     ...
> 6    goto 10
> 7     ...
> 8     code to execute if A <= B
> 9     ...
> 10   rest of the code
> ```

---

#### Copy
There is no special function to copy a number but it is possible with just one line of code and a bit of ingenuity. The simplest method is to use
addition with zero.

| Command | - | From | Zero | - | To |
| ---: | --- | --- | -------- | --- | --- |
| 0100 |  0  | FFF | 00000000 |  1  | TTT |

---

#### Write
Writing a predefined number into the memory is the trickiest task so far. The simplest method relies on addition and requires that at least one index
has the value 0.

| Command | - | Zero | Number | - | To |
| ---: | --- | --- | -------- | --- | --- |
| 0100 |  0  | ZZZ | NNNNNNNN |  1  | TTT |

Notice that the 3 *zero* bits are not necessarily *000*. It is a pointer to the value *00000000* anywhere in memory.
Even with no zeroes available, it is still possible to write any value in one command, but finding the right combination of bits can be a problem.
Luckily [Elias](https://replit.com/@pyelias), member of the TLE community, has created an algorithm that creates the command authomatically:

``` python
# python code by @pyelias
# see their replit for a fully commented version

def gen_const(n):
    b = n * 25 % 32
    curr_out = b * 9 % 256 # this = n mod 32
    
    diff = (n - curr_out) % 256 # this is a multiple of 32
    assert(diff % 32 == 0)      # ^ make sure that's true
    
    a = diff // 32
    return a, b

n = int(input("what constant to write? ")) % 256
a, b = gen_const(n)
print("use this instruction:")
print(f"0100 1 {a:03b} {b:05b} 000 1 out")
```

It is possible to reset any variable using the command below.

| Command | - | Zeroes | - | Var to reset |
| ---: | --- | ----------- | --- | --- |
| 0100 |  1  | 00000000000 |  1  | RRR |



---

#### Mix'n'match
*Use at your own risk*

*F+-* is flexible. There is no debugger telling you what to do, no error messages, and no risk of breaking something important. It is easy to notice,
that many commands have unused bits, wasting that precious memory. Technically it should be possible to overlap two commands for efficiency.

> Addition + Goto
> 
> | Command | - | Index A | Go to (reversed) | Index B | - | Out |
> | ---: | --- | --- | ----- | --- | --- | --- |
> | 0101 |  0  | AAA | GGGGG | BBB |  0  | OOO |
> 
> Numbers A nad B are added together and the result is written to memory. Then the program counter is set to a specified value.

---

## Possible future content
- [x] Document every command
- [ ] Add code examples
- [ ] Add in-depth explanation of the computer

---
*Made by Mixer001*
