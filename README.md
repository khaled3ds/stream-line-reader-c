# Stream Line Reader in C

## Overview

This project implements a buffered line-by-line file reader in C.

It provides functionality similar to:

```
getline()
```

but is implemented manually using low-level system calls and static buffering.

The goal is to read from a file descriptor incrementally, returning one complete line per call, without loading the entire file into memory.

---

## Problem Definition

Standard file reading functions often require:

- Full file loading
- Manual buffer handling
- Custom line extraction logic

This implementation abstracts that complexity and provides a reusable function:

```
char *get_next_line(int fd);
```

Each call returns the next line from the given file descriptor.

---

## Core Features

- Incremental reading
- Buffered I/O
- Handles arbitrary line lengths
- Works with files, stdin, and pipes
- Supports multiple file descriptors simultaneously
- Controlled memory allocation

---

## System Calls Used

- `read()` – low-level file descriptor reading
- `malloc()` – dynamic memory allocation
- `free()` – controlled memory cleanup

No stdio helpers are used.

---

## Internal Architecture

### 1. Static Buffer Strategy

A static buffer retains unread data between calls.

Flow:

```
read() → append to buffer → extract line → store remainder → return line
```

This ensures:

- No data loss
- Efficient partial reads
- Controlled memory growth

---

### 2. Line Extraction Logic

Each call:

1. Checks if a newline exists in the buffer
2. If not, reads more data
3. Extracts characters until `\n`
4. Stores leftover characters for next call

---

## Execution Flow

```
User calls get_next_line(fd)
        ↓
Check static buffer
        ↓
Read if necessary
        ↓
Extract line
        ↓
Return allocated string
```

---

## Compilation

```
make
```

Produces:

```
get_next_line.a
```

---

## Usage Example

```c
int fd = open("file.txt", O_RDONLY);
char *line;

while ((line = get_next_line(fd)) != NULL)
{
    printf("%s", line);
    free(line);
}
close(fd);
```

---

## Memory Management

- Each returned line is dynamically allocated
- Caller is responsible for freeing memory
- Static buffers are managed per file descriptor
- No memory leaks under normal execution

---

## Edge Cases Handled

- Empty files
- Files without newline at end
- Very large lines
- Invalid file descriptors
- Partial reads
- Multiple descriptors simultaneously

---

## Complexity Considerations

- Time complexity: O(n) over file size
- Space complexity: proportional to longest line
- Efficient due to incremental reads (BUFFER_SIZE controlled)

---

## Key Concepts Demonstrated

- Buffered I/O design
- Stateful function behavior
- Static storage duration
- String parsing under constraints
- Memory-safe C programming

---

## What This Project Demonstrates

This implementation reflects:

- Understanding of file descriptor mechanics
- Low-level stream processing
- Incremental parsing logic
- Manual buffer management
- Defensive memory handling

Applicable to:

- Log parsers
- Stream processors
- Shell input handlers
- Network protocol readers

---

## Future Improvements

- Configurable dynamic buffer resizing
- Non-blocking descriptor support
- Integration with event loops
- Benchmark comparison with getline()

---

## Author

Khaled Adas  
Systems Programming | C | Stream Processing
