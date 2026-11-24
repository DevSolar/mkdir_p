# mkdir_p.h

A header-only C library that creates a directory together with all missing
parent directories, like `mkdir -p`, on POSIX and Windows.

Neither POSIX `mkdir()` nor Windows `_mkdir()` is recursive: both create only
the last path component and fail with `ENOENT` if a parent is missing.
`mkdir_p.h` provides the recursive behaviour in one portable function.

## Usage

In exactly **one** C or C++ source file, define `MKDIR_P_IMPLEMENTATION`
before including the header:

```c
#define MKDIR_P_IMPLEMENTATION
#include "mkdir_p.h"
```

In all other files, include the header normally. Then:

```c
if (mkdir_p("a/b/c", 0777) != 0)
{
    perror("mkdir_p");
}
```

## API

```c
int mkdir_p(const char *path, unsigned int mode);
```

Returns `0` on success (including if the directory already exists), or `-1`
with `errno` set:

| errno     | meaning                                              |
|-----------|------------------------------------------------------|
| `EINVAL`  | `path` is `NULL` or empty                            |
| `ENOMEM`  | out of memory                                        |
| `ENOTDIR` | a component of the path exists but is not a directory |
| others    | as set by `mkdir()` / `_mkdir()` (`EACCES`, ...)     |

Platform notes:

- `mode` is passed to `mkdir()` on POSIX (subject to umask) and ignored on
  Windows.
- On Windows, both `/` and `\` are accepted as separators. Drive (`C:`) and
  UNC (`\\server\share`) prefixes are not created.

## Options

Define before including the header:

| macro                    | effect                                                |
|--------------------------|-------------------------------------------------------|
| `MKDIR_P_IMPLEMENTATION` | emit the function bodies in this translation unit     |
| `MKDIR_P_STATIC`         | make all functions `static` (private to the file)     |
| `MKDIR_P_API`            | override the linkage/visibility decoration            |

## License

[CC0 1.0 Universal](LICENSE.CC0) (public domain dedication).
