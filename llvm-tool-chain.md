# LLVM
LLVM is a compiler [infrastructure](https://llvm.org/pubs/2008-10-04-ACAT-LLVM-Intro.pdf). LLVM can be used to implement
compilers for other languages. This is possible because how LLVM is designed to be re-used. LLVM is implemented as a set of re-usable
compiler components (for e.g. a re-usable X86 code generator so that a new compiler can re-use that code generator). This is the main benefit of LLVM compared to a monolithic
compiler project like [gcc](https://gcc.gnu.org).

![LLVM Architecture](./figures/LLVM.svg)

Figure1: LLVM's modular architecture

LLVM can be subdivided into three major components: the frontend, the intermediate representation (IR) and the backend. The
frontend is responsible for translating input source code (for e.g. C/C++, Rust or Cool programs) into LLVM IR. We will look how to write a
LLVM frontend as part of `Cool` compiler implementation in this tutorial. The IR is the core of the LLVM. The IR provides the most
significant benefit of LLVM for compiler implementers. The backend component is responsible for translating IR to a specific
hardware such as the Intel X86 or the ARM architecture. IR acts as the input to the backend and thereby allow compiler writers to
just to focus on writing a frontend for their language in order to produce a production ready compiler.

## LLVM IR

### Installing
In order to implement a compiler with LLVM, we need to install LLVM libraries https://llvm.org/docs/GettingStarted.html#getting-started-with-llvm.
I am using below steps for compling and installing LLVM on a Linux system:

Install cmake and add the bin path into `PATH`:
```
$ wget https://github.com/Kitware/CMake/releases/download/v3.24.2/cmake-3.24.2-linux-x86_64.tar.gz
$ tar -xvf cmake-3.24.2-linux-x86_64.tar.gz
```


```
$ yum install -y git gcc gcc-c++ python zlib make
$ pwd
/home/ec2-user/
$ git clone https://github.com/llvm/llvm-project.git
$ mkdir llvm-project/build
$ cd llvm-project/build
$ cmake -G 'Unix Makefiles' \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/home/ec2-user/llvm-project/install \
        -DLLVM_TARGETS_TO_BUILD='X86' \
        -DLLVM_ENABLE_PROJECTS='clang;lldb;lld;mlir;clang-tools-extra;compiler-rt' \
        ../llvm 
$ make;make install
```

### Using LLVM Tool Chain
Let's start with an example to see how to use the LLVM tool chain. Below is a simple C program.

```
// hello.c
#include <stdio.h>

int main() {
    printf("hello world\n");
}
```

Compile this using `clang` and generate the [LLVM IR](https://llvm.org/docs/LangRef.html).
```
$ clang -S -emit-llvm -O3 hello.c
```

This produces the human-readable LLVM IR representation of the program.
```
$ cat hello.ll
@str = private unnamed_addr constant [12 x i8] c"hello world\00", align 1

; Function Attrs: nofree nounwind uwtable
define dso_local i32 @main() local_unnamed_addr #0 {
  %1 = tail call i32 @puts(ptr nonnull dereferenceable(1) @str)
  ret i32 0
}

; Function Attrs: nofree nounwind
declare noundef i32 @puts(ptr nocapture noundef readonly) local_unnamed_addr #1
```

Use LLVM assembler to convert the LLVM IR to LLVM bitcode. Bit code is a binary representation of LLVM IR.
```
$ llvm-as hello.ll -o hello.bc
```

`hexdump` can be used to view the binary LLVM bitcode file.
```
$ hexdump -c hello.bc
0000000   B   C 300 336   5 024  \0  \0 005  \0  \0  \0   b  \f   0   $
0000010   M   Y 276   f 255 373 264   O 033 310   $   D 001   2 005  \0
0000020   !  \f  \0  \0 374 001  \0  \0  \v 002   !  \0 002  \0  \0  \0
0000030 026  \0  \0  \0  \a 201   # 221   A 310 004   I 006 020   2   9
0000040 222 001 204  \f   % 005  \b 031 036 004 213   b 200 020   E 002
0000050   B 222  \v   B 204 020   2 024   8  \b 030   K  \n   2   B 210
0000060   H   p 304   !   #   D 022 207 214 020   A 222 002   d 310  \b
0000070 261 024       C   F 210     311 001   2   B 204 030   *   (   *
0000080 220   1   | 260   \ 221     304 310  \0  \0  \0 211      \0  \0
...
```

LLVM disassembler can be used to convert LLVM bitcode file back to LLVM IR.
```
$ llvm-dis hello.bc -o hello.ll
```

LLVM linker can be used to link two or more LLVM bitcode files to output to a single bitcode file.
```
$ llvm-link first.bc second.bc -o combined.bc 
```

LLVM IR compiler `llc` can be used to compile LLVM bitcode to target assembly.
```
$ llc hello.bc -o hello.s
```
```
$ cat hello.s
	.text
	.file	"hello.c"
	.globl	main                            # -- Begin function main
	.p2align	4, 0x90
	.type	main,@function
main:                                   # @main
	.cfi_startproc
# %bb.0:
	pushq	%rax
	.cfi_def_cfa_offset 16
	movl	$.Lstr, %edi
	callq	puts@PLT
	xorl	%eax, %eax
	popq	%rcx
	.cfi_def_cfa_offset 8
	retq
.Lfunc_end0:
	.size	main, .Lfunc_end0-main
	.cfi_endproc
                                        # -- End function
	.type	.Lstr,@object                   # @str
	.section	.rodata.str1.1,"aMS",@progbits,1
.Lstr:
	.asciz	"hello world"
	.size	.Lstr, 12

	.ident	"clang version 16.0.0 (https://github.com/llvm/llvm-project.git 22a4b336a6ff7db1ab5b5b050c452259a3710dae)"
	.section	".note.GNU-stack","",@progbits  
```

LLVM `lli` can be used to execute LLVM bitcode.
```
$ lli hello.bc 
hello world
```      

LLVM optimizer (`opt`) support large number of optimization.
```
$ opt -mem2reg hello.bc -o hello.opt.bc
$ llc hello.opt.bc -o hello.opt.s 
```
```
$ cat hello.opt.s
cat hello.opt.s 
	.text
	.file	"hello.c"
	.globl	main                            # -- Begin function main
	.p2align	4, 0x90
	.type	main,@function
main:                                   # @main
	.cfi_startproc
# %bb.0:
	pushq	%rax
	.cfi_def_cfa_offset 16
	movl	$.Lstr, %edi
	callq	puts@PLT
	xorl	%eax, %eax
	popq	%rcx
	.cfi_def_cfa_offset 8
	retq
.Lfunc_end0:
	.size	main, .Lfunc_end0-main
	.cfi_endproc
                                        # -- End function
	.type	.Lstr,@object                   # @str
	.section	.rodata.str1.1,"aMS",@progbits,1
.Lstr:
	.asciz	"hello world"
	.size	.Lstr, 12

	.ident	"clang version 16.0.0 (https://github.com/llvm/llvm-project.git 22a4b336a6ff7db1ab5b5b050c452259a3710dae)"
	.section	".note.GNU-stack","",@progbits
```

# References
* [LLVM](http://www.aosabook.org/en/llvm.html)
* [Getting Started with the LLVM System](https://llvm.org/docs/GettingStarted.html)
* [Creating an LLVM Project](https://llvm.org/docs/Projects.html)
* [Mapping High Level Constructs to LLVM IR](https://mapping-high-level-constructs-to-llvm-ir.readthedocs.io/en/latest/a-quick-primer/index.html)
* https://tomassetti.me/a-tutorial-on-how-to-write-a-compiler-using-llvm/
* https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/index.html
* [Compilers and IRs: LLVM IR, SPIR-V, and MLIR](https://www.lei.chat/posts/compilers-and-irs-llvm-ir-spirv-and-mlir/)
* [Superoptimizing LLVM](https://www.youtube.com/watch?v=Ux0YnVEaI6A)
* [A Complete Guide to LLVM for Programming Language Creators](https://mukulrathi.com/create-your-own-programming-language/llvm-ir-cpp-api-tutorial/)
* [How a Go Program Compiles down to Machine Code](https://getstream.io/blog/how-a-go-program-compiles-down-to-machine-code/)
* [Mapping High Level Constructs to LLVM IR](https://mapping-high-level-constructs-to-llvm-ir.readthedocs.io/en/latest/README.html)
