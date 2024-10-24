---
title: Bomblab 手记
date: 2024-10-13 17:57:08
tags: 
  - ICS
  - 本科课程
---

## 前言

国庆的时候断断续续用 IDA 强拆了前六个炸弹便没再管了。本着为自己负责的原则，在这里把所有炸弹好好再拆一遍。

先用 `objdump` 把可执行文件反汇编到 `bomb.asm` 中。

打开 `bomb.c` 看看：

```c
/***************************************************************************
 * Dr. Evil's Insidious Bomb, Version 1.0
 * Copyright 2011, Dr. Evil Incorporated. All rights reserved.
 *
 * LICENSE:
 *
 * Dr. Evil Incorporated (the PERPETRATOR) hereby grants you (the
 * VICTIM) explicit permission to use this bomb (the BOMB).  This is a
 * time limited license, which expires on the death of the VICTIM.
 * The PERPETRATOR takes no responsibility for damage, frustration,
 * insanity, bug-eyes, carpal-tunnel syndrome, loss of sleep, or other
 * harm to the VICTIM.  Unless the PERPETRATOR wants to take credit,
 * that is.  The VICTIM may not distribute this bomb source code to
 * any enemies of the PERPETRATOR.  No VICTIM may debug,
 * reverse-engineer, run "strings" on, decompile, decrypt, or use any
 * other technique to gain knowledge of and defuse the BOMB.  BOMB
 * proof clothing may not be worn when handling this program.  The
 * PERPETRATOR will not apologize for the PERPETRATOR's poor sense of
 * humor.  This license is null and void where the BOMB is prohibited
 * by law.
 ***************************************************************************/

#include <stdio.h>
#include <stdlib.h>
#include "support.h"
#include "phases.h"

/* 
 * Note to self: Remember to erase this file so my victims will have no
 * idea what is going on, and so they will all blow up in a
 * spectaculary fiendish explosion. -- Dr. Evil 
 */

FILE *infile;

int main(int argc, char *argv[])
{
    char *input;

    /* Note to self: remember to port this bomb to Windows and put a 
     * fantastic GUI on it. */

    /* When run with no arguments, the bomb reads its input lines 
     * from standard input. */
    if (argc == 1) {  
		infile = stdin;
    } 

    /* When run with one argument <file>, the bomb reads from <file> 
     * until EOF, and then switches to standard input. Thus, as you 
     * defuse each phase, you can add its defusing string to <file> and
     * avoid having to retype it. */
    else if (argc == 2) {
		if (!(infile = fopen(argv[1], "r"))) {
			printf("%s: Error: Couldn't open %s\n", argv[0], argv[1]);
			exit(8);
		}
    }

    /* You can't call the bomb with more than 1 command line argument. */
    else {
		printf("Usage: %s [<input_file>]\n", argv[0]);
		exit(8);
    }

    /* Do all sorts of secret stuff that makes the bomb harder to defuse. */
    int *fp = initialize_bomb();

    if (*fp != SECRETTOKEN){
      printf("Don't try to make the bomb run on your local machine!(*/w＼*)");
      return 0;
    }

    printf("Welcome to Mr.Gin's little bomb. You have 6 phases with\n");
    printf("which to blow yourself up. Have a nice day! Mua ha ha ha!\n");

    /* Hmm...  Six phases must be more secure than one phase! */
    input = read_line();             /* Get input                   */
    phase_1(input);                  /* Run the phase               */
    phase_defused(fp);                 /* Drat!  They figured it out!
									  * Let me know how they did it. */
    printf("Phase 1 defused. How about the next one?\n");

    /* The second phase is harder.  No one will ever figure out
     * how to defuse this... */
    input = read_line();
    phase_2(input);
    phase_defused(fp);
    printf("That's number 2. Keep going!\n");

    /* I guess this is too easy so far.  Some more complex code will
     * confuse people. */
    input = read_line();
    phase_3(input);
    phase_defused(fp);
    printf("Halfway there! Good job!\n");

    /* Oh yeah?  Well, how good is your math?  Try on this saucy problem! */
    input = read_line();
    phase_4(input);
    phase_defused(fp);
    printf("So you got that one. Try this one.\n");
    
    /* Round and 'round in memory we go, where we stop, the bomb blows! */
    input = read_line();
    phase_5(input);
    phase_defused(fp);
    printf("Good work! On to the next...\n");

    /* This phase will never be used, since no one will get past the
     * earlier ones.  But just in case, make this one extra hard. */
    input = read_line();
    phase_6(input);
    phase_defused(fp);

    /* Wow, they got it!  But isn't something... missing?  Perhaps
     * something they overlooked?  Mua ha ha ha ha! */
    
    free(fp);
    fp = NULL;

    return 0;
}

```

便知道每个 phase 都需要我们找到一个正确的字符串。

## GDB 备忘录

- 断点的设置与删除：

  - 设置断点：`b` 指令。比如 `b function_name`。如果要跳到相对函数的某指令，就 `b *function_name+n` 即可；
  - 删除断点：`delete breakpoint_number` 可以删除单个断点。

- 打印寄存器的值：`p` 指令，后面跟寄存器。比如 `p $rdi`；

- 打印内存的值：`x /nfu`，x 意为 examine，n 为要显示的内存单元数目，f 为显示方式，u 为一个地址单元的长度。

  >x 按十六进制格式显示变量。
  >d 按十进制格式显示变量。
  >u 按十进制格式显示无符号整型。
  >o 按八进制格式显示变量。
  >t 按二进制格式显示变量。
  >a 按十六进制格式显示变量。
  >i 指令地址格式
  >c 按字符格式显示变量。
  >f 按浮点数格式显示变量。
  >s 按字符串显示变量。
  >
  >
  >
  >
  >b 表示单字节， h 表示双字节， w 表示四字节， g 表示八字节

## Phase 1

先在 `bomb.asm` 里面找到 phase 1：

```assembly
00000000000027b8 <phase_1>:
    27b8:	f3 0f 1e fa          	endbr64
    27bc:	48 83 ec 08          	sub    $0x8,%rsp
    27c0:	48 8d 35 39 2a 00 00 	lea    0x2a39(%rip),%rsi        # 5200 <_IO_stdin_used+0x200>
    27c7:	e8 65 05 00 00       	call   2d31 <strings_not_equal>
    27cc:	85 c0                	test   %eax,%eax
    27ce:	75 05                	jne    27d5 <phase_1+0x1d>
    27d0:	48 83 c4 08          	add    $0x8,%rsp
    27d4:	c3                   	ret
    27d5:	e8 6c 08 00 00       	call   3046 <explode_bomb>
    27da:	eb f4                	jmp    27d0 <phase_1+0x18>
```

发现似乎很简单。显然我们的输入是在 `%rdi` 的，`27c0` 又将 `%rip+0x2a39` 赋给了 `%rsi`，这俩自然是 `strings_not_equal` 的参数。根据其逻辑可以发现如果不相等的话就会 `call explode_bomb`。所以我们自然希望获取到那一段内存里面的值。

打开 gdb，`gdb bomb`，为了保险起见先给 `explode_bomb` 打断点，然后给 `phase_1` 打断点：

```bash
Reading symbols from bomb...
(gdb) b explode_bomb
Breakpoint 1 at 0x3046
(gdb) b phase_1
Breakpoint 2 at 0x27b8
```

然后输出 r 就可以运行程序了，先随便输入一个字符串。程序会在第一个断点停下来，也即 `phase_1` 处。

```bash
(gdb) r
Starting program: /home/ubuntu/bomb162/bomb 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Welcome to Mr.Gin's little bomb. You have 6 phases with
which to blow yourself up. Have a nice day! Mua ha ha ha!
abcdefg

Breakpoint 2, 0x00005555555567b8 in phase_1 ()
(gdb) 
```

输入 `disas` 可以让 gdb 显示对应部分的汇编代码。按 `ni` 可以跳到下一步指令。我们一直 `ni` 到 `lea` 完成为止。然后用 `x/s $rsi` 即可检查对应部分的内存并以字符串输出了。所以 phase_1 的答案就是那个字符串。

```assembly
Breakpoint 2, 0x00005555555567b8 in phase_1 ()
(gdb) disas
Dump of assembler code for function phase_1:
=> 0x00005555555567b8 <+0>:     endbr64
   0x00005555555567bc <+4>:     sub    $0x8,%rsp
   0x00005555555567c0 <+8>:     lea    0x2a39(%rip),%rsi        # 0x555555559200
   0x00005555555567c7 <+15>:    call   0x555555556d31 <strings_not_equal>
   0x00005555555567cc <+20>:    test   %eax,%eax
   0x00005555555567ce <+22>:    jne    0x5555555567d5 <phase_1+29>
   0x00005555555567d0 <+24>:    add    $0x8,%rsp
   0x00005555555567d4 <+28>:    ret
   0x00005555555567d5 <+29>:    call   0x555555557046 <explode_bomb>
   0x00005555555567da <+34>:    jmp    0x5555555567d0 <phase_1+24>
End of assembler dump.
(gdb) ni
0x00005555555567bc in phase_1 ()
(gdb) ni
0x00005555555567c0 in phase_1 ()
(gdb) ni
0x00005555555567c7 in phase_1 ()
(gdb) x/s $rsi
0x555555559200: "Love is a program that science and logic can never explain."
```

按 `r` 重新运行程序然后输入这个字符串即可。按 `c` 可以从断点处继续往下运行。

```assembly
(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /home/ubuntu/bomb162/bomb 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Welcome to Mr.Gin's little bomb. You have 6 phases with
which to blow yourself up. Have a nice day! Mua ha ha ha!
Love is a program that science and logic can never explain.

Breakpoint 2, 0x00005555555567b8 in phase_1 ()
(gdb) b phase_2
Breakpoint 3 at 0x5555555567dc
(gdb) c
Continuing.
Phase 1 defused. How about the next one?
```

## Phase 2

进入 phase 2，先看看汇编代码。真的还长了很多，下面分段给出注释。

```assembly
00000000000027dc <phase_2>:
    27dc:	f3 0f 1e fa          	endbr64
    27e0:	53                   	push   %rbx # 把 %rbx 存入栈中
    27e1:	48 83 ec 20          	sub    $0x20,%rsp # 在栈上开 20 个字节的空间
    27e5:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax # 金丝雀值
    27ec:	00 00 
    27ee:	48 89 44 24 18       	mov    %rax,0x18(%rsp) # 金丝雀值
    27f3:	31 c0                	xor    %eax,%eax # 清空 rax 寄存器
    27f5:	48 89 e6             	mov    %rsp,%rsi # 将当前栈指针作为传给 read_six_numbers 的第二个参数
    27f8:	e8 cf 08 00 00       	call   30cc <read_six_numbers>
```

我们再跳到 `read_six_numbers` 看看是个啥情况：

```assembly
00000000000030cc <read_six_numbers>:
    30cc:	f3 0f 1e fa          	endbr64
    30d0:	48 83 ec 08          	sub    $0x8,%rsp
    30d4:	48 89 f2             	mov    %rsi,%rdx
    30d7:	48 8d 4e 04          	lea    0x4(%rsi),%rcx
    30db:	48 8d 46 14          	lea    0x14(%rsi),%rax
    30df:	50                   	push   %rax
    30e0:	48 8d 46 10          	lea    0x10(%rsi),%rax
    30e4:	50                   	push   %rax
    30e5:	4c 8d 4e 0c          	lea    0xc(%rsi),%r9
    30e9:	4c 8d 46 08          	lea    0x8(%rsi),%r8
    30ed:	48 8d 35 78 25 00 00 	lea    0x2578(%rip),%rsi        # 566c <array.0+0x30c>
    30f4:	b8 00 00 00 00       	mov    $0x0,%eax
    30f9:	e8 62 f2 ff ff       	call   2360 <__isoc99_sscanf@plt>
    30fe:	48 83 c4 10          	add    $0x10,%rsp
    3102:	83 f8 05             	cmp    $0x5,%eax
    3105:	7e 05                	jle    310c <read_six_numbers+0x40>
    3107:	48 83 c4 08          	add    $0x8,%rsp
    310b:	c3                   	ret
    310c:	e8 35 ff ff ff       	call   3046 <explode_bomb>
```

注意到 `30f9` 处调用了 `sscanf` 函数，那么想必前几行就是在准备 `sscanf` 的参数了。`sscanf` 的第一个参数为要 scan 的字符串地址，存在 `%rdi` 中，第二个参数为匹配的模式串，存在 `%rsi` 中。这个函数要读六个数字，那么后面的几个参数就应当为读到的数字的地址。下面给出相应注释：

```assembly
00000000000030cc <read_six_numbers>:
    30cc:	f3 0f 1e fa          	endbr64
    30d0:	48 83 ec 08          	sub    $0x8,%rsp # 开栈
    30d4:	48 89 f2             	mov    %rsi,%rdx # rdx 是第三个参数，放的是第一个数字
                                                     # 而这正是 %rsi 指向的地址
    30d7:	48 8d 4e 04          	lea    0x4(%rsi),%rcx # 第二个数字，%rsi+4
    30db:	48 8d 46 14          	lea    0x14(%rsi),%rax # 第六个数字，参数不够了
    30df:	50                   	push   %rax            # 扔栈里
    30e0:	48 8d 46 10          	lea    0x10(%rsi),%rax # 第五个数字，同理
    30e4:	50                   	push   %rax
    30e5:	4c 8d 4e 0c          	lea    0xc(%rsi),%r9   # 第四个数字
    30e9:	4c 8d 46 08          	lea    0x8(%rsi),%r8   # 第三个数字
    30ed:	48 8d 35 78 25 00 00 	lea    0x2578(%rip),%rsi        # 566c <array.0+0x30c>
                                                            # 把模式串取出来，在 gdb 里面将其打印，可知为
                                                            # "%d %d %d %d %d %d"
    30f4:	b8 00 00 00 00       	mov    $0x0,%eax       # 清空 eax
    30f9:	e8 62 f2 ff ff       	call   2360 <__isoc99_sscanf@plt> # 调用 sscanf 进行读取
    30fe:	48 83 c4 10          	add    $0x10,%rsp      # 把刚push用掉的空间还回去（16个字节）
    3102:	83 f8 05             	cmp    $0x5,%eax       # 比较 sscanf 的返回值与 5 的值
    3105:	7e 05                	jle    310c <read_six_numbers+0x40> # 如果小于等于 5，炸弹爆炸
    3107:	48 83 c4 08          	add    $0x8,%rsp       # 还回去一开始的 8 个字节
    310b:	c3                   	ret
    310c:	e8 35 ff ff ff       	call   3046 <explode_bomb>
```

这样便已看明白 `read_six_numbers` 的行为，是读 $6$ 个数字，索性称为 $a_0,\cdots,a_5$ 吧。他们存放在栈里。看看接下来要做什么。

```assembly

    27fd:	83 3c 24 00          	cmpl   $0x0,(%rsp)
    2801:	75 07                	jne    280a <phase_2+0x2e> # 如果 a[0] != 0，爆炸
    2803:	83 7c 24 04 01       	cmpl   $0x1,0x4(%rsp)      # 比较 a[1] 与 1
    2808:	74 05                	je     280f <phase_2+0x33> # a[1] 不为 1 就爆炸
    280a:	e8 37 08 00 00       	call   3046 <explode_bomb>
    280f:	bb 02 00 00 00       	mov    $0x2,%ebx           # 令 ebx=2，可以看出来 ebx 是循环指标变量
    2814:	eb 03                	jmp    2819 <phase_2+0x3d>
    2816:	83 c3 01             	add    $0x1,%ebx
    2819:	83 fb 05             	cmp    $0x5,%ebx
    281c:	7f 20                	jg     283e <phase_2+0x62> # 这里是 for(int i=2;i<=5;++i)
    281e:	48 63 d3             	movslq %ebx,%rdx           # 将 ebx 符号扩展传送到 rdx
    2821:	8d 4b fe             	lea    -0x2(%rbx),%ecx     # 令 ecx = rbx-2，即 i - 2
    2824:	48 63 c9             	movslq %ecx,%rcx
    2827:	8d 43 ff             	lea    -0x1(%rbx),%eax     # 令 eax = i - 1
    282a:	48 98                	cltq
    282c:	8b 04 84             	mov    (%rsp,%rax,4),%eax  # 取出 a[i - 1]
    282f:	03 04 8c             	add    (%rsp,%rcx,4),%eax  # 得到 a[i - 1] + a[i - 2]
    2832:	39 04 94             	cmp    %eax,(%rsp,%rdx,4)  # 将 a[i] 与 a[i - 1] + a[i - 2] 比较
    2835:	74 df                	je     2816 <phase_2+0x3a> # 必须相等，不等就炸
    2837:	e8 0a 08 00 00       	call   3046 <explode_bomb>
    283c:	eb d8                	jmp    2816 <phase_2+0x3a> # 循环结束
    283e:	48 8b 44 24 18       	mov    0x18(%rsp),%rax     # 取到存在栈里面的金丝雀值
    2843:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    284a:	00 00 
    284c:	75 06                	jne    2854 <phase_2+0x78> # 如果金丝雀值被攻击，调用__stack_chk_fail
    284e:	48 83 c4 20          	add    $0x20,%rsp
    2852:	5b                   	pop    %rbx                # 取回一开始压进栈里的 rbx
    2853:	c3                   	ret
    2854:	e8 57 fa ff ff       	call   22b0 <__stack_chk_fail@plt>
```

所以这六个数字显然为 `0 1 1 2 3 5`，是为斐波那契数列。

## Phase 3

顺带一提的是，观察 `main` 函数我们知道，可以将已经破开的字符串放进一个文件里面，就不用每次都输入了。到了文件里面没有的才会从 `stdin` 里面读入。

所以我们不妨令 `main` 的参数为文件名，在 `gdb` 中 `set args data.in` 即可。

```bash
Reading symbols from bomb...
(gdb) set args data.in
(gdb) b explode_bomb
Breakpoint 1 at 0x3046
(gdb) b phase_3
Breakpoint 2 at 0x2859
(gdb) r
Starting program: /home/ubuntu/bomb162/bomb data.in
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Welcome to Mr.Gin's little bomb. You have 6 phases with
which to blow yourself up. Have a nice day! Mua ha ha ha!
Phase 1 defused. How about the next one?
That's number 2. Keep going!
```

此时我们就可以继续着眼于第三个 phase 了。

phase 3 更长，我们先看前几行，浅浅注释一波：

```assembly
0000000000002859 <phase_3>:
    2859:	f3 0f 1e fa          	endbr64
    285d:	48 83 ec 18          	sub    $0x18,%rsp
    2861:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2868:	00 00 
    286a:	48 89 44 24 08       	mov    %rax,0x8(%rsp)        # 金丝雀值
    286f:	31 c0                	xor    %eax,%eax             # 清空 eax
    2871:	48 8d 4c 24 04       	lea    0x4(%rsp),%rcx        # 令 rcx=%rsp+4
    2876:	48 89 e2             	mov    %rsp,%rdx             # 令 rdx=%rsp
    2879:	48 8d 35 f8 2d 00 00 	lea    0x2df8(%rip),%rsi        # 5678 <array.0+0x318>
    2880:	e8 db fa ff ff       	call   2360 <__isoc99_sscanf@plt>
```

看到调用 `sscanf` 便知又是要从字符串里面读些什么。先看看 `%rsi` 是什么情况。可以通过 `*phase_3+39` 打断点到相应的指令位置，然后输出 `%rsi`。

```bash
(gdb) disas
Dump of assembler code for function phase_3:
=> 0x0000555555556859 <+0>:     endbr64
   0x000055555555685d <+4>:     sub    $0x18,%rsp
   0x0000555555556861 <+8>:     mov    %fs:0x28,%rax
   0x000055555555686a <+17>:    mov    %rax,0x8(%rsp)
   0x000055555555686f <+22>:    xor    %eax,%eax
   0x0000555555556871 <+24>:    lea    0x4(%rsp),%rcx
   0x0000555555556876 <+29>:    mov    %rsp,%rdx
   0x0000555555556879 <+32>:    lea    0x2df8(%rip),%rsi        # 0x555555559678
   0x0000555555556880 <+39>:    call   0x555555556360 <__isoc99_sscanf@plt>
(gdb) b *phase_3+39
Breakpoint 3 at 0x555555556880
(gdb) c
Continuing.

Breakpoint 3, 0x0000555555556880 in phase_3 ()
(gdb) x/s $rsi
0x555555559678: "%d %d"
```

这下我们知道，phase 3 是需要我们输入两个整数，存放在 `%rsp` 和 `%rsp+4` 处，暂且分别叫做 $x$ 和 $y$ 吧。看看后面要做什么：

```assembly
    2880:	e8 db fa ff ff       	call   2360 <__isoc99_sscanf@plt>
    2885:	83 f8 01             	cmp    $0x1,%eax
    2888:	7e 1e                	jle    28a8 <phase_3+0x4f>     # 判断有没有成功读，没就爆炸
    288a:	83 3c 24 07          	cmpl   $0x7,(%rsp)
    288e:	0f 87 8d 00 00 00    	ja     2921 <phase_3+0xc8>     # 若 x>7，也爆炸
    2894:	8b 04 24             	mov    (%rsp),%eax             # 把 x 提取到 eax 中
    2897:	48 8d 15 a2 2a 00 00 	lea    0x2aa2(%rip),%rdx        # 5340 <_IO_stdin_used+0x340>
    
    289e:	48 63 04 82          	movslq (%rdx,%rax,4),%rax
    28a2:	48 01 d0             	add    %rdx,%rax
    28a5:	3e ff e0             	notrack jmp *%rax
```

到了比较麻烦的地方了。这里是取了个 `%rdx`，然后要跳到 `%rdx * 2 + 4 * x` 的地方。

这个东西有点像 `switch` 语句，但我们如果不真带入个啥也没法分析了。不妨我们就让输入的 $x=0$，然后看看跳到了哪。

发现跳到了这个地方：

```assembly
    28af:	8b 44 24 04          	mov    0x4(%rsp),%eax
    28b3:	05 82 02 00 00       	add    $0x282,%eax
    28b8:	3d b0 06 00 00       	cmp    $0x6b0,%eax
    28bd:	75 71                	jne    2930 <phase_3+0xd7>
    28bf:	48 8b 44 24 08       	mov    0x8(%rsp),%rax
    28c4:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    28cb:	00 00 
    28cd:	75 68                	jne    2937 <phase_3+0xde>
    28cf:	48 83 c4 18          	add    $0x18,%rsp
    28d3:	c3                   	ret
```

第一行相当于把 $y$ 取出来了。然后加上了 `0x282` 后与 `0x6b0` 比较，如果不等于的话就跳去 `2930`，即爆炸。否则好像就可以了。

所以有一个答案为 `0 1070`。剩下的分支都是类似的，但都会跳到与 `0x6b0` 的比较。

事实上用 IDA 的反编译代码也如此：

```c
unsigned __int64 __fastcall phase_3(__int64 a1)
{
  int v1; // eax
  int v3; // [rsp+0h] [rbp-18h] BYREF
  int v4; // [rsp+4h] [rbp-14h] BYREF
  unsigned __int64 v5; // [rsp+8h] [rbp-10h]

  v5 = __readfsqword(0x28u);
  if ( (int)__isoc99_sscanf(a1, "%d %d", &v3, &v4) <= 1 )
    explode_bomb();
  switch ( v3 )
  {
    case 0:
      v1 = v4 + 642;
      break;
    case 1:
      v1 = v4 + 443;
      break;
    case 2:
      v1 = v4 + 584;
      break;
    case 3:
      v1 = v4 + 316;
      break;
    case 4:
      v1 = v4 + 188;
      break;
    case 5:
      v1 = v4 + 270;
      break;
    case 6:
      v1 = v4 + 856;
      break;
    case 7:
      v1 = v4 + 760;
      break;
    default:
      explode_bomb();
  }
  if ( v1 != 1712 )
    explode_bomb();
  return v5 - __readfsqword(0x28u);
}
```

## Phase 4

看看开头吧：

```assembly
0000000000002977 <phase_4>:
    2977:	f3 0f 1e fa          	endbr64
    297b:	55                   	push   %rbp
    297c:	53                   	push   %rbx
    297d:	48 83 ec 18          	sub    $0x18,%rsp
    2981:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2988:	00 00 
    298a:	48 89 44 24 08       	mov    %rax,0x8(%rsp)
    298f:	31 c0                	xor    %eax,%eax
    2991:	48 8d 4c 24 04       	lea    0x4(%rsp),%rcx
    2996:	48 89 e2             	mov    %rsp,%rdx
    2999:	48 8d 35 d8 2c 00 00 	lea    0x2cd8(%rip),%rsi        # 5678 <array.0+0x318>
    29a0:	e8 bb f9 ff ff       	call   2360 <__isoc99_sscanf@plt>
```

照例把 `sscanf` 的模式串找出来，仍为 `%d %d`。

```assembly
    29a5:	83 f8 02             	cmp    $0x2,%eax             # 照例
    29a8:	75 06                	jne    29b0 <phase_4+0x39>
    29aa:	83 3c 24 05          	cmpl   $0x5,(%rsp)           # x 与 5 比较
    29ae:	74 05                	je     29b5 <phase_4+0x3e>   # 说明 x=5
    29b0:	e8 91 06 00 00       	call   3046 <explode_bomb>
    29b5:	bd 00 00 00 00       	mov    $0x0,%ebp             # ebp,ebx 置零
    29ba:	bb 00 00 00 00       	mov    $0x0,%ebx
    
    29bf:	eb 0c                	jmp    29cd <phase_4+0x56>
    29c1:	89 df                	mov    %ebx,%edi
    29c3:	e8 74 ff ff ff       	call   293c <func4>          # 计算 func4(ebx)
    29c8:	01 c5                	add    %eax,%ebp             # ebp += func4(ebx)
    29ca:	83 c3 01             	add    $0x1,%ebx             # ebx += 1
    29cd:	39 1c 24             	cmp    %ebx,(%rsp)           # 若 x > ebx
    29d0:	7f ef                	jg     29c1 <phase_4+0x4a>   # 就跳到 29c1
    
    29d2:	39 6c 24 04          	cmp    %ebp,0x4(%rsp)        # ebp != y 就爆炸
    29d6:	75 17                	jne    29ef <phase_4+0x78>
    29d8:	48 8b 44 24 08       	mov    0x8(%rsp),%rax
    29dd:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    29e4:	00 00 
    29e6:	75 0e                	jne    29f6 <phase_4+0x7f>
    29e8:	48 83 c4 18          	add    $0x18,%rsp
    29ec:	5b                   	pop    %rbx
    29ed:	5d                   	pop    %rbp
    29ee:	c3                   	ret
    29ef:	e8 52 06 00 00       	call   3046 <explode_bomb>
    29f4:	eb e2                	jmp    29d8 <phase_4+0x61>
    29f6:	e8 b5 f8 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

所以这就相当于：

```c
int val = 0, i = 0;
for (i = 0; i < x; ++i) {
    val += func4(i)
}

if (val != y) explode_bomb
```

其实我们都不必关心 `func4` 里面是个什么东西，只需要执行到 `29d2` 的时候找到 `ebp` 的值就可以了。

```bash
(gdb) p $ebp
$1 = 20
```

所以这题答案为 `5 20`，搞定！（怎么感觉比 phase 3 还简单）

## Phase 5

看看是个啥情况：

```assembly
00000000000029fb <phase_5>:
    29fb:	f3 0f 1e fa          	endbr64
    29ff:	53                   	push   %rbx
    2a00:	48 83 ec 10          	sub    $0x10,%rsp
    2a04:	48 89 fb             	mov    %rdi,%rbx
    2a07:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2a0e:	00 00 
    2a10:	48 89 44 24 08       	mov    %rax,0x8(%rsp)
    2a15:	31 c0                	xor    %eax,%eax
    2a17:	e8 fd 02 00 00       	call   2d19 <string_length>
    2a1c:	83 f8 06             	cmp    $0x6,%eax
    2a1f:	75 07                	jne    2a28 <phase_5+0x2d>
    2a21:	b8 00 00 00 00       	mov    $0x0,%eax
    2a26:	eb 23                	jmp    2a4b <phase_5+0x50>
    2a28:	e8 19 06 00 00       	call   3046 <explode_bomb>
```

看到这里就知道我们需要的是一个长度为 $6$ 的字符串，且继续往下看，发现又是一个循环。发现又去寻了东西来，发现是 `maduiersnfotvbylSo you think you can stop the bomb with ctrl-c, d`。令这个数组为 `T[]` 吧

```assembly
    2a21:	b8 00 00 00 00       	mov    $0x0,%eax               # i = 0
    2a26:	eb 23                	jmp    2a4b <phase_5+0x50>
    2a28:	e8 19 06 00 00       	call   3046 <explode_bomb>
    2a2d:	eb f2                	jmp    2a21 <phase_5+0x26>
    
    2a2f:	48 63 c8             	movslq %eax,%rcx               # rcx = i
    2a32:	0f b6 14 0b          	movzbl (%rbx,%rcx,1),%edx      # edx = S[i]
    2a36:	83 e2 0f             	and    $0xf,%edx               # edx &= 0xF
    2a39:	48 8d 35 20 29 00 00 	lea    0x2920(%rip),%rsi        # 5360 <array.0> 令这个数组叫 T[] 吧
    2a40:	0f b6 14 16          	movzbl (%rsi,%rdx,1),%edx      # edx = T[edx]
    2a44:	88 54 0c 01          	mov    %dl,0x1(%rsp,%rcx,1)    # 将其移到 rsp+i+1 的位置里面
    2a48:	83 c0 01             	add    $0x1,%eax               # eax += 1
    2a4b:	83 f8 05             	cmp    $0x5,%eax               # i <= 5
    2a4e:	7e df                	jle    2a2f <phase_5+0x34>
```

所以，这一部分是令 `A[i] = T[S[i] & 0xF]`，相当于做了些变换处理。我们继续看：

```assembly
   2a50:	c6 44 24 07 00       	movb   $0x0,0x7(%rsp)          # 将 rsp+7 的位置置零。可以理解为是 A[6] = 0
    2a55:	48 8d 7c 24 01       	lea    0x1(%rsp),%rdi          # 将 A[0] 的地址扔给 %rdi
    2a5a:	48 8d 35 43 27 00 00 	lea    0x2743(%rip),%rsi        # 51a4 <_IO_stdin_used+0x1a4> 取一个串
    2a61:	e8 cb 02 00 00       	call   2d31 <strings_not_equal> # 比较是否相等
    2a66:	85 c0                	test   %eax,%eax
    2a68:	75 16                	jne    2a80 <phase_5+0x85>
    2a6a:	48 8b 44 24 08       	mov    0x8(%rsp),%rax
    2a6f:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    2a76:	00 00 
    2a78:	75 0d                	jne    2a87 <phase_5+0x8c>
    2a7a:	48 83 c4 10          	add    $0x10,%rsp
    2a7e:	5b                   	pop    %rbx
    2a7f:	c3                   	ret
    2a80:	e8 c1 05 00 00       	call   3046 <explode_bomb>
    2a85:	eb e3                	jmp    2a6a <phase_5+0x6f>
    2a87:	e8 24 f8 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

把对应的串取出来，可以得到是 `bruins`。

那么我们需要的就是构造对应的输入，这个是不难的，考虑到 writeup 中补充说了 phase_5 的答案为数字和字母，所以构造出答案 `M63487`。

## Phase 6

最难的一个来了。

```assembly
0000000000002a8c <phase_6>:
    2a8c:	f3 0f 1e fa          	endbr64
    2a90:	41 54                	push   %r12
    2a92:	55                   	push   %rbp
    2a93:	53                   	push   %rbx
    2a94:	48 83 ec 60          	sub    $0x60,%rsp
    2a98:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2a9f:	00 00 
    2aa1:	48 89 44 24 58       	mov    %rax,0x58(%rsp)
    2aa6:	31 c0                	xor    %eax,%eax
    2aa8:	48 89 e6             	mov    %rsp,%rsi
    2aab:	e8 1c 06 00 00       	call   30cc <read_six_numbers>
    2ab0:	bd 00 00 00 00       	mov    $0x0,%ebp
    2ab5:	eb 27                	jmp    2ade <phase_6+0x52>
    2ab7:	e8 8a 05 00 00       	call   3046 <explode_bomb>
    2abc:	eb 33                	jmp    2af1 <phase_6+0x65>
    2abe:	83 c3 01             	add    $0x1,%ebx
    2ac1:	83 fb 05             	cmp    $0x5,%ebx
    2ac4:	7f 15                	jg     2adb <phase_6+0x4f>
    2ac6:	48 63 c5             	movslq %ebp,%rax
    2ac9:	48 63 d3             	movslq %ebx,%rdx
    2acc:	8b 3c 94             	mov    (%rsp,%rdx,4),%edi
    2acf:	39 3c 84             	cmp    %edi,(%rsp,%rax,4)
    2ad2:	75 ea                	jne    2abe <phase_6+0x32>
    2ad4:	e8 6d 05 00 00       	call   3046 <explode_bomb>
    2ad9:	eb e3                	jmp    2abe <phase_6+0x32>
    2adb:	44 89 e5             	mov    %r12d,%ebp
    2ade:	83 fd 05             	cmp    $0x5,%ebp
    2ae1:	7f 17                	jg     2afa <phase_6+0x6e>
    2ae3:	48 63 c5             	movslq %ebp,%rax
    2ae6:	8b 04 84             	mov    (%rsp,%rax,4),%eax
    2ae9:	83 e8 01             	sub    $0x1,%eax
    2aec:	83 f8 05             	cmp    $0x5,%eax
    2aef:	77 c6                	ja     2ab7 <phase_6+0x2b>
    2af1:	44 8d 65 01          	lea    0x1(%rbp),%r12d
    2af5:	44 89 e3             	mov    %r12d,%ebx
    2af8:	eb c7                	jmp    2ac1 <phase_6+0x35>
    2afa:	be 00 00 00 00       	mov    $0x0,%esi
    2aff:	eb 17                	jmp    2b18 <phase_6+0x8c>
    2b01:	48 8b 52 08          	mov    0x8(%rdx),%rdx
    2b05:	83 c0 01             	add    $0x1,%eax
    2b08:	48 63 ce             	movslq %esi,%rcx
    2b0b:	39 04 8c             	cmp    %eax,(%rsp,%rcx,4)
    2b0e:	7f f1                	jg     2b01 <phase_6+0x75>
    2b10:	48 89 54 cc 20       	mov    %rdx,0x20(%rsp,%rcx,8)
    2b15:	83 c6 01             	add    $0x1,%esi
    2b18:	83 fe 05             	cmp    $0x5,%esi
    2b1b:	7f 0e                	jg     2b2b <phase_6+0x9f>
    2b1d:	b8 01 00 00 00       	mov    $0x1,%eax
    2b22:	48 8d 15 e7 65 00 00 	lea    0x65e7(%rip),%rdx        # 9110 <node1>
    2b29:	eb dd                	jmp    2b08 <phase_6+0x7c>
    2b2b:	48 8b 5c 24 20       	mov    0x20(%rsp),%rbx
    2b30:	48 89 d9             	mov    %rbx,%rcx
    2b33:	b8 01 00 00 00       	mov    $0x1,%eax
    2b38:	eb 12                	jmp    2b4c <phase_6+0xc0>
    2b3a:	48 63 d0             	movslq %eax,%rdx
    2b3d:	48 8b 54 d4 20       	mov    0x20(%rsp,%rdx,8),%rdx
    2b42:	48 89 51 08          	mov    %rdx,0x8(%rcx)
    2b46:	83 c0 01             	add    $0x1,%eax
    2b49:	48 89 d1             	mov    %rdx,%rcx
    2b4c:	83 f8 05             	cmp    $0x5,%eax
    2b4f:	7e e9                	jle    2b3a <phase_6+0xae>
    2b51:	48 c7 41 08 00 00 00 	movq   $0x0,0x8(%rcx)
    2b58:	00 
    2b59:	bd 00 00 00 00       	mov    $0x0,%ebp
    2b5e:	eb 07                	jmp    2b67 <phase_6+0xdb>
    2b60:	48 8b 5b 08          	mov    0x8(%rbx),%rbx
    2b64:	83 c5 01             	add    $0x1,%ebp
    2b67:	83 fd 04             	cmp    $0x4,%ebp
    2b6a:	7f 11                	jg     2b7d <phase_6+0xf1>
    2b6c:	48 8b 43 08          	mov    0x8(%rbx),%rax
    2b70:	8b 00                	mov    (%rax),%eax
    2b72:	39 03                	cmp    %eax,(%rbx)
    2b74:	7d ea                	jge    2b60 <phase_6+0xd4>
    2b76:	e8 cb 04 00 00       	call   3046 <explode_bomb>
    2b7b:	eb e3                	jmp    2b60 <phase_6+0xd4>
    2b7d:	48 8b 44 24 58       	mov    0x58(%rsp),%rax
    2b82:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    2b89:	00 00 
    2b8b:	75 09                	jne    2b96 <phase_6+0x10a>
    2b8d:	48 83 c4 60          	add    $0x60,%rsp
    2b91:	5b                   	pop    %rbx
    2b92:	5d                   	pop    %rbp
    2b93:	41 5c                	pop    %r12
    2b95:	c3                   	ret
    2b96:	e8 15 f7 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

先看前面吧，又是熟悉的读六个整数，要做些什么妖呢：

```assembly
    2a8c:	f3 0f 1e fa          	endbr64
    2a90:	41 54                	push   %r12                         # caller-saved
    2a92:	55                   	push   %rbp                         # 
    2a93:	53                   	push   %rbx 
    2a94:	48 83 ec 60          	sub    $0x60,%rsp
    2a98:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2a9f:	00 00 
    2aa1:	48 89 44 24 58       	mov    %rax,0x58(%rsp)
    2aa6:	31 c0                	xor    %eax,%eax
    2aa8:	48 89 e6             	mov    %rsp,%rsi
    2aab:	e8 1c 06 00 00       	call   30cc <read_six_numbers>
    
    2ab0:	bd 00 00 00 00       	mov    $0x0,%ebp                    # 令 ebp=0，令其为 i 吧
    2ab5:	eb 27                	jmp    2ade <phase_6+0x52>          # 开始循环
    2ab7:	e8 8a 05 00 00       	call   3046 <explode_bomb>
    2abc:	eb 33                	jmp    2af1 <phase_6+0x65>
    2abe:	83 c3 01             	add    $0x1,%ebx                    # ++j
    2ac1:	83 fb 05             	cmp    $0x5,%ebx                    # j
    2ac4:	7f 15                	jg     2adb <phase_6+0x4f>          # j > 5，则到 2adb
    2ac6:	48 63 c5             	movslq %ebp,%rax                    # rax = i
    2ac9:	48 63 d3             	movslq %ebx,%rdx                    # rdx = j
    2acc:	8b 3c 94             	mov    (%rsp,%rdx,4),%edi           # edi = a[j]
    2acf:	39 3c 84             	cmp    %edi,(%rsp,%rax,4)           # 比较 a[i] 与 a[j]
    2ad2:	75 ea                	jne    2abe <phase_6+0x32>          # 若 a[i] != a[j]，往上跳到 2abe
    2ad4:	e8 6d 05 00 00       	call   3046 <explode_bomb>          # 否则爆炸
    2ad9:	eb e3                	jmp    2abe <phase_6+0x32>
    
    2adb:	44 89 e5             	mov    %r12d,%ebp                  # i = i + 1
    2ade:	83 fd 05             	cmp    $0x5,%ebp                   #
    2ae1:	7f 17                	jg     2afa <phase_6+0x6e>         # 若 i > 5，就调到后面去
    
    2ae3:	48 63 c5             	movslq %ebp,%rax                   # rax=i
    2ae6:	8b 04 84             	mov    (%rsp,%rax,4),%eax          # 取出 a[i]
    2ae9:	83 e8 01             	sub    $0x1,%eax                   # a[i] - 1
    2aec:	83 f8 05             	cmp    $0x5,%eax                   # a[i] - 1 与 5 比较
    2aef:	77 c6                	ja     2ab7 <phase_6+0x2b>         # 若 a[i] - 1 > 5，爆炸
    2af1:	44 8d 65 01          	lea    0x1(%rbp),%r12d             # i + 1
    2af5:	44 89 e3             	mov    %r12d,%ebx                  # ebx = i + 1
    2af8:	eb c7                	jmp    2ac1 <phase_6+0x35>         # 跳到 2ac1
```

仔细分析之后发现是一个二重循环，内容如下：

```c
int i, j;

for (i = 0; i <= 5; ++i) {
    if (a[i] - 1 > 5)
        explode_bomb();
    for (int j = i + 1; j <= 5; ++j) {
        if (a[i] == a[j])
            explode_bomb();
    }
}
```

这告诉我们，输入的必须是 $6$ 个互不相等的数字，而且还不能超过 $6$。

循环结束后会跳到 `2afa`，我们先看前面几行。

```assembly
    2afa:	be 00 00 00 00       	mov    $0x0,%esi               # 根据后面怀疑其为下标
    2aff:	eb 17                	jmp    2b18 <phase_6+0x8c>
    2b01:	48 8b 52 08          	mov    0x8(%rdx),%rdx
    2b05:	83 c0 01             	add    $0x1,%eax
    2b08:	48 63 ce             	movslq %esi,%rcx
    2b0b:	39 04 8c             	cmp    %eax,(%rsp,%rcx,4)
    2b0e:	7f f1                	jg     2b01 <phase_6+0x75>
    2b10:	48 89 54 cc 20       	mov    %rdx,0x20(%rsp,%rcx,8)
    2b15:	83 c6 01             	add    $0x1,%esi
    2b18:	83 fe 05             	cmp    $0x5,%esi               # i <= 5
    2b1b:	7f 0e                	jg     2b2b <phase_6+0x9f>
    2b1d:	b8 01 00 00 00       	mov    $0x1,%eax               # eax = 1
    2b22:	48 8d 15 e7 65 00 00 	lea    0x65e7(%rip),%rdx        # 9110 <node1>
    2b29:	eb dd                	jmp    2b08 <phase_6+0x7c>
```

现在比较逆天的是，出现了一个 `<node1>`，我们在这个地方打好断点后，看看那段内存是个什么情况：

```bash
(gdb) x/32 $rdx
0x55555555d110 <node1>: 0x00000257      0x00000001      0x5555d120      0x00005555
0x55555555d120 <node2>: 0x000002db      0x00000002      0x5555d130      0x00005555
0x55555555d130 <node3>: 0x0000007a      0x00000003      0x5555d140      0x00005555
0x55555555d140 <node4>: 0x000001ba      0x00000004      0x5555d150      0x00005555
0x55555555d150 <node5>: 0x000003d9      0x00000005      0x5555c080      0x00005555
0x55555555d160 <host_table>:    0x555596c2      0x00005555      0x555596cb      0x00005555
0x55555555d170 <host_table+16>: 0x555596d5      0x00005555      0x555596dc      0x00005555
0x55555555d180 <host_table+32>: 0x555596e3      0x00005555      0x555596ea      0x00005555
```

发现这一段内存的形式有点像是一个链表。第一个字段存值，第二个字段存下标，第三和第四个字段一起成为指向下一个元素的指针。

发现 node5 指向了 `0x000055555555c080`，检查一下那段内存：

```bash
(gdb) x/4 0x000055555555c080
0x55555555c080 <node6>: 0x00000285      0x00000006      0x00000000      0x00000000
```

合理了。说明这个链表的初始状态是 node1->node2->node3->node4->node5->node6->NULL。输入的数字被限制在 $6$ 以内，想必也与此有关。

然后我们再仔细看看他在做些什么：

```assembly
    2afa:	be 00 00 00 00       	mov    $0x0,%esi               # 根据后面怀疑其为下标
    2aff:	eb 17                	jmp    2b18 <phase_6+0x8c>
    
    2b01:	48 8b 52 08          	mov    0x8(%rdx),%rdx          # rdx += 8，即往后面跳一个节点
    2b05:	83 c0 01             	add    $0x1,%eax               # eax += 1
    2b08:	48 63 ce             	movslq %esi,%rcx               
    2b0b:	39 04 8c             	cmp    %eax,(%rsp,%rcx,4)      # 比较 eax 与 a[i] 的值
    2b0e:	7f f1                	jg     2b01 <phase_6+0x75>     # 直到 eax 大于等于 a[i] 
    
    2b10:	48 89 54 cc 20       	mov    %rdx,0x20(%rsp,%rcx,8)  # 将 rdx 的值赋到 rsp+20+8*i 的地方
    2b15:	83 c6 01             	add    $0x1,%esi
    2b18:	83 fe 05             	cmp    $0x5,%esi               # i <= 5
    2b1b:	7f 0e                	jg     2b2b <phase_6+0x9f>
    2b1d:	b8 01 00 00 00       	mov    $0x1,%eax               # eax = 1
    2b22:	48 8d 15 e7 65 00 00 	lea    0x65e7(%rip),%rdx        # 9110 <node1>
    2b29:	eb dd                	jmp    2b08 <phase_6+0x7c>
```

我们大概可以明白，`rsp+20` 起应当是维护了一个指针数组 `node* nodes[6]`，伪代码可以写成：

```c
int i;

for (i = 0; i <= 5; ++i) {
    int val = 1;
    node* p = &node1;
    while (val < a[i]) {
        p = p->next;
        ++val;
    }
    nodes[i] = p;
}
```

于是我们知道，`nodes[i]` 维护的便是指向 `node_{a[i]}` 的指针。回忆之前所说读入的数字应当个个不同，所以可以确定的是，这段代码是将链表中的节点按照给定的顺序重新取了出来，我们继续往下看，发现应该又是一个循环：

```assembly
    2b2b:	48 8b 5c 24 20       	mov    0x20(%rsp),%rbx            # rbx = nodes[0]
    2b30:	48 89 d9             	mov    %rbx,%rcx                  # rcx = rbx = nodes[0]
    2b33:	b8 01 00 00 00       	mov    $0x1,%eax                  # i = 1
    2b38:	eb 12                	jmp    2b4c <phase_6+0xc0>
    2b3a:	48 63 d0             	movslq %eax,%rdx                
    2b3d:	48 8b 54 d4 20       	mov    0x20(%rsp,%rdx,8),%rdx     # 取 rdx = nodes[i]
    2b42:	48 89 51 08          	mov    %rdx,0x8(%rcx)             # 将 rcx 的下一个节点改成 nodes[i]
    2b46:	83 c0 01             	add    $0x1,%eax                  # ++i
    2b49:	48 89 d1             	mov    %rdx,%rcx                  # rcx = nodes[i]
    2b4c:	83 f8 05             	cmp    $0x5,%eax
    2b4f:	7e e9                	jle    2b3a <phase_6+0xae>        # i <= 5
    
    2b51:	48 c7 41 08 00 00 00 	movq   $0x0,0x8(%rcx)             # 循环结束，令最后一个节点的指针指向空
    2b58:	00
```

这便是把那些节点的顺序进行了重新安排，安排成了我们输入指定的顺序。

继续！马上胜利了！发现又有一个循环，拆解：

```assembly
    # 此时 rbx 的值为 nodes[0]
    2b59:	bd 00 00 00 00       	mov    $0x0,%ebp                  # i = 0
    2b5e:	eb 07                	jmp    2b67 <phase_6+0xdb>
    2b60:	48 8b 5b 08          	mov    0x8(%rbx),%rbx             # 跳到下一个节点
    2b64:	83 c5 01             	add    $0x1,%ebp
    2b67:	83 fd 04             	cmp    $0x4,%ebp
    2b6a:	7f 11                	jg     2b7d <phase_6+0xf1>        # i > 4 则跳到 2b7d，结束循环
    2b6c:	48 8b 43 08          	mov    0x8(%rbx),%rax             
    2b70:	8b 00                	mov    (%rax),%eax
    2b72:	39 03                	cmp    %eax,(%rbx)                # 比较当前节点与下一节点的值
    2b74:	7d ea                	jge    2b60 <phase_6+0xd4>        # 当前节点必须大于等于下一节点的值
    2b76:	e8 cb 04 00 00       	call   3046 <explode_bomb>
    2b7b:	eb e3                	jmp    2b60 <phase_6+0xd4>
    
    2b7d:	48 8b 44 24 58       	mov    0x58(%rsp),%rax            # 这之后都是收尾操作了
    2b82:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    2b89:	00 00 
    2b8b:	75 09                	jne    2b96 <phase_6+0x10a>
    2b8d:	48 83 c4 60          	add    $0x60,%rsp
    2b91:	5b                   	pop    %rbx
    2b92:	5d                   	pop    %rbp
    2b93:	41 5c                	pop    %r12
    2b95:	c3                   	ret
    2b96:	e8 15 f7 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

这是在干什么，这是要求最后按照我们的顺序排好后，链表中节点的值是递减的。

回头去看看各个节点的值，便知道答案为 `5 2 6 1 4 3`。

## Secret Phase

### 炸弹本地化

主要在于很无奈的是，现在提交 secret phase 会用掉两个 grace day。所以就只能破拆一下二进制文件的样子。

先看 `initialize_bomb`：

```assembly
0000000000002d80 <initialize_bomb>:
    2d80:	f3 0f 1e fa          	endbr64
    2d84:	48 81 ec 00 10 00 00 	sub    $0x1000,%rsp
    2d8b:	48 83 0c 24 00       	orq    $0x0,(%rsp)
    2d90:	48 81 ec 00 10 00 00 	sub    $0x1000,%rsp
    2d97:	48 83 0c 24 00       	orq    $0x0,(%rsp)
    2d9c:	48 83 ec 58          	sub    $0x58,%rsp
    2da0:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2da7:	00 00 
    2da9:	48 89 84 24 48 20 00 	mov    %rax,0x2048(%rsp)
    2db0:	00 
    2db1:	31 c0                	xor    %eax,%eax
    2db3:	48 8d 35 d0 fe ff ff 	lea    -0x130(%rip),%rsi        # 2c8a <sig_handler>
    2dba:	bf 02 00 00 00       	mov    $0x2,%edi
    2dbf:	e8 2c f5 ff ff       	call   22f0 <signal@plt>
    2dc4:	48 89 e7             	mov    %rsp,%rdi
    2dc7:	be 40 00 00 00       	mov    $0x40,%esi
    2dcc:	e8 cf f5 ff ff       	call   23a0 <gethostname@plt>
    2dd1:	85 c0                	test   %eax,%eax
    2dd3:	75 39                	jne    2e0e <initialize_bomb+0x8e>
    2dd5:	48 8d 7c 24 40       	lea    0x40(%rsp),%rdi
    2dda:	e8 5b 10 00 00       	call   3e3a <init_driver>
    2ddf:	85 c0                	test   %eax,%eax
    2de1:	78 41                	js     2e24 <initialize_bomb+0xa4>
    2de3:	bf 04 00 00 00       	mov    $0x4,%edi
    2de8:	e8 53 f5 ff ff       	call   2340 <malloc@plt>
    2ded:	c7 00 11 fa 21 20    	movl   $0x2021fa11,(%rax)
    2df3:	48 8b 94 24 48 20 00 	mov    0x2048(%rsp),%rdx
    2dfa:	00 
    2dfb:	64 48 2b 14 25 28 00 	sub    %fs:0x28,%rdx
    2e02:	00 00 
    2e04:	75 43                	jne    2e49 <initialize_bomb+0xc9>
    2e06:	48 81 c4 58 20 00 00 	add    $0x2058,%rsp
    2e0d:	c3                   	ret
    2e0e:	48 8d 3d 93 25 00 00 	lea    0x2593(%rip),%rdi        # 53a8 <array.0+0x48>
    2e15:	e8 66 f4 ff ff       	call   2280 <puts@plt>
    2e1a:	bf 08 00 00 00       	mov    $0x8,%edi
    2e1f:	e8 8c f5 ff ff       	call   23b0 <exit@plt>
    2e24:	48 8d 54 24 40       	lea    0x40(%rsp),%rdx
    2e29:	48 8d 35 e2 27 00 00 	lea    0x27e2(%rip),%rsi        # 5612 <array.0+0x2b2>
    2e30:	bf 01 00 00 00       	mov    $0x1,%edi
    2e35:	b8 00 00 00 00       	mov    $0x0,%eax
    2e3a:	e8 41 f5 ff ff       	call   2380 <__printf_chk@plt>
    2e3f:	bf 08 00 00 00       	mov    $0x8,%edi
    2e44:	e8 67 f5 ff ff       	call   23b0 <exit@plt>
    2e49:	e8 62 f4 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

发现正常情况下其会返回一个指针，指向的内容为 `0x2021fa11`，而且看主函数的代码可以发现返回值不对的话也会触发异常。

那我们就只需要让其在函数一开始就完成指令

```assembly
mov $0x4, %edi
call malloc
movl $0x2021fall, (%rax)
ret
```

就可以了。注意一开始的 `endbr64` 不能动。

需要注意不能修改二进制文件的长度，只能直接就近覆盖。而且由于 `call` 是相对地址，所以也需要计算一下新的偏移量。更改后的如下：

```assembly
0000000000002d80 <initialize_bomb>:
    2d80:	f3 0f 1e fa          	endbr64 
    2d84:	bf 04 00 00 00       	mov    $0x4,%edi
    2d89:	e8 b2 f5 ff ff       	call   2340 <malloc@plt>
    2d8e:	c7 00 11 fa 21 20    	movl   $0x2021fa11,(%rax)
    2d94:	c3                   	ret     
```

然后是 `send_msg`，即每次炸弹拆除后/炸弹爆炸时都会调用的函数，通过 IDA 反编译结果和前人资料我们知道 `send_msg` 的第二个参数是一个指向 32 位值的指针，而 `send_msg` 要将其的值置为 $1$。

所以我们让 `send_msg` 的前几行变成

```assembly
endbr64
movl   $0x1,(%rsi)
ret
```

所以改成  `c7 06 01 00 00 00 c3` 即可。

```assembly
0000000000002ee4 <send_msg>:
    2ee4:	f3 0f 1e fa          	endbr64 
    2ee8:	c7 06 01 00 00 00    	movl   $0x1,(%rsi)
    2eee:	c3                   	ret    
```

经过测试，完全不会有任何问题。

### 求解

似乎在 `main` 函数里面也没有找到 secret phase，还是先找找哪里 call 了 secret phase 吧。发现在 `phase_defused` 函数里面。我们先看第一部分控制流吧。

```assembly
000000000000324f <phase_defused>:
    324f:	f3 0f 1e fa          	endbr64
    3253:	53                   	push   %rbx
    3254:	48 89 fb             	mov    %rdi,%rbx
    3257:	c7 07 00 00 00 00    	movl   $0x0,(%rdi)
    325d:	48 89 fe             	mov    %rdi,%rsi
    3260:	bf 01 00 00 00       	mov    $0x1,%edi
    3265:	e8 7a fc ff ff       	call   2ee4 <send_msg>
    326a:	83 3b 01             	cmpl   $0x1,(%rbx)
    326d:	75 0b                	jne    327a <phase_defused+0x2b>
    326f:	83 3d 62 63 00 00 06 	cmpl   $0x6,0x6362(%rip)        # 95d8 <num_input_strings>
    3276:	74 22                	je     329a <phase_defused+0x4b>
    3278:	5b                   	pop    %rbx
    3279:	c3                   	ret
    327a:	48 8d 35 87 21 00 00 	lea    0x2187(%rip),%rsi        # 5408 <array.0+0xa8>
    3281:	bf 01 00 00 00       	mov    $0x1,%edi
    3286:	b8 00 00 00 00       	mov    $0x0,%eax
    328b:	e8 f0 f0 ff ff       	call   2380 <__printf_chk@plt>
    3290:	bf 08 00 00 00       	mov    $0x8,%edi
    3295:	e8 16 f1 ff ff       	call   23b0 <exit@plt>
```

前面的东西不必理会，主要是看到有一个全局变量叫做 `num_input_strings`，经过实验发现用 `p (int)num_input_strings` 后发现这个变量即为 `read_line` 读入的字符串个数，放在这里也就是拆掉了的炸弹个数。所以必须拆掉前面所有炸弹之后才可能进入 secret phase。

```asm
    329a:	e8 f9 f3 ff ff       	call   2698 <abracadabra>
    329f:	85 c0                	test   %eax,%eax
    32a1:	75 1a                	jne    32bd <phase_defused+0x6e>
    32a3:	48 8d 3d be 22 00 00 	lea    0x22be(%rip),%rdi        # 5568 <array.0+0x208>
    32aa:	e8 d1 ef ff ff       	call   2280 <puts@plt>
    32af:	48 8d 3d fa 22 00 00 	lea    0x22fa(%rip),%rdi        # 55b0 <array.0+0x250>
    32b6:	e8 c5 ef ff ff       	call   2280 <puts@plt>
    32bb:	eb bb                	jmp    3278 <phase_defused+0x29>
    32bd:	e8 63 f4 ff ff       	call   2725 <alohomora>
    32c2:	85 c0                	test   %eax,%eax
    32c4:	74 30                	je     32f6 <phase_defused+0xa7>
    32c6:	48 8d 3d ab 21 00 00 	lea    0x21ab(%rip),%rdi        # 5478 <array.0+0x118>
    32cd:	e8 ae ef ff ff       	call   2280 <puts@plt>
    32d2:	48 8d 3d c7 21 00 00 	lea    0x21c7(%rip),%rdi        # 54a0 <array.0+0x140>
    32d9:	e8 a2 ef ff ff       	call   2280 <puts@plt>
    32de:	48 8d 3d f3 21 00 00 	lea    0x21f3(%rip),%rdi        # 54d8 <array.0+0x178>
    32e5:	e8 96 ef ff ff       	call   2280 <puts@plt>
    32ea:	b8 00 00 00 00       	mov    $0x0,%eax
    32ef:	e8 f7 f8 ff ff       	call   2beb <secret_phase>
    32f4:	eb ad                	jmp    32a3 <phase_defused+0x54>
    32f6:	48 8d 3d 2b 22 00 00 	lea    0x222b(%rip),%rdi        # 5528 <array.0+0x1c8>
    32fd:	e8 7e ef ff ff       	call   2280 <puts@plt>
    3302:	48 8d 3d cf 21 00 00 	lea    0x21cf(%rip),%rdi        # 54d8 <array.0+0x178>
    3309:	e8 72 ef ff ff       	call   2280 <puts@plt>
    330e:	eb 93                	jmp    32a3 <phase_defused+0x54>
```

> %rip 是指令指针的意思，指向下一条要执行的指令。

发现先调用了 `abracadabra`，若返回值为 $0$，则会打印 `0x22be(%rip)` 和 `0x22fa(%rip)`。不过无所谓，只有返回值不为 $0$ 才会调用 `alohomora`。如果其返回值为 $0$，会到 `32f6` 处，打印 `0x222b(%rip)` 和 `0x21cf(%rip)`，**但只要其返回值不为 $0$，就能够进入 secret phase 了**。提示词如下：

```bash
(gdb) x/s *phase_defused+126+0x21ab
0x555555559478: "Curses, you've found the secret phase!"
(gdb) x/s *phase_defused+138+0x21c7
0x5555555594a0: "But finding it and solving it are quite different..."
(gdb) x/s *phase_defused+150+0x21f3
0x5555555594d8: "And I can't give you a clear answer yet. You need to measure it yourself..."
```

emm，如果 `alohomora` 触发失败就是这样的：

```assembly
(gdb) x/s *phase_defused+174+0x222b
0x555555559528: "Do you think you really trigger the secret phase? Mua ha ha ha!"
(gdb) x/s *phase_defused+186+0x21cf
0x5555555594d8: "And I can't give you a clear answer yet. You need to measure it yourself..."
```

~~什么沟槽的信科特色~~

经过 gdb 调试，进入 `abracadabra` 的时候 `%rdi` 的值仍为 $1$（是当时要传给 `send_msg` 的），因此判断 `abracadabra` 是没有参数的。

我们看看 `abracadabra` 在干什么吧：

```assembly
0000000000002698 <abracadabra>:
    2698:	f3 0f 1e fa          	endbr64
    269c:	48 81 ec 98 00 00 00 	sub    $0x98,%rsp
    26a3:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    26aa:	00 00 
    26ac:	48 89 84 24 88 00 00 	mov    %rax,0x88(%rsp)
    26b3:	00 
    26b4:	31 c0                	xor    %eax,%eax
    26b6:	48 8d 4c 24 0c       	lea    0xc(%rsp),%rcx
    26bb:	48 8d 54 24 08       	lea    0x8(%rsp),%rdx
    26c0:	4c 8d 44 24 10       	lea    0x10(%rsp),%r8
    26c5:	48 8d 35 cf 2a 00 00 	lea    0x2acf(%rip),%rsi        # 519b <_IO_stdin_used+0x19b>
    26cc:	48 8d 3d 75 70 00 00 	lea    0x7075(%rip),%rdi        # 9748 <input_strings+0x168>
    26d3:	e8 88 fc ff ff       	call   2360 <__isoc99_sscanf@plt>
```

发现又有一个 `sscanf`，我们把 `%rdi` 和 `%rsi` 指向的东西都打印出来看看：

```bash
(gdb) x/s $rdi
0x55555555d748 <input_strings+360>:     "5 20"
(gdb) x/s $rsi
0x55555555919b: "%d %d %s"
```

发现是 phase 4 我们的输入。然后把数字分别存到 `%rsp+0x8` 和 `%rsp+0xc` 里面，再把后面的字符串放到 `%rsp+0x10` 里面，再看后面：

```assembly
    26d8:	83 f8 03             	cmp    $0x3,%eax
    26db:	74 20                	je     26fd <abracadabra+0x65>
    26dd:	b8 00 00 00 00       	mov    $0x0,%eax
    26e2:	48 8b 94 24 88 00 00 	mov    0x88(%rsp),%rdx
    26e9:	00 
    26ea:	64 48 2b 14 25 28 00 	sub    %fs:0x28,%rdx
    26f1:	00 00 
    26f3:	75 2b                	jne    2720 <abracadabra+0x88>
    26f5:	48 81 c4 98 00 00 00 	add    $0x98,%rsp
    26fc:	c3                   	ret
    26fd:	48 8d 7c 24 10       	lea    0x10(%rsp),%rdi
    2702:	48 8d 35 a7 2a 00 00 	lea    0x2aa7(%rip),%rsi        # 51b0 <_IO_stdin_used+0x1b0>
    2709:	e8 23 06 00 00       	call   2d31 <strings_not_equal>
    270e:	85 c0                	test   %eax,%eax
    2710:	74 07                	je     2719 <abracadabra+0x81>
    2712:	b8 00 00 00 00       	mov    $0x0,%eax
    2717:	eb c9                	jmp    26e2 <abracadabra+0x4a>
    2719:	b8 01 00 00 00       	mov    $0x1,%eax
    271e:	eb c2                	jmp    26e2 <abracadabra+0x4a>
    2720:	e8 8b fb ff ff       	call   22b0 <__stack_chk_fail@plt>
```

发现必须得读到第三个字符串，然后会与 `2702` 行取到的东西进行比较，我们具体看看是什么：

```bash
(gdb) x/s *abracadabra+113+0x2aa7
0x5555555591b0: "...VeniVidiViciTwoThousandYearsAgo?"
```

于是我们知道了，必须在第 $4$ 个字符串后面跟上 `...VeniVidiViciTwoThousandYearsAgo?` 才会触发 `alohomora`。接下来看看阿拉霍洞开吧！

```assembly
0000000000002725 <alohomora>:
    2725:	f3 0f 1e fa          	endbr64
    2729:	48 81 ec 88 00 00 00 	sub    $0x88,%rsp
    2730:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2737:	00 00 
    2739:	48 89 44 24 78       	mov    %rax,0x78(%rsp)
    273e:	31 c0                	xor    %eax,%eax
    2740:	48 8d 05 11 6f 00 00 	lea    0x6f11(%rip),%rax        # 9658 <input_strings+0x78>
                                    # 取了第二问的答案 0 1 1 2 3 5
    2747:	eb 04                	jmp    274d <alohomora+0x28>
    2749:	48 83 c0 01          	add    $0x1,%rax
    274d:	80 38 00             	cmpb   $0x0,(%rax)
    2750:	75 f7                	jne    2749 <alohomora+0x24>
                                    # 这里的感觉就是，一直把 rax 往后加，加到遇到这个字符串的末尾为止
    
    2752:	48 83 e8 01          	sub    $0x1,%rax # rax 减一，循环从这里开始，rax 一开始指向字符串最后一个元素
    2756:	48 89 e2             	mov    %rsp,%rdx # 把栈指针赋给 rdx
    2759:	eb 0a                	jmp    2765 <alohomora+0x40>
    
    275b:	88 0a                	mov    %cl,(%rdx) # 把 rcx 扔给 rdx指向的位置（在栈里面）
    275d:	48 83 c2 01          	add    $0x1,%rdx  # ++rdx
    2761:	48 83 e8 01          	sub    $0x1,%rax  # rax -= 1
    2765:	0f b6 08             	movzbl (%rax),%ecx # 把 rax 指向的字符丢给 rcx
    2768:	80 f9 20             	cmp    $0x20,%cl   # 将其与 0x20(空格)比较
    276b:	74 0c                	je     2779 <alohomora+0x54>    # 如果等于空格，跳到 2779，循环结束
    276d:	48 8d 35 e4 6e 00 00 	lea    0x6ee4(%rip),%rsi        # 9658 <input_strings+0x78>
                                           # 经检查，还是第二问的答案
    2774:	48 39 f0             	cmp    %rsi,%rax  # 比较 rsi 和 rax
    2777:	75 e2                	jne    275b <alohomora+0x36> # 如果还没到头，跳到 275b
    
    2779:	c6 02 00             	movb   $0x0,(%rdx) # 字符串的末尾
    277c:	48 89 e7             	mov    %rsp,%rdi
    277f:	48 8d 35 52 2a 00 00 	lea    0x2a52(%rip),%rsi        # 51d8 <_IO_stdin_used+0x1d8>
    2786:	e8 a6 05 00 00       	call   2d31 <strings_not_equal>
    278b:	85 c0                	test   %eax,%eax
    278d:	74 1d                	je     27ac <alohomora+0x87>
    278f:	b8 00 00 00 00       	mov    $0x0,%eax
    2794:	48 8b 54 24 78       	mov    0x78(%rsp),%rdx
    2799:	64 48 2b 14 25 28 00 	sub    %fs:0x28,%rdx
    27a0:	00 00 
    27a2:	75 0f                	jne    27b3 <alohomora+0x8e>
    27a4:	48 81 c4 88 00 00 00 	add    $0x88,%rsp
    27ab:	c3                   	ret
    27ac:	b8 01 00 00 00       	mov    $0x1,%eax
    27b1:	eb e1                	jmp    2794 <alohomora+0x6f>
    27b3:	e8 f8 fa ff ff       	call   22b0 <__stack_chk_fail@plt>
```

于是我们可以看懂了，在第二个 phase 的字符串后面还要跟一段东西。应当是要跟上 `...diaSecnOraseaCsuiluJsuiaGtahTwonKUoD` 的 reverse，即 `DoUKnowThatGaiusJuliusCaesarOnceSaid...`，有点意思。

此时，我们便已经能够进入 secret phase。

```assembly
0000000000002beb <secret_phase>:
    2beb:	f3 0f 1e fa          	endbr64
    2bef:	53                   	push   %rbx
    2bf0:	48 83 ec 10          	sub    $0x10,%rsp
    2bf4:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    2bfb:	00 00 
    2bfd:	48 89 44 24 08       	mov    %rax,0x8(%rsp)
    2c02:	31 c0                	xor    %eax,%eax
    2c04:	e8 08 05 00 00       	call   3111 <read_line>
    2c09:	48 89 c7             	mov    %rax,%rdi
    2c0c:	ba 0a 00 00 00       	mov    $0xa,%edx
    2c11:	be 00 00 00 00       	mov    $0x0,%esi
    2c16:	e8 05 f7 ff ff       	call   2320 <strtol@plt>
    2c1b:	48 89 c3             	mov    %rax,%rbx
    2c1e:	48 8d 3d 8b 64 00 00 	lea    0x648b(%rip),%rdi        # 90b0 <n1>
    2c25:	e8 71 ff ff ff       	call   2b9b <get_sum>
    2c2a:	39 d8                	cmp    %ebx,%eax
    2c2c:	75 50                	jne    2c7e <secret_phase+0x93> # 炸弹爆炸
    2c2e:	48 8d 3d 0b 26 00 00 	lea    0x260b(%rip),%rdi        # 5240 <_IO_stdin_used+0x240>
    2c35:	e8 46 f6 ff ff       	call   2280 <puts@plt>
    2c3a:	48 8d 3d 27 26 00 00 	lea    0x2627(%rip),%rdi        # 5268 <_IO_stdin_used+0x268>
    2c41:	e8 3a f6 ff ff       	call   2280 <puts@plt>
    2c46:	48 8d 3d 63 26 00 00 	lea    0x2663(%rip),%rdi        # 52b0 <_IO_stdin_used+0x2b0>
    2c4d:	e8 2e f6 ff ff       	call   2280 <puts@plt>
    2c52:	48 8d 3d 8f 26 00 00 	lea    0x268f(%rip),%rdi        # 52e8 <_IO_stdin_used+0x2e8>
    2c59:	e8 22 f6 ff ff       	call   2280 <puts@plt>
    2c5e:	48 8d 7c 24 04       	lea    0x4(%rsp),%rdi
    2c63:	e8 e7 05 00 00       	call   324f <phase_defused>
    2c68:	48 8b 44 24 08       	mov    0x8(%rsp),%rax
    2c6d:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    2c74:	00 00 
    2c76:	75 0d                	jne    2c85 <secret_phase+0x9a>
    2c78:	48 83 c4 10          	add    $0x10,%rsp
    2c7c:	5b                   	pop    %rbx
    2c7d:	c3                   	ret
    2c7e:	e8 c3 03 00 00       	call   3046 <explode_bomb>
    2c83:	eb a9                	jmp    2c2e <secret_phase+0x43>
    2c85:	e8 26 f6 ff ff       	call   22b0 <__stack_chk_fail@plt>
```

我们可以看出来，secret phase 读入的字符串会过一道 `strtol` 函数变成 `long`，然后调用一个 `get_sum` 并要求其返回的结果与我们输入的数相同。我们且看看 `get_sum` 是怎么个事，嗯其返回 $63$，然后就做完了。？

```
➜  ubuntu@ics-yangty ~/bomb162  > ./bomb
Welcome to Mr.Gin's little bomb. You have 6 phases with
which to blow yourself up. Have a nice day! Mua ha ha ha!
Love is a program that science and logic can never explain.
Phase 1 defused. How about the next one?
0 1 1 2 3 5 DoUKnowThatGaiusJuliusCaesarOnceSaid...
That's number 2. Keep going!
0 1070
Halfway there! Good job!
5 20 ...VeniVidiViciTwoThousandYearsAgo?
So you got that one. Try this one.
M63487
Good work! On to the next...
5 2 6 1 4 3
Curses, you've found the secret phase!
But finding it and solving it are quite different...
And I can't give you a clear answer yet. You need to measure it yourself...
63
Bravo! You've defused the secret stage!
        Mr. Gin finally knew that the students at PKU are not easy to defeat.
        He fled in haste, but you know it is not over...
        "A sleepless malice", uttered you as you caught a whiff of the next trouble...
Congratulations! You've defused the bomb and saved your machine!
Your instructor has been notified and will verify your solution.
```

## 后记

好像这个 secret phase 是简单版本的。重新下载了个炸弹开始拆，前面的就用了 IDA：

### Phase 1

略

### Phase 2

```c
unsigned __int64 __fastcall phase_2(__int64 a1)
{
  int i; // ebx
  _DWORD v3[6]; // [rsp+0h] [rbp-28h] BYREF
  unsigned __int64 v4; // [rsp+18h] [rbp-10h]

  v4 = __readfsqword(0x28u);
  read_six_numbers(a1, v3);
  if ( v3[0] < 0 )
    explode_bomb();
  for ( i = 1; i <= 5; ++i )
  {
    if ( v3[i] != v3[i - 1] + i )
      explode_bomb();
  }
  return v4 - __readfsqword(0x28u);
}
```

### Phase 3

```c
unsigned __int64 __fastcall phase_3(__int64 a1)
{
  char v1; // al
  char v3; // [rsp+Fh] [rbp-19h] BYREF
  int v4; // [rsp+10h] [rbp-18h] BYREF
  int v5; // [rsp+14h] [rbp-14h] BYREF
  unsigned __int64 v6; // [rsp+18h] [rbp-10h]

  v6 = __readfsqword(0x28u);
  if ( (int)__isoc99_sscanf(a1, "%d %c %d", &v4, &v3, &v5) <= 2 )
    explode_bomb();
  switch ( v4 )
  {
    case 0:
      if ( v5 != 505 )
        explode_bomb();
      v1 = 120;
      break;
    case 1:
      if ( v5 != 552 )
        explode_bomb();
      v1 = 102;
      break;
    case 2:
      if ( v5 != 295 )
        explode_bomb();
      v1 = 105;
      break;
    case 3:
      if ( v5 != 422 )
        explode_bomb();
      v1 = 121;
      break;
    case 4:
      if ( v5 != 825 )
        explode_bomb();
      v1 = 121;
      break;
    case 5:
      if ( v5 != 777 )
        explode_bomb();
      v1 = 122;
      break;
    case 6:
      if ( v5 != 866 )
        explode_bomb();
      v1 = 100;
      break;
    case 7:
      if ( v5 != 680 )
        explode_bomb();
      v1 = 99;
      break;
    default:
      explode_bomb();
  }
  if ( v3 != v1 )
    explode_bomb();
  return v6 - __readfsqword(0x28u);
}
```

### Phase 4

```c
unsigned __int64 __fastcall phase_4(__int64 a1)
{
  unsigned int v2; // [rsp+0h] [rbp-18h] BYREF
  int v3; // [rsp+4h] [rbp-14h] BYREF
  unsigned __int64 v4; // [rsp+8h] [rbp-10h]

  v4 = __readfsqword(0x28u);
  if ( (unsigned int)__isoc99_sscanf(a1, "%d %d", &v2, &v3) != 2 || v2 > 0xE )
    explode_bomb();
  if ( (unsigned int)func4(v2, 0LL, 14LL) != 31 || v3 != 31 )
    explode_bomb();
  return v4 - __readfsqword(0x28u);
}
```

那我们看看 `func4` 在干嘛：

```c
__int64 __fastcall func4(__int64 a1, __int64 a2, __int64 a3)
{
  int v3; // ebx

  v3 = a2 + ((int)a3 - (int)a2) / 2;
  if ( v3 > (int)a1 )
  {
    v3 += func4(a1, a2, (unsigned int)(v3 - 1));
  }
  else if ( v3 < (int)a1 )
  {
    v3 += func4(a1, (unsigned int)(v3 + 1), a3);
  }
  return (unsigned int)v3;
}
```

直接写个程序硬跑就可以了：`13 31`

### Phase 5

这个没法直接看出来了。

```c
unsigned __int64 __fastcall phase_5(__int64 a1)
{
  char *v1; // rsi
  __int64 v2; // rcx
  __int64 v3; // rdx
  int v5; // [rsp+0h] [rbp-18h] BYREF
  int v6; // [rsp+4h] [rbp-14h] BYREF
  unsigned __int64 v7; // [rsp+8h] [rbp-10h]

  v7 = __readfsqword(0x28u);
  v1 = "%d %d";
  if ( (int)__isoc99_sscanf(a1, "%d %d", &v5, &v6) <= 1 )
    ((void (__noreturn *)(void))explode_bomb)();
  v5 &= 0xFu;
  v2 = 0LL;
  v3 = 0LL;
  while ( v5 != 15 )
  {
    v3 = (unsigned int)(v3 + 1);
    v1 = (char *)array_0;
    v5 = array_0[v5];
    v2 = (unsigned int)(v5 + v2);
  }
  if ( (_DWORD)v3 != 15 || v6 != (_DWORD)v2 )
    explode_bomb(a1, v1, v3, v2);
  return v7 - __readfsqword(0x28u);
}
```

这个循环里面，有种跳链表的感觉。

```bash
(gdb) x/20dw $rsi
0x555555558380 <array.0>:       10      2       14      7
0x555555558390 <array.0+16>:    8       12      15      11
0x5555555583a0 <array.0+32>:    0       4       1       13
0x5555555583b0 <array.0+48>:    3       9       6       5
```

整理一下就是：

```
0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
10 2  14 7  8  12 15 11 0  4  1  13 3  9  6  5
```

因为 `v3` 一定最后要是 $15$，所以我们把关系整理一下：

```
0 -> 10 -> 1 -> 2 -> 14 -> 6 -> 15 -> 5 -> 12 -> 3 -> 7 -> 11 -> 13 -> 9 -> 4 -> 8 -> 0
```

重新排一下：

```
5 -> 12 -> 3 -> 7 -> 11 -> 13 -> 9 -> 4 -> 8 -> 0 -> 10 -> 1 -> 2 -> 14 -> 6 -> 15 
```

最后可以得到答案 `5 115`。

### Phase 6

和我的是一样的。

### Secret Phase

```assembly
0000000000001cb3 <secret_phase>:
    1cb3:	f3 0f 1e fa          	endbr64
    1cb7:	55                   	push   %rbp
    1cb8:	53                   	push   %rbx
    1cb9:	48 83 ec 18          	sub    $0x18,%rsp
    1cbd:	64 48 8b 04 25 28 00 	mov    %fs:0x28,%rax
    1cc4:	00 00 
    1cc6:	48 89 44 24 08       	mov    %rax,0x8(%rsp)
    1ccb:	31 c0                	xor    %eax,%eax
    1ccd:	e8 2b 05 00 00       	call   21fd <read_line>
    1cd2:	48 89 c5             	mov    %rax,%rbp
    1cd5:	bb 00 00 00 00       	mov    $0x0,%ebx
    1cda:	eb 03                	jmp    1cdf <secret_phase+0x2c>
    1cdc:	83 c3 01             	add    $0x1,%ebx
    1cdf:	48 63 c3             	movslq %ebx,%rax
    1ce2:	80 7c 05 00 00       	cmpb   $0x0,0x0(%rbp,%rax,1)
    1ce7:	74 0c                	je     1cf5 <secret_phase+0x42>
    1ce9:	83 fb 18             	cmp    $0x18,%ebx
    1cec:	7e ee                	jle    1cdc <secret_phase+0x29>
    1cee:	e8 3f 04 00 00       	call   2132 <explode_bomb>
    1cf3:	eb e7                	jmp    1cdc <secret_phase+0x29>
    1cf5:	48 89 ef             	mov    %rbp,%rdi
    1cf8:	e8 6c ff ff ff       	call   1c69 <check_synchronizing_sequence>
    1cfd:	85 c0                	test   %eax,%eax
    1cff:	75 69                	jne    1d6a <secret_phase+0xb7>
    1d01:	48 8d 3d 40 25 00 00 	lea    0x2540(%rip),%rdi        # 4248 <_IO_stdin_used+0x248>
    1d08:	e8 63 f5 ff ff       	call   1270 <puts@plt>
    1d0d:	48 8d 3d 6c 25 00 00 	lea    0x256c(%rip),%rdi        # 4280 <_IO_stdin_used+0x280>
    1d14:	e8 57 f5 ff ff       	call   1270 <puts@plt>
    1d19:	48 8d 3d a0 25 00 00 	lea    0x25a0(%rip),%rdi        # 42c0 <_IO_stdin_used+0x2c0>
    1d20:	e8 4b f5 ff ff       	call   1270 <puts@plt>
    1d25:	48 8d 3d cc 25 00 00 	lea    0x25cc(%rip),%rdi        # 42f8 <_IO_stdin_used+0x2f8>
    1d2c:	e8 3f f5 ff ff       	call   1270 <puts@plt>
    1d31:	48 8d 3d 00 26 00 00 	lea    0x2600(%rip),%rdi        # 4338 <_IO_stdin_used+0x338>
    1d38:	e8 33 f5 ff ff       	call   1270 <puts@plt>
    1d3d:	48 8d 3d 69 24 00 00 	lea    0x2469(%rip),%rdi        # 41ad <_IO_stdin_used+0x1ad>
    1d44:	e8 27 f5 ff ff       	call   1270 <puts@plt>
    1d49:	48 8d 7c 24 04       	lea    0x4(%rsp),%rdi
    1d4e:	e8 e8 05 00 00       	call   233b <phase_defused>
    1d53:	48 8b 44 24 08       	mov    0x8(%rsp),%rax
    1d58:	64 48 2b 04 25 28 00 	sub    %fs:0x28,%rax
    1d5f:	00 00 
    1d61:	75 0e                	jne    1d71 <secret_phase+0xbe>
    1d63:	48 83 c4 18          	add    $0x18,%rsp
    1d67:	5b                   	pop    %rbx
    1d68:	5d                   	pop    %rbp
    1d69:	c3                   	ret
    1d6a:	e8 c3 03 00 00       	call   2132 <explode_bomb>
    1d6f:	eb 90                	jmp    1d01 <secret_phase+0x4e>
    1d71:	e8 2a f5 ff ff       	call   12a0 <__stack_chk_fail@plt>
```

前面的地方是在检测串长，要求串长不能超过 $25$。然后调用一个 `check_synchronizing_sequence`：

```assembly
0000000000001c69 <check_synchronizing_sequence>:
    1c69:	f3 0f 1e fa          	endbr64
    1c6d:	41 54                	push   %r12
    1c6f:	55                   	push   %rbp
    1c70:	53                   	push   %rbx
    1c71:	48 89 fd             	mov    %rdi,%rbp
    1c74:	48 89 fe             	mov    %rdi,%rsi
    1c77:	bf 00 00 00 00       	mov    $0x0,%edi
    1c7c:	e8 91 ff ff ff       	call   1c12 <emulate_fsm>
    1c81:	41 89 c4             	mov    %eax,%r12d
    1c84:	bb 01 00 00 00       	mov    $0x1,%ebx
    1c89:	83 fb 06             	cmp    $0x6,%ebx
    1c8c:	7f 14                	jg     1ca2 <check_synchronizing_sequence+0x39>
    1c8e:	48 89 ee             	mov    %rbp,%rsi
    1c91:	89 df                	mov    %ebx,%edi
    1c93:	e8 7a ff ff ff       	call   1c12 <emulate_fsm>
    1c98:	44 39 e0             	cmp    %r12d,%eax
    1c9b:	75 0f                	jne    1cac <check_synchronizing_sequence+0x43>
    1c9d:	83 c3 01             	add    $0x1,%ebx
    1ca0:	eb e7                	jmp    1c89 <check_synchronizing_sequence+0x20>
    1ca2:	b8 00 00 00 00       	mov    $0x0,%eax
    1ca7:	5b                   	pop    %rbx
    1ca8:	5d                   	pop    %rbp
    1ca9:	41 5c                	pop    %r12
    1cab:	c3                   	ret
    1cac:	b8 ff ff ff ff       	mov    $0xffffffff,%eax
    1cb1:	eb f4                	jmp    1ca7 <check_synchronizing_sequence+0x3e>
```

又发现有个叫做 `emulate_fsm` 的东西。

```assembly
0000000000001c12 <emulate_fsm>:
    1c12:	f3 0f 1e fa          	endbr64
    1c16:	55                   	push   %rbp
    1c17:	53                   	push   %rbx
    1c18:	48 83 ec 08          	sub    $0x8,%rsp
    1c1c:	89 fd                	mov    %edi,%ebp
    1c1e:	48 89 f3             	mov    %rsi,%rbx
    1c21:	eb 28                	jmp    1c4b <emulate_fsm+0x39>
    1c23:	0f be 03             	movsbl (%rbx),%eax
    1c26:	83 e8 30             	sub    $0x30,%eax
    1c29:	48 63 ed             	movslq %ebp,%rbp
    1c2c:	48 98                	cltq
    1c2e:	48 8d 14 c5 00 00 00 	lea    0x0(,%rax,8),%rdx
    1c35:	00 
    1c36:	48 29 c2             	sub    %rax,%rdx
    1c39:	48 8d 04 2a          	lea    (%rdx,%rbp,1),%rax
    1c3d:	48 8d 15 7c 27 00 00 	lea    0x277c(%rip),%rdx        # 43c0 <transition_table>
    1c44:	8b 2c 82             	mov    (%rdx,%rax,4),%ebp
    1c47:	48 83 c3 01          	add    $0x1,%rbx
    1c4b:	0f b6 03             	movzbl (%rbx),%eax
    1c4e:	84 c0                	test   %al,%al
    1c50:	74 0e                	je     1c60 <emulate_fsm+0x4e>
    1c52:	83 e8 30             	sub    $0x30,%eax
    1c55:	3c 01                	cmp    $0x1,%al
    1c57:	76 ca                	jbe    1c23 <emulate_fsm+0x11>
    1c59:	e8 d4 04 00 00       	call   2132 <explode_bomb>
    1c5e:	eb c3                	jmp    1c23 <emulate_fsm+0x11>
    1c60:	89 e8                	mov    %ebp,%eax
    1c62:	48 83 c4 08          	add    $0x8,%rsp
    1c66:	5b                   	pop    %rbx
    1c67:	5d                   	pop    %rbp
    1c68:	c3                   	ret
```

很有趣。反编译出来长这样的：

```c
__int64 __fastcall emulate_fsm(unsigned int a1, _BYTE *a2)
{
  while ( *a2 )
  {
    if ( (unsigned __int8)(*a2 - 48) > 1u )
      explode_bomb();
    a1 = transition_table[7 * (char)*a2++ - 336 + a1];
  }
  return a1;
}
```

把 `transition_table` 弄出来：

```bash
(gdb) x/20dw 0x5555555583c0
0x5555555583c0 <transition_table>:      6       1       3       0
0x5555555583d0 <transition_table+16>:   2       4       5       0
0x5555555583e0 <transition_table+32>:   2       1       3       1
0x5555555583f0 <transition_table+48>:   5       6       2032168787      1948284271
0x555555558400: 1802398056      1970239776      1851876128      1869902624
```

所以 transition_table 就是 `6, 1, 3, 0, 2, 4, 5, 0, 2, 1, 3, 1, 5, 6`。

这便指引我们，需要枚举一个长度小于等于 $25$ 的 0-1 序列，写个脚本判断吧：

```c
#include <stdio.h>
#include <assert.h>

int transition_table[] = {6, 1, 3, 0, 2, 4, 5, 0, 2, 1, 3, 1, 5, 6};
char str[30];

long long emulate_fsm(unsigned int a1, char *a2)
{
  while ( *a2 )
  {
    assert(*a2 == '0' || *a2 == '1');
    a1 = transition_table[7 * (char)*a2++ - 336 + a1];
  }
  return a1;
}

long long check_synchronizing_sequence(char* s)
{
  int v1; // r12d
  int i; // ebx

  v1 = emulate_fsm(0LL, s);
  for ( i = 1; ; ++i )
  {
    if ( i > 6 )
      return 0LL;
    if ( (unsigned int)emulate_fsm((unsigned int)i, s) != v1 )
      break;
  }
  return 0xFFFFFFFFLL;
}

void gen_str(int len, int idx) {
  if (len == idx) {
    long long res = check_synchronizing_sequence(str);
    if (!res) {
      printf("found answer %s\n", str);
    }
    return;
  }
  str[idx] = '1';
  gen_str(len, idx + 1);
  str[idx] = '0';
  gen_str(len, idx + 1);
  return;
}

int main() {
  for (int len = 1; len <= 25; ++len) {
    gen_str(len, 0);
  }
}
```

跑出来结果了。搞定。

```
Fear of death is worse than death itself.
0 1 3 6 10 15 DoUKnowThatGaiusJuliusCaesarOnceSaid...
0 x 505
13 31 ...VeniVidiViciTwoThousandYearsAgo?
5 115
2 3 4 5 1 6
1000100101001000101000001

```

