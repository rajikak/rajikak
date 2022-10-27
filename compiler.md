# Writing a Compiler Using LLVM
This tutorial will explore the implementation of Cool programming langague using LLVM. Cool source code will be turned into executable machine code. If you are new to compiler constructions there are few text books in the Reference section. 

The topics covered are in this tutorial are:
* Creating the compiler frontend for Cool using ANTLR
* Using LLVM libraries to generate machine codes for Cool
* Generating the debug information for source level debugging for Cool programs

[LLVM](https://llvm.org/pubs/2008-10-04-ACAT-LLVM-Intro.pdf) implements large number of optimizations that produce better, fast machine codes. The source code for cool-llvm is avaiable at https://github.com/rajikak/cool-llvm.

# References
* [CS 143 Compilers](https://web.stanford.edu/class/cs143/)
* [Engineering A Compiler](https://www.amazon.com/Engineering-Compiler-Keith-D-Cooper/dp/0128154128/ref=sr_1_1?keywords=engineering+a+compiler&qid=1666828042&qu=eyJxc2MiOiIxLjcwIiwicXNhIjoiMS40MyIsInFzcCI6IjEuNjkifQ%3D%3D&s=books&sprefix=Engineegin+a+compiler%2Cstripbooks%2C83&sr=1-1)
* [Writing An Interpreter In Go](https://interpreterbook.com/)
* [Writing A Compiler In Go](https://compilerbook.com/)
* [Cool manual](./assets/cool-manual.pdf)([Online](https://web.stanford.edu/class/cs143/materials/cool-manual.pdf))
* [LLVM](http://www.aosabook.org/en/llvm.html)
* https://tomassetti.me/a-tutorial-on-how-to-write-a-compiler-using-llvm/
* https://llvm.org/docs/tutorial/MyFirstLanguageFrontend/index.html
