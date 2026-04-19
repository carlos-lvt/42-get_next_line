![42 Porto](https://img.shields.io/badge/42-Porto-blue)

*This project has been created as part of the 42 curriculum by carlaugu.*

# Get Next Line
A C function that reads one line at a time from a file descriptor, using a static variable to maintain state between calls.

## Description
`get_next_line` returns the next line from a given file descriptor each time it is called. It works with both regular files and standard input, and correctly handles lines of any size and buffer sizes of any value. The function retains its reading position between calls using a static variable, making sequential reads possible without external state management.

## Instructions

### Compilation
The `BUFFER_SIZE` used by `read()` is defined at compile time:

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c
```

`BUFFER_SIZE` can be any positive integer. The function must work correctly regardless of the value chosen.

### Usage
Include the header and call the function in a loop:

```c
#include "get_next_line.h"

int main(void)
{
    int     fd;
    char    *line;

    fd = open("file.txt", O_RDONLY);
    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }
    close(fd);
    return (0);
}
```

Each call returns the next line including the terminating `\n`, except for the last line if the file does not end with `\n`. Returns `NULL` when there is nothing left to read or on error.

## Algorithm
`get_next_line` uses a **static buffer** to persist unread data between calls. The core idea is that each `read()` call may bring in more data than needed for one line — the excess must be saved for the next call.

The function works as follows:
1. On each call, `read()` fills a temporary buffer of `BUFFER_SIZE` bytes
2. This buffer is appended to a static string (`stash`) that persists across calls
3. The stash is searched for a `\n` character
4. If found, everything up to and including `\n` is extracted and returned as the current line; the remainder stays in the stash for the next call
5. If no `\n` is found, reading continues until one is found or EOF is reached
6. On EOF, whatever remains in the stash is returned as the final line, then `NULL` on the subsequent call

This approach reads only as much as necessary per call and avoids loading the entire file into memory. It handles edge cases including empty lines, files not ending in `\n`, and very large or very small `BUFFER_SIZE` values.

## Bonus
The bonus part manages multiple file descriptors simultaneously using a single static variable — an array indexed by file descriptor. Each fd maintains its own independent stash, allowing interleaved reads across multiple fds without losing state.

Files submitted for the bonus:
```
get_next_line_bonus.c
get_next_line_bonus.h
get_next_line_utils_bonus.c
```

## Allowed Functions
`read` `malloc` `free`
