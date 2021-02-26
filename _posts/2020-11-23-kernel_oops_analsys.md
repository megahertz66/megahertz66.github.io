---
title: MIPS 架构 Kernel oops 分析(待续...)
date: 2020-11-23
categories:
- Kernel
tags:
- Debug
---




### How to get the thread information struct

在实际的代码中，打印oops的函数是比较简单的。其实际的意图也是打印出当前发生问题时刻的cpu状态 (各个寄存器的值)。
比较关键的就是 pt_regs 结构的信息获取，linux 在 `linux-3.4.11/arch/mips/include/asm/ptrace.h` 文件中将MIPS的各个寄存器及其他信息抽象成了pt_regs结构体。


```c
/*
 * This struct defines the way the registers are stored on the stack during a
 * system call/exception. As usual the registers k0/k1 aren't being saved.
 */
struct pt_regs {
#ifdef CONFIG_32BIT
	/* Pad bytes for argument save space on the stack. */
	unsigned long pad0[6];
#endif

	/* Saved main processor registers. */
	unsigned long regs[32];

	/* Saved special registers. */
	unsigned long cp0_status;
	unsigned long hi;
	unsigned long lo;
#ifdef CONFIG_CPU_HAS_SMARTMIPS
	unsigned long acx;
#endif
	unsigned long cp0_badvaddr;
	unsigned long cp0_cause;
	unsigned long cp0_epc;
#ifdef CONFIG_MIPS_MT_SMTC
	unsigned long cp0_tcstatus;
#endif /* CONFIG_MIPS_MT_SMTC */
#ifdef CONFIG_CPU_CAVIUM_OCTEON
	unsigned long long mpl[3];        /* MTM{0,1,2} */
	unsigned long long mtp[3];        /* MTP{0,1,2} */
#endif
} __attribute__ ((aligned (8)));

/* Arbitrarily choose the same ptrace numbers as used by the Sparc code. */
#define PTRACE_GETREGS		12
#define PTRACE_SETREGS		13
#define PTRACE_GETFPREGS		14
#define PTRACE_SETFPREGS		15
/* #define PTRACE_GETFPXREGS		18 */
/* #define PTRACE_SETFPXREGS		19 */

#define PTRACE_OLDSETOPTIONS	21

#define PTRACE_GET_THREAD_AREA	25
#define PTRACE_SET_THREAD_AREA	26

/* Calls to trace a 64bit program from a 32bit program.  */
#define PTRACE_PEEKTEXT_3264	0xc0
#define PTRACE_PEEKDATA_3264	0xc1
#define PTRACE_POKETEXT_3264	0xc2
#define PTRACE_POKEDATA_3264	0xc3
#define PTRACE_GET_THREAD_AREA_3264	0xc4

/* Read and write watchpoint registers.  */
enum pt_watch_style {
	pt_watch_style_mips32,
	pt_watch_style_mips64
};
```


其中的关键代码。

`struct pt_regs *regs = get_irq_regs();`


It define in linux-3.4.11/arch/mips/include/asm/irq_regs.h.

```c
static inline struct pt_regs *get_irq_regs(void)
{
	return current_thread_info()->regs;
}

#define current_thread_info()  __current_thread_info

/* How to get the thread information struct from C.  */
register struct thread_info *__current_thread_info __asm__("$28");
#define current_thread_info()  __current_thread_info
```

**\$28	$gp	全局指针(Global Pointer)**
	

Another case is get information through the arm architechture.

```c
/*
 * how to get the thread information struct from C
 */
static inline struct thread_info *current_thread_info(void) __attribute_const__;

static inline struct thread_info *current_thread_info(void)
{
	register unsigned long sp asm ("sp");
	return (struct thread_info *)(sp & ~(THREAD_SIZE - 1));
}

```

The function of print the log on screen.
`void __noreturn die(const char *str, struct pt_regs *regs)` 

Now we ignore the other code nad only focus on the panic function.




### 反汇编

`objdump -D -S a.o > a.s`


### MIPS Architecture exception

[MIPS execption Guide](chrome-extension://ikhdkkncnoglghljlkmcimlnlhkeamad/pdf-viewer/web/viewer.html?file=http%3A%2F%2Fwww.cs.iit.edu%2F~virgil%2Fcs470%2FLabs%2FLab7.pdf)




### The MIPS registers 

![MIPS registers](/picture/MIPS_registers.png)