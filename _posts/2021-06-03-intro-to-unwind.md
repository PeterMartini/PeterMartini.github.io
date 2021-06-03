---
layout: post
title: Adding RISC-V support to `libunwind`
---

While playing with a `qemu` virtual machine image of a Debian [RISC-V](http://riscv.org)
system (which was bootstrapped largely by tweaking the instructions
on [this blog post](https://blog.davidburela.com/2020/11/15/emulating-risc-v-debian-on-wsl2/)),
I found one library missing for a project I wanted to compile: [libunwind](https://www.nongnu.org/libunwind/).

There was an [issue raised on the repo](https://github.com/libunwind/libunwind/issues/99) asking for
RISC-V support, which was closed with a note that PRs would be welcome but the maintainers
didn't have the resources (time or hardware) to work on it themselves.

This seemed like a great place to jump in and start writing code for RISC-V myself!

But first, what is `libunwind`?

## Introduction to `libunwind`

`libunwind` is a library that allows programs to inspect and capture the call-chain of a program.
The [homepage](https://www.nongnu.org/libunwind/) lists 4 main use cases: exception handling,
debugging, introspection, and efficient [setjmp()](https://en.wikipedia.org/wiki/Setjmp.h)

That last use case is worth a demonstration.  The code below doesn't have any comments, since
the rest of this post will be explaining the flow of the program, how it interacts with the
C++ runtime, and what this means for unwinding the call chain.

## A Test Program

Let's take this file, `test.cpp`:

```c++
#include <iostream>
#include <cstring>

#define UNW_LOCAL_ONLY
#include <libunwind.h>

#include <setjmp.h>

static jmp_buf buf;

void show_backtrace (void) {
  unw_cursor_t cursor;
  unw_context_t uc;
  unw_word_t ip, offset;

  unw_getcontext(&uc);
  unw_init_local(&cursor, &uc);
  while (unw_step(&cursor) > 0) {
    unw_get_reg(&cursor, UNW_REG_IP, &ip);
    char name[256];
    if (0 != unw_get_proc_name(&cursor, name, sizeof(name), &offset)) {
      strncpy(name, "<unknown>", sizeof(name));
      offset = -1;
    }
    std::cout << std::hex
              << " ip = " << ip
              << " name = " << name
              << " offset = " << offset
              << std::endl;
  }
}

class Foo {
  private:
    std::string m_loc;
  public:
    Foo(const char * loc) : m_loc(loc) {
      std::cout << m_loc << std::endl;
      show_backtrace();
    }
    ~Foo(){
      std::cout << m_loc << std::endl;
      show_backtrace();
   }
};

void second() {
  Foo bar("Inside second");
  longjmp(buf, 1);
}

void first() {
  second();
  // Not reached
}

int main(int argc, char **argv)
{
  Foo bar("In main");  
  if (!setjmp(buf)) {
    first();
  } else {
    std::cout << "After setjmp:" << std::endl;
    show_backtrace(); 
  }
}
```

## Building And Running It

Assuming a Linux machine with `libunwind-dev` and `g++` installed, you can compile
and run it with `g++`, and should see something like this:

```shell
$ g++ -o test test.cpp -lunwind -no-pie
$ ./test
In main
 ip = 40186e name = _ZN3FooC1EPKc offset = 80
 ip = 4015bc name = main offset = 36
 ip = 7fab48f830b3 name = __libc_start_main offset = f3
 ip = 4012fe name = _start offset = 2e
Inside second
 ip = 40186e name = _ZN3FooC1EPKc offset = 80
 ip = 401565 name = _Z6secondv offset = 2e
 ip = 401583 name = _Z5firstv offset = d
 ip = 4015da name = main offset = 54
 ip = 7fab48f830b3 name = __libc_start_main offset = f3
 ip = 4012fe name = _start offset = 2e
After setjmp
 ip = 401609 name = main offset = 83
 ip = 7fab48f830b3 name = __libc_start_main offset = f3
 ip = 4012fe name = _start offset = 2e
In main
 ip = 401905 name = _ZN3FooD1Ev offset = 3d
 ip = 401615 name = main offset = 8f
 ip = 7fab48f830b3 name = __libc_start_main offset = f3
 ip = 4012fe name = _start offset = 2e
```

Each time `show_backtrace` is called, it prints the name
of the function that called it, the `ip` (instruction
pointer) or absolute address of the instruction after
that function, and the `offset` of that instruction from
the base of the function.  It then repeats this to print
the function above that one, and then the function above
that one, until it reaches the top of the call stack, 
which is not `main` but a function named `_start`.

The `-no-pie` argument isn't really necessary but was useful for this
demonstration, since it disabled [ASLR](https://en.wikipedia.org/wiki/Address_space_layout_randomization) (at
least on my machine).  With `-no-pie`, every run of the program lists
the same numbers in the same places for `ip` (instruction pointer); without that
flag, address space layout randomization kicks in and the base address changes
for every run.  It's worth testing this both ways, since it highlights the
value of `libunwind`: regardless of where the code is loaded into memory
when the program is running, `libunwind` will still find the function
name and the offset of that instruction.

At least, it will if the program still has debug symbols.

```shell
$ file test
test: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=85194eb200d7777cc79d4fd655bf66b351c0d2e1, for GNU/Linux 3.2.0, not stripped
```

This version of the program has debug symbols embedded ("not stripped").  If those
debug symbols are removed ("stripped"), the unwind call to get the function
name and offset ([unw_get_proc_name](https://www.nongnu.org/libunwind/man/unw_get_proc_name%283%29.html))
will fail:

```shell
$ strip test
$ ./test 
In main
 ip = 40186e name = <unknown> offset = ffffffffffffffff
 ip = 4015bc name = <unknown> offset = ffffffffffffffff
 ip = 7f158bea10b3 name = __libc_start_main offset = f3
 ip = 4012fe name = <unknown> offset = ffffffffffffffff
```

The only name that can still be resolved is the one that's used to reference a function
in a shared library; the rest of the mapping details about names has been
lost.  That data came from [DWARF](https://en.wikipedia.org/wiki/DWARF) metadata
stored in the [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) file
named `./test` here; the `strip` command removed that debug data, and without it,
`libunwind` couldn't enrich the absolute address with a name or offset.

This is one example of the complexity of unwinding.  It's not enough to understand
just the RISC-V assembly instructions; file format and memory representations, 
[calling conventions](https://en.wikipedia.org/wiki/Calling_convention), exception
handling, and other special cases like `setjmp` need to be handled as well.

## `setjmp`

What is `setjmp` that makes it so special?

The code above has 5 functions (other than `main`):

1. `show_backtrace`, which wraps the `libunwind` functions
   into a neat little package to iteratively walk up the stack and list calling functions
2. `Foo::Foo(loc)`, a constructor for an object of class `Foo` that tracks the location
   it was called and prints a stack trace every time its called
3. `Foo::~Foo()`, the destructor, which similarly prints a stack trace every time its called
4. `first`, which does nothing but call `second`
5. `second`, which calls `longjmp`

`setjmp` and `longjmp` operate as a pair.  When `setjmp` is called the first time, it saves
the context of where its running to a buffer and returns 0, and when `longjmp` is called with
that buffer later on *the program behaves as if had just returned from the original `setjmp`
call*.

That is, in this block of code:

```c++
  if (!setjmp(buf)) {
    first();
  } else {
    std::cout << "After setjmp:" << std::endl;
    show_backtrace(); 
  }
```

*both* branches of the `if` statement will be executed!  The first time `setjmp` is called,
it returns 0, and the top half of the `if` statement is executed.  Then `first` calls `second`
and `second` calls `longjmp`, and the program picks up as if `setjmp` had just returned a 1
and falls into the `else` statement.

This is an interesting corner case for `libunwind` since neither `second` nor `first` ever
actually returned!

In the example output before, it's easy to see `libunwind` handled this properly:

```
...
After setjmp
 ip = 401609 name = main offset = 83
 ip = 7f7b3b8d80b3 name = __libc_start_main offset = f3
 ip = 4012fe name = _start offset = 2e
...
```

What may be less obvious is that the lack of a `return` from `second` meant that
normal C++ stack unwinding, including calling `Foo`'s destructor, did not happen:

```shell
$ ./test | c++filt | grep Foo
 ip = 40186e name = Foo::Foo(char const*) offset = 80
 ip = 40186e name = Foo::Foo(char const*) offset = 80
 ip = 401905 name = Foo::~Foo() offset = 3d
```

## Next Steps

This post included a test program that exercised both the basic features of `libunwind`
and a specific, challenging corner case (`setjmp`/`longjmp`), as well as highlighting
the role of DWARF data in getting meaningful output.

There are a few more posts worth writing as the work on this develops: an Ubuntu/Debian
focused post on setting up a RISC-V virtual machine; an overview of the flavors of
RISC-V architecture (32-bit, 64-bit, and floating point extensions); and maybe experience
testing this on the [HiFive Unmatched](https://www.mouser.com/ProductDetail/115-HF105-000)
when I can get one to test.

## A closing note: which `libunwind`?

There are a few variants of `libunwind` out there.  On my Ubuntu system, `libunwind-dev` is
based on github.com/libunwind/libunwind, but it seems LLVM has their own version
also called `libunwind`.  The reason for the split and some history was [explained on the LLVM mailing list](http://lists.llvm.org/pipermail/cfe-dev/2016-September/050649.html).
