---
layout: page
title: Bookshelf
permalink: /bookshelf/
---

My virtual bookshelf - a list of articles / blog posts / books worth
having around for reference.

## RISC-V

### libunwind

Various resources used to add RISC-V support for [libunwind](https://www.nongnu.org/libunwind/)

* [How debuggers work: Part 3 - Debugging information](https://eli.thegreenplace.net/2011/02/07/how-debuggers-work-part-3-debugging-information) - A walk through of DWARF, ELF, and debuggers
* [RISC-V Calling Conventions](https://riscv.org/wp-content/uploads/2015/01/riscv-calling.pdf)
  * [RISC-V Calling Conventions (lecture notes - all text)](https://pdos.csail.mit.edu/6.828/2019/lec/l-riscv.txt)
* [RISC-V ELF psABI specification](https://github.com/riscv/riscv-elf-psabi-doc/blob/master/riscv-elf.md)
  * [x86 psABI for comparison](https://gitlab.com/x86-psABIs)
* [binutils-gdb: gdb/riscv: Provide non-DWARF stack unwinder](https://sourceware.org/legacy-ml/gdb-cvs/2018-09/msg00002.html) (a patch mailed to the `gdb` mailing list)
* [libunwind docs](https://www.nongnu.org/libunwind/docs.html)
* [DWARF Register Mappings](https://github.com/riscv/riscv-elf-psabi-doc/issues/70) - A GitHub issue for the RISC-V psABI, raised by someone trying to add stack unwinding to Mono
* [Manual Stack Unwinding](https://github.com/slavaim/riscv-notes/blob/master/magenta/manual-stack-unwinding.md) - Someone's notes testing stack unwinding on RISC-V (from 2017; may be out of date)
* [cfa == rsp on x86_64](https://www.corsix.org/content/cfa-rsp-x86-64) - A post explaining the relationship between 'CFA' (Canonical Frame Address) in the DWARF standard and the RSP register on `x86_64`
* [Generating core dumps on bare metal RISC-V](https://forums.sifive.com/t/generating-core-dumps-on-bare-metal-risc-v/3088) - A forum post asking for help on core dumps on RISC-V that mentions details of the `.reg` section of the core dump files
* [riscv.h](https://github.com/gcc-mirror/gcc/blob/master/gcc/config/riscv/riscv.h) - The GCC config for RISC-V, a useful reference for important implementation details
* [RISC-V registers](https://en.wikichip.org/wiki/risc-v/registers) A short and handy reference page on RISC-V registers.  Not comprehensive; it details the 32 general purpose registers but doesn't mention floating point registers
* [libunwind patch for AArch64 support](https://github.com/libunwind/libunwind/commit/ac6c0a6535975f1dc2da6e4e2766614baac2a14a#diff-44c9a8e487fd562ca0a78f2469deee6ca193e450bf03a83866aff7f590e9e494)
* [Emulating RISC-V Debian on WSL2](https://blog.davidburela.com/2020/11/15/emulating-risc-v-debian-on-wsl2/)
* [Building a RISC-V CPU from scratch](https://spectrum.ieee.org/geek-life/hands-on/build-a-riscv-cpu-from-scratch)
* [The Apple M1 and RISC-V (lots of links on history and current status as well as a suggestion of RISC-V as the basis of a co-processor revolution)](https://erik-engheim.medium.com/apple-m1-foreshadows-risc-v-dd63a62b2562)
* [RISC-V from scratch 2: Hardware layouts, linker scripts, and C runtimes](https://twilco.github.io/riscv-from-scratch/2019/04/27/riscv-from-scratch-2.html)
* [Transparently running binaries from any architecture in Linux with QEMU and binfmt_misc](https://ownyourbits.com/2018/06/13/transparently-running-binaries-from-any-architecture-in-linux-with-qemu-and-binfmt_misc)

## Neat Tweets

* [6 interesting HTML attributes](https://twitter.com/Insharamin/status/1399621939480522755)
* [Debugging mystery: the case of the 50ms request (understanding TCP)](https://twitter.com/b0rk/status/1390012478386577411)
* [A long tweet thread about failures of complex systems (and why the software industry is still immature here)](https://twitter.com/www_ora_tion_ca/status/1027539289750429696)
* [Favorite Security Articles](https://twitter.com/grittygrease/status/1028769194643353600)

## Compilers

* [A Basic Just-In-Time Compiler](https://nullprogram.com/blog/2015/03/19)
* [JIT compiler and runtime for a toy language, using Cranelift](https://github.com/bytecodealliance/cranelift-jit-demo)
* [A Complete Guide to LLVM for Programming Language Creators](https://mukulrathi.com/create-your-own-programming-language/llvm-ir-cpp-api-tutorial/)
* [Linux x86 Program Start Up](http://dbp-consulting.com/tutorials/debugging/linuxProgramStartup.html)
* [A Whirlwind Tutorial on Creating Really Teensy ELF Executables for Linux](http://muppetlabs.com/~breadbox/sotware/tiny/teensy.html)

## Firmware

* [UEFI & EDK II Training](https://twitter.com/Intel_Brian/status/1143630892855771136)

## Service Mesh

* [The rise of Layer 7, microservices, and the proxy war with Envoy, NGINX, and HAProxy](http://slideshare.net/datawire/the-rise-of-layer-7-microservices-and-the-proxy-war-with-envoy-nginx-and-haproxy)
* [REST API Design Guide](https://github.com/NationalBankBelgium/REST-API-Design-Guide)
* [Kubernetes and Networks - why is this so dang hard?](https://speakerdeck.com/thockin/kubernetes-and-networks-why-is-this-so-dang-hard)

## Debugging

* [Debugging Incidents in Google's Distributed Systems](https://queue.acm.org/detail.cfm?id=3404974)
* [Basic Assembler Debugging with GDB](https://dbp-consulting.com/tutorials/debugging/basicAsmDebuggingGDB.html)

## Assorted Tools

* [Get a Windows 10 Development Environment](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/)

## File Formats

* [In which a PDF is a Git Repository Containing its own LaTeX Source and a Copy of Itself](https://github.com/ESultanik/PDFGitPolyglot)

## Reverse Engineering

* [ROP Emporium - Learn return-oriented programming through a series of challenges](http://ropemporium.com)
