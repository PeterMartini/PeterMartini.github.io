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
* [RISC-V ELF psABI specification](https://github.com/riscv/riscv-elf-psabi-doc/blob/master/riscv-elf.md)
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
