# Writing a Compiler Using LLVM
This tutorial will explore the implementation of [Cool](https://dl.acm.org/doi/10.1145/381841.381847) programming language using LLVM. Cool source code will be turned into executable machine code. If you are new to compiler 
constructions there are few text books and references (free) in the Reference section. 

The topics covered are in this tutorial are:
* Introduction to Cool
* Introduction to LLVM
* Creating the compiler frontend for Cool using ANTLR
* Using LLVM libraries to generate machine codes for Cool
* Generating the debug information for source level debugging for Cool programs

[LLVM]() implements large number of optimizations that produce better, fast machine codes. The source code for this tutorial (cool-llvm) is available at https://github.com/rajikak/cool-llvm.

# Introduction to Cool
`Cool` (stands for Classroom Object Oriented Language) is programming langauge designed by [Prof. Alexander Aiken](http://theory.stanford.edu/~aiken/) for use in an undergraduate compiler course project. 
So it can be implemented within a short period of time, but also rich enough to understand most aspects of an engineering a compiler. `Cool`'s features includes:
* Object orientation
* Abstractions 
* Re-use (inheritance)
* Memory management

## Cool Syntax 
* `Cool` source files have the extension `.cl` 
* Every `Cool` program has a class called `Main` 
* Every class declaration is terminated by `;`
* Every `Main` class should have a no argument `main()` method. That's where the program execution starts
* Every method declaration is terminated by `;`
* Every method signature is terminated by `:`
* See the full syntax list in the [Cool's manual](./assets/cool-manual.pdf)

Here's a hello world `Cool` program (`hello-world.cl`): 
```
-- This is a Cool hello world program
class Main inherits IO {
	main(): Object {
		out_string("hello, world\n")
	};
};
```

Compile and run the program using `cool` compiler:
```
$ cool hello-world.cl
hello, world
```

Here's another `Cool` program with a syntax error(`syntax-error.cl`), that just returns integer 1:
```
class Main {
    main() {
        1        
    };
};
```

Compile the program:
```
$ cool syntax-error.cl
'syntax-error.cl', line 3: syntax error at or near '{'
```

Fix the syntax error, by adding a `: Int`:
```
class Main {
    main(): Int {
        1        
    };
};
```

Compile the program again:
```
$ cool syntax-error.cl
```

Here's a `Cool` program to calculate factorial (`fact.cl`):
```
class Main inherit A2i{
	main(): Object {
		(new IO).out_string(a2i(fact(new IO().in_string())).contact("\n"));
	};

	fact(i: Int): Int {
		if (i == 0) then 1 else i * fact(i - 1) fi
	};

	(* factorial using iterations *)
	fact1(i: Int): Int {
		let fact: Int <- 1 in {
			while (not (i = 0) loop
				{
				   	fact <- fact * i;
					i <- i - 1;
				};
			pool;
			fact;
		};
	};
};
```


# LLVM
LLVM is a compiler [infrastructure](https://llvm.org/pubs/2008-10-04-ACAT-LLVM-Intro.pdf). LLVM can be used to implement 
compilers for other languages. This is possible because LLVM's modular architecture. LLVM is implemented as set of re-usable 
compiler components (for e.g. a re-usable x86 code generator so that a new compiler can re-use that code generator) that can re-used in other compiler implementations. This is the main benefit of LLVM compared to a monolithic 
compiler project like [gcc](https://gcc.gnu.org). 

## Installing 



# References
* [CS 143 Compilers](https://web.stanford.edu/class/cs143/)
* [Compilers (edX)](https://learning.edx.org/course/course-v1:StanfordOnline+SOE.YCSCS1+2T2020/home)
* [Engineering A Compiler](https://www.amazon.com/Engineering-Compiler-Keith-D-Cooper/dp/0128154128/ref=sr_1_1?keywords=engineering+a+compiler&qid=1666828042&qu=eyJxc2MiOiIxLjcwIiwicXNhIjoiMS40MyIsInFzcCI6IjEuNjkifQ%3D%3D&s=books&sprefix=Engineegin+a+compiler%2Cstripbooks%2C83&sr=1-1)
* [Writing An Interpreter In Go](https://interpreterbook.com/)
* [Writing A Compiler In Go](https://compilerbook.com/)
* [Cool manual](./assets/cool-manual.pdf)([Online](https://web.stanford.edu/class/cs143/materials/cool-manual.pdf))
* [LLVM](http://www.aosabook.org/en/llvm.html)
* https://tomassetti.me/a-tutorial-on-how-to-write-a-compiler-using-llvm/
* https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/index.html
* [Lexical Scanning in Go - Rob Pike](https://www.youtube.com/watch?v=HxaD_trXwRE)
