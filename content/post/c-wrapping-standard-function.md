---
title: "Wrapping Standard Library Function in C"
date: 2018-07-15T13:16:22+05:30
Description: ""
Tags: ["C"]
Categories: ["Programming"]
---

It is straightforward to put print messages in user defined functions (for debugging or for logging purposes) but what if you want to do something similar to standard library functions? It is not that difficult either. GNU Linker's(=ld=) =--wrap=symbol= option helps achieving this.

Suppose, you want to wrap ```strlen```. Here is the scheme:

1. The program will be compiled with following linker option: ```-Wl,--wrap=strlen```
2. Call the =strlen= as-is in your program
3. Define ```__wrap_strlen``` with the same prototype as ```strlen```. Do whatever is desired inside this function and call ```__real_strlen```. ```__real_strlen``` will call the actual library routine. 

Here is a complete example wrapping ```strlen```:

``` c++
#include <stdio.h>
#include <string.h>

/* call to strlen will call __wrap_strlen first */
size_t __wrap_strlen (const char *s)
{
	printf ("inside strlen wrapper\n");
	/* lib's strlen call will happen with __real_strlen */
	return __real_strlen (s);
}

int main (int argc, char *argv[])
{
	/* call to strlen remains as-is, and calls __wrap_strlen */
	printf ("program's name length: %zu\n", strlen(argv[0]));
        return 0;
}

```

Compile it as:

``` shell
$ gcc wrap_strlen.c -Wl,--wrap=strlen -o wrap_strlen
```

Run:

``` shell
$ ./wrap_strlen
inside strlen wrapper
program's name length: 13
```

Here is what ld info page says which is self-explanatory:

    --wrap=symbol

    Use a wrapper function for symbol.  Any undefined reference to symbol will be resolved to "__wrap_symbol".  Any undefined reference to
    "__real_symbol" will be resolved to symbol.

    This can be used to provide a wrapper for a system function.  The wrapper function should be called "__wrap_symbol".  If it wishes to
    call the system function, it should call "__real_symbol".

    Here is a trivial example:

            void *
            __wrap_malloc (size_t c)
            {
              printf ("malloc called with %zu\n", c);
              return __real_malloc (c);
            }

    If you link other code with this file using --wrap malloc, then all calls to "malloc" will call the function "__wrap_malloc" instead.
    The call to "__real_malloc" in "__wrap_malloc" will call the real "malloc" function.

    You may wish to provide a "__real_malloc" function as well, so that links without the --wrap option will succeed.  If you do this, you
    should not put the definition of "__real_malloc" in the same file as "__wrap_malloc"; if you do, the assembler may resolve the call
    before the linker has a chance to wrap it to "malloc".

Well, short, wrapping it up!
