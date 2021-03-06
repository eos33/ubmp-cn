# 后记

非常感谢您阅读本书！

目前为止，我们已经讲述了如何在UEFI下进行裸机编程。如果你能读完本书后感到“啊！原来是这么回事”，将是我莫大的荣幸。

UEFI十分精妙，它具有许多内建的功能，也可以做很多事情。当我阅读UEFI标准文档的目录时，我惊叹道“这些居然可以在固件层面上做到”[^1]。并且，即便在软件上进行最底层[^2]操作时，也不需要考虑到内存映射这些问题，这也是很令人惊讶的。

就像我在引言中说的，本书的理念是解答“通过调用UEFI内置的功能来编写一个类似于操作系统的程序可行吗？”这个想法。事实证明了，这是完全可行的。UEFI提供了许多功能，而其中，它的文件系统功能是非常强大的。因此，在我看来，如果你想从零编写一个操作系统，从UEFI开始是很合适的。

最后，我想用一首二进制短歌[^3]来结束本书。其汇编代码如图8.1所示，生成的程序反汇编如图8.2所示，示例代码的目录为`sample_tanka` (仅限日文版)。 [^4]

```asm
#define CONOUT_ADDR 0xa3819590
#define OUTPUTSTRING_ADDR 0xa387e1b8

    .text
    .globl      efi_main
efi_main:
    /* 输出"msg"字符串 */
    /* 将OutputString的第一个参数ConOut放入RCX寄存器 */
    movq        $CONOUT_ADDR, %rcx
    /* 将OutputString函数的地址放入RAX寄存器 */
    movq        $OUTPUTSTRING_ADDR, %rax
    /* 将要输出的字符串"msg"的地址(第二个参数)放入RDX寄存器 */
    leaq        msg, %rdx
    /* 调用OutputString函数 */
    callq       *%rax

    /* 无限循环 */
    jmp     .

    .data
msg:
    /* "ABCD" */
    .ascii      "A\0B\0C\0D\0\0\0"
    /* "すごーい" */
    /*.ascii        "Y0T0\3740D0\0\0"*/
    /* ボス */
    /*.ascii        "\326\0\0\0"*/
```

图8.1: 短歌的汇编代码[^5]

```asm
0000000000401000 <efi_main>:
  401000:       48 b9 90 95 81 a3 00    movabs $0xa3819590,%rcx
  401007:       00 00 00
  40100a:       48 b8 b8 e1 87 a3 00    movabs $0xa387e1b8,%rax
  401011:       00 00 00
  401014:       48 8d 14 25 00 20 40    lea    0x402000,%rdx
  40101b:       00
  40101c:       ff d0                   callq  *%rax
  40101e:       eb fe                   jmp    40101e <efi_main+0x1e>
```

图8.2: 对生成的程序进行反汇编的结果

这里`.text`段占用32字节，多了1字节。

再次感谢您阅读本书！


[^1]: 拥有内建的内存分配器对我而言是不可思议的。

[^2]: 严格意义上讲，UEFI并不是最底层的，其下还有硬件的固件。

[^3]: 译者注：短歌是一种日本传统诗歌，有5句31个音节，以五・七・五・七・七的形式排列

[^4]: 译者注：中文版这里不提供示例代码的原因是UEFI不保证各个协议及函数的地址是一个恒定不变的值，在代码中写死的地址的写法是不可取的。这里我仍旧保留并翻译这些内容的原因是为了说明用汇编编写UEFI应用程序的可能性

[^5]: 译者注：x86_64下的UEFI遵循[Microsoft x64 calling convention](https://en.wikipedia.org/wiki/X86_calling_conventions#Microsoft_x64_calling_convention)
