# Get Next Line

A robust C implementation of the `get_next_line` function developed as part of the 42 Cursus. The project provides a line-oriented file-reading interface that returns one complete line per function call, including the terminating newline when present.

The implementation focuses on low-level file I/O, dynamic memory management, buffering, error handling, and the preservation of unread data between successive calls.

## Project Overview

The standard `read()` system call works with raw byte buffers rather than logical lines. `get_next_line` solves this problem by accumulating data until a newline is found, returning the next available line while preserving any remaining characters for the following call.

This repository contains both the mandatory implementation and the bonus implementation:

- **Mandatory version:** supports one file descriptor at a time.
- **Bonus version:** supports multiple file descriptors simultaneously by maintaining independent persistent buffers.

## Features

- Reads files and standard input line by line.
- Returns one line for each call to `get_next_line()`.
- Preserves the newline character when it is present.
- Correctly handles files that do not end with a newline.
- Supports configurable buffer sizes through `BUFFER_SIZE`.
- Handles empty files and end-of-file conditions.
- Detects invalid file descriptors and read errors.
- Frees allocated memory when an error or end-of-file condition occurs.
- Includes a bonus implementation for simultaneous multi-file-descriptor reading.
- Uses only low-level C functions required by the project.

## Repository Structure

```text
.
├── en.subject.pdf
└── src
    ├── get_next_line.c
    ├── get_next_line.h
    ├── get_next_line_utils.c
    ├── get_next_line_bonus.c
    ├── get_next_line_bonus.h
    └── get_next_line_utils_bonus.c
```

### Main files

- `get_next_line.c` — mandatory implementation of `get_next_line()`.
- `get_next_line.h` — declarations and configuration for the mandatory version.
- `get_next_line_utils.c` — internal string and memory utilities.
- `get_next_line_bonus.c` — multi-file-descriptor implementation.
- `get_next_line_bonus.h` — declarations and configuration for the bonus version.
- `get_next_line_utils_bonus.c` — utility functions used by the bonus implementation.
- `en.subject.pdf` — official project subject.

## How It Works

The function uses a persistent buffer to retain data that has been read but not yet returned. The general process is:

1. Allocate a temporary buffer using the configured `BUFFER_SIZE`.
2. Read data from the file descriptor with `read()`.
3. Append the newly read data to the persistent storage.
4. Stop reading when a newline is found or the end of the file is reached.
5. Extract and return the next complete line.
6. Preserve the remaining data for the next function call.
7. Release all allocated memory when no data remains or an error occurs.

The bonus implementation stores one persistent buffer per file descriptor, allowing calls such as `get_next_line(fd1)`, `get_next_line(fd2)`, and `get_next_line(fd1)` without mixing data between streams.

## API

```c
char *get_next_line(int fd);
```

### Parameters

- `fd` — the file descriptor from which data should be read.

### Return value

- A dynamically allocated string containing the next line.
- `NULL` when the end of the input is reached, the file descriptor is invalid, a read error occurs, or memory allocation fails.

The caller is responsible for freeing every returned line:

```c
free(line);
```

## Compilation

`BUFFER_SIZE` defaults to `42`, but it can be overridden during compilation:

```bash
gcc -Wall -Wextra -Werror -D BUFFER_SIZE=42 \
    src/get_next_line.c \
    src/get_next_line_utils.c \
    main.c -o get_next_line
```

To compile with another buffer size:

```bash
gcc -Wall -Wextra -Werror -D BUFFER_SIZE=1024 \
    src/get_next_line.c \
    src/get_next_line_utils.c \
    main.c -o get_next_line
```

## Using the Bonus Version

Compile the bonus sources instead of the mandatory sources:

```bash
gcc -Wall -Wextra -Werror -D BUFFER_SIZE=42 \
    src/get_next_line_bonus.c \
    src/get_next_line_utils_bonus.c \
    main.c -o get_next_line_bonus
```

The bonus version is designed to read from multiple file descriptors independently.

## Example

```c
#include "get_next_line.h"
#include <fcntl.h>
#include <stdio.hn>
#include <stdlib.h>

int main(void)
{
    int     fd;
    char    *line;

    fd = open("example.txt", O_RDONLY);
    if (fd < 0)
        return (1);
    line = get_next_line(fd);
    while (line != NULL)
    {
        printf("%s", line);
        free(line);
        line = get_next_line(fd);
    }
    close(fd);
    return (0);
}
```

> Replace `#include <stdio.hn>` with `#include <stdio.h>` if copying the example. The incorrect spelling is intentionally not part of the project implementation.

## Memory Management

Each returned line is allocated dynamically and must be released by the caller. Internal buffers are also released when:

- A read error occurs.
- Memory allocation fails.
- The end of the file is reached and no data remains.
- An invalid file descriptor is detected.

This design prevents persistent allocations from surviving after the input stream can no longer produce data.

## Learning Objectives

This project provides practical experience with:

- File descriptors and the `read()` system call.
- Static variables and persistent state.
- Dynamic allocation and deallocation.
- String manipulation without standard string-library shortcuts.
- Buffer management and incremental parsing.
- Error handling in C.
- Designing reusable and modular functions.
- Avoiding memory leaks and invalid memory access.

## Requirements

- A C compiler such as `cc` or `gcc`.
- A POSIX-compatible environment.
- The 42 Norm for source-code style when evaluated within the 42 curriculum.

## Notes

This project is part of the 42 Cursus curriculum. It is intended for educational purposes and demonstrates how a higher-level line-reading abstraction can be built on top of the low-level `read()` system call.

## Author

Developed by **A-chem** as part of the 42 Cursus.
