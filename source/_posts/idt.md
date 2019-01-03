---
title: 中断描述表(idt)和全局描述符表(GDT)
date: 2016-01-02 13:13:54
tags:
    -操作系统
---
##GDT 的结构
![](/blog_blog_imagesgdt.png)
```
/* segment descriptors */
struct segdesc {
    unsigned sd_lim_15_0 : 16;        // low bits of segment limit
    unsigned sd_base_15_0 : 16;        // low bits of segment base address
    unsigned sd_base_23_16 : 8;        // middle bits of segment base address
    unsigned sd_type : 4;            // segment type (see STS_ constants)
    unsigned sd_s : 1;                // 0 = system, 1 = application
    unsigned sd_dpl : 2;            // descriptor Privilege Level
    unsigned sd_p : 1;                // present
    unsigned sd_lim_19_16 : 4;        // high bits of segment limit
    unsigned sd_avl : 1;            // unused (available for software use)
    unsigned sd_rsv1 : 1;            // reserved
    unsigned sd_db : 1;                // 0 = 16-bit segment, 1 = 32-bit segment
    unsigned sd_g : 1;                // granularity: limit scaled by 4K when set
    unsigned sd_base_31_24 : 8;        // high bits of segment base address
};
```
组成：
1. 32 位的段基址
2. 20 位的段界限
3.  1  位的D/B Flag：说明使用16位/32位的段。为1即可。
4.  1  位的G(Granularity,粒度)：为 1 ,段的大小以 4 KB 为单位。为0，段的大小为1byte。
5.  1  位的L：1为64位系统。0为32位。
6.  1  位的P(segment-Present,段占用?) ： 用于标志段是否在内存中。
7.  2  位的DPL:DPL(Descriptor Privilege Level)域标志着段的特权级，0为特权级，3为用户。
8.  1  位的S:S(descriptor type) flag 标志着该段是否系统段:置为 0 代表该段是系统段;置为 1 代表该段是代码段或者数据段。
9.  4  位的Type:设置数据／代码段时的读写等权限。
10.  1位的AVL:保留给操作系统软件使用的位。
共64位。
```
#define SEG(type, base, lim, dpl)                        \
    (struct segdesc){                                    \
        ((lim) >> 12) & 0xffff, (base) & 0xffff,        \
        ((base) >> 16) & 0xff, type, 1, dpl, 1,            \
        (unsigned)(lim) >> 28, 0, 0, 1, 1,                \
        (unsigned) (base) >> 24                            \
    }
    
    /* Application segment type bits */
#define STA_X       0x8     // Executable segment
#define STA_E       0x4     // Expand down (non-executable segments)
#define STA_C       0x4     // Conforming code segment (executable only)
#define STA_W       0x2     // Writeable (non-executable segments)
#define STA_R       0x2     // Readable (executable segments)
#define STA_A       0x1     // Accessed
  ```
  
## 初始化GDT


```
static struct segdesc gdt[] = {
    SEG_NULL,
    [SEG_KTEXT] = SEG(STA_X | STA_R, 0x0, 0xFFFFFFFF, DPL_KERNEL),
    [SEG_KDATA] = SEG(STA_W, 0x0, 0xFFFFFFFF, DPL_KERNEL),
    [SEG_UTEXT] = SEG(STA_X | STA_R, 0x0, 0xFFFFFFFF, DPL_USER),
    [SEG_UDATA] = SEG(STA_W, 0x0, 0xFFFFFFFF, DPL_USER),
    [SEG_TSS]    = SEG_NULL,
};

static struct pseudodesc gdt_pd = {
    sizeof(gdt) - 1, (uint32_t)gdt
};
lgdt(&gdt_pd);
```
系统启动时cs为8H,ds为10H.

gdt[x]描述了该段的工作方式。其下标x指名当cs/ds为x<<3时，该描述符生效。


##IDT的结构
![](/blog_blog_images0_12833186831ecn.gif)
IDT是一个最大为256项的表，每个表项为8字节。称为中断门。CPU通过IDT.base+n*8来寻找门。

```
/* Gate descriptors for interrupts and traps */
struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
};
```
组成：（陷阱门、中断门）
1. 32 位中断地址： 中断服务程序的地址
2. 16 位段选择地址：中断服务程序的段地址
3. 4  位类型：    STS_{TG,IG32,TG32}
4. 2  位的DPL:DPL(Descriptor Privilege Level)域标志着中断的特权级，0为特权级，3为用户。

```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
##初始化
```
static struct gatedesc idt[256] = {{0}};

static struct pseudodesc idt_pd = {
    sizeof(idt) - 1, (uintptr_t)idt
};
while ( ++i < 256 ) {
    SETGATE(idt[i],1,GD_KTEXT,__vectors[i],DPL_KERNEL);
}
SETGATE(idt[T_SWITCH_TOK], 0, GD_KTEXT, __vectors[T_SWITCH_TOK], DPL_USER);

lidt(&idt_pd);
    ```