# 🐜 subprocess.h

[![Build status](https://ci.appveyor.com/api/projects/status/0sm37thiavt9juee?svg=true)](https://ci.appveyor.com/project/sheredom/subprocess-h)
[![Build Status](https://travis-ci.org/sheredom/subprocess.h.svg)](https://travis-ci.org/sheredom/subprocess.h)

A simple one header solution to launching processes and interacting with them
for C/C++.

## Usage

Just `#include "subprocess.h"` in your code!

The current supported compilers are gcc, clang and msvc.

The current supported platforms are Linux, macOS and Windows.

## Design

Subprocess is a single header cross-platform library that allows users to launch
sub-processes, interact with the stdin, stdout, and stderr of the process, and
wait for them to complete.

### Launching a Process

To launch a process you call `subprocess_create` like so:

```c
const char *command_line[] = {"echo", "\"Hello, world!\"", NULL};
struct subprocess_s subprocess;
int result = subprocess_create(command_line, 0, &subprocess);
if (0 != result) {
  // an error occurred!
}
```

You specify an array of string for the command line - terminating the array with
a `NULL` element.

If the process is created successfully then 0 is returned from
`subprocess_create`.

### Writing to the Standard Input of a Process

To write to the standard input of a child process you call `subprocess_stdin` to
get the FILE handle to write with, passing a previously created process, like
so:

```c
FILE* p_stdin = subprocess_stdin(&process);
fputs("Hello, world!", p_stdin);
```

Care must be taken to not write to the stdin after any call to `subprocess_join`
or `subprocess_destroy`.

### Reading from the Standard Output of a Process

To read from the standard output of a child process you call `subprocess_stdout`
to get the FILE handle to read with, passing a previously created process, like
so:

```c
FILE* p_stdout = subprocess_stdout(&process);
char hello_world[32];
fgets(hello_world, 32, p_stdout);
```

Care must be taken to not read from the stdout after any call to 
`subprocess_destroy`.

### Reading from the Standard Error of a Process

To read from the standard error of a child process you call `subprocess_stderr`
to get the FILE handle to read with, passing a previously created process, like
so:

```c
FILE* p_stderr = subprocess_stderr(&process);
char hello_world[32];
fgets(hello_world, 32, p_stderr);
```

Care must be taken to not read from the stderr after any call to 
`subprocess_destroy`.

### Waiting on a Process

To wait for a previously created process to finish executing you call
`subprocess_join` like so:

```c
int process_return;
int result = subprocess_join(&process, &process_return);
if (0 != result) {
  // an error occurred!
}
```

The return code of the child process is returned in the second argument (stored
into `process_return` in the example above). This parameter can be `NULL` if you
don't care about the process' return code.

### Destroying a Process

To destroy a previously created process you call `subprocess_destroy` like so:

```c
int result = subprocess_destroy(&process);
if (0 != result) {
  // an error occurred!
}
```

Note that you can destroy a process before it has completed execution - this
allows you to spawn a process that would outlive the execution of the parent
process for instance.

## Todo

The current list of todos:

* Add the ability to [set environment variables of the child process](https://github.com/sheredom/subprocess.h/issues/1)
  as suggested by [@graphitemaster](https://github.com/graphitemaster).
* Add the ability to specify if a child process should die if the parent process
  is terminated.

## License

This is free and unencumbered software released into the public domain.

Anyone is free to copy, modify, publish, use, compile, sell, or
distribute this software, either in source code form or as a compiled
binary, for any purpose, commercial or non-commercial, and by any
means.

In jurisdictions that recognize copyright laws, the author or authors
of this software dedicate any and all copyright interest in the
software to the public domain. We make this dedication for the benefit
of the public at large and to the detriment of our heirs and
successors. We intend this dedication to be an overt act of
relinquishment in perpetuity of all present and future rights to this
software under copyright law.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.

For more information, please refer to <http://unlicense.org/>
