*This project has been created as part of the 42 curriculum by hait-h-m.*

## Description
**Get Next Line** is a core 42 curriculum project whose goal is to implement a function in C that returns a single line read from a file descriptor (`fd`), terminated with a `\n` character if present, on each successive call. 

Repeated calls to `get_next_line()` allow the caller to read an entire text stream sequentially (from regular files, pipes, or `stdin`) line by line without loading the full file into memory at once.

## Algorithm Explanation and Justification
The chosen algorithm relies on three main phases per call:

1. **Read & Accumulate (`read_and_stash`)**:
   - A buffer of size `BUFFER_SIZE + 1` is allocated dynamically on the heap (to prevent stack overflow even if `BUFFER_SIZE` is extremely large, such as `10000000`).
   - The function reads chunks of `BUFFER_SIZE` bytes using `read()` and appends them to a static variable (`stash`) using a custom `ft_strjoin`.
   - The loop stops as soon as a newline character (`\n`) is discovered in `stash` or when EOF (`bytes_read == 0`) is reached. This adheres strictly to the requirement of reading as little as possible per call.

2. **Line Extraction (`extract_line`)**:
   - The accumulated `stash` is parsed up to and including the first `\n` (or EOF).
   - Memory is allocated for this slice, the characters are copied, and a terminating null byte `\0` is appended to produce the returned string.

3. **Stash Truncation (`clean_stash`)**:
   - Any characters read past the newline are preserved in a new dynamically allocated string, and the old stash is freed.
   - If no characters remain after the newline (or EOF was reached), the stash is completely freed and reset to `NULL`, preventing any dangling pointers or memory leaks.

### Justification
This static buffer approach maintains an $O(N)$ time complexity relative to the line length while maintaining optimal memory usage. Reading only until a newline is found prevents premature buffering of the entire file, which satisfies both performance requirements and constraints on system resource consumption.

For the bonus part, the static pointer is extended to an array of pointers indexed by file descriptor (`static char *stash[OPEN_MAX]`), allowing simultaneous tracking of multiple file streams without cross-contamination.

## Instructions

### Compilation
The project is compiled with `cc` along with the flags `-Wall -Wextra -Werror` and an optional `-D BUFFER_SIZE=n`:

```bash
# Mandatory compilation example
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl

# Bonus compilation example
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line_bonus.c get_next_line_utils_bonus.c main.c -o gnl_bonus