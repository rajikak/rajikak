# Building and Installing LLVM on Apple Mac M1 and on Linux

Complete instructions on building LLVM from source can be found [here](https://llvm.org/docs/CMake.html).

## Building LLVM on Apple M1
Install cmake and add the bin path into PATH:
```
$ wget https://github.com/Kitware/CMake/releases/download/v3.24.2/cmake-3.24.2-linux-x86_64.tar.gz
$ tar -xvf cmake-3.24.2-linux-x86_64.tar.gz
```

Install xcode [command](https://mac.install.guide/commandlinetools/4.html) line tools:
```
$ sudo xcode-select --install
```
Verify the installation:
```
$ xcode-select -p
/Library/Developer/CommandLineTools

$ which clang++
/usr/bin/clang++

$ /usr/bin/clang++ -v
Apple clang version 14.0.0 (clang-1400.0.29.202)
Target: arm64-apple-darwin21.5.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin

$ /usr/bin/clang -v
Apple clang version 14.0.0 (clang-1400.0.29.202)
Target: arm64-apple-darwin21.5.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
```

Compile and install LLVM from the source (based on above tool chain version, download the appropiate LLVM release version).
This is to ensure new compiler code can be compiled against the System libraries without any issue. Example below shows how to
compile the development version:
```
$ pwd
/Users/kumarasiri/
$ git clone https://github.com/llvm/llvm-project.git # or wget https://github.com/llvm/llvm-project/archive/refs/tags/llvmorg-14.0.0.tar.gz
$ mkdir llvm-project/install
$ cd llvm-project/build
$ cmake -G 'Unix Makefiles' -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/Users/kumarasiri/llvm-project/install \
    -DLLVM_TARGETS_TO_BUILD='AArch64' \
    -DLLVM_ENABLE_PROJECTS='bolt;clang;clang-tools-extra;libclc;lld;lldb;mlir' \
	-DLLVM_ENABLE_RUNTIMES='libcxx;libcxxabi;libunwind' \
    -DCMAKE_OSX_ARCHITECTURES='arm64' \
    -DLLDB_USE_SYSTEM_DEBUGSERVER=ON  \
    -DLLDB_INCLUDE_TESTS=OFF \
    ../llvm 
$ make;make install 
```

Add `/Users/kumarasiri/llvm-project/install/bin` into `$PATH`. 

## Building LLVM on a Linux System
Install cmake and add the bin path into PATH:
```
$ wget https://github.com/Kitware/CMake/releases/download/v3.24.2/cmake-3.24.2-linux-x86_64.tar.gz
$ tar -xvf cmake-3.24.2-linux-x86_64.tar.gz
```

Install developer tools:
```
$ yum install -y git gcc gcc-c++ python zlib make
```

Compile and install LLVM from the source:
```
$ pwd
/home/ec2-user/
$ git clone https://github.com/llvm/llvm-project.git
$ mkdir llvm-project/install
$ cd llvm-project/build
$ cmake -G 'Unix Makefiles' \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=/home/ec2-user/llvm-project/install \
        -DLLVM_TARGETS_TO_BUILD='X86' \
        -DLLVM_ENABLE_PROJECTS='clang;lldb;lld;mlir;clang-tools-extra;compiler-rt' \
        ../llvm 
$ make;make install
```

Add `/home/ec2-user/llvm-project/install/bin` into `$PATH`.
