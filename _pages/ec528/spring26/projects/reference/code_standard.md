---
title: ""
permalink: /ec528/fall25/projects/reference/standards
author_profile: false  
classes: ec528-page
layout: single
---

# Coding Standards 

Our standards for coding are most important for grading. We want to stress that aside from the fact that we are explicitly basing part of your grade on these things, good coding practices will improve the quality of your code. This makes it easier for your partners to interact with it, and ultimately, will improve your chances of having a good working program. That said once, the rest of this document will discuss only the ways in which our coding standards will affect our grading.

## Style

Style, for the purposes of our grading, refers to how readable your code is. At minimum, this means that your code is well formatted, your variable names are descriptive and your functions are decomposed and well commented. Any other factors which make it hard (or easy) for us to read or use your code will be reflected in your style grade.

The existing Pintos code is written in the GNU style and largely follows the [GNU Coding Standards](https://www.gnu.org/prep/standards/). We encourage you to follow the applicable parts of them too, especially chapter 5, "Making the Best Use of C." Using a different style won't cause actual problems, but it's ugly to see gratuitous differences in style from one function to another. If your code is too ugly, it will cost you points.

Please limit C source file lines to at most 79 characters long.

Pintos comments sometimes refer to external standards or specifications by writing a name inside square brackets, like this: <span style="color:Crimson">`[IA32-v3a]`</span>. These names refer to the reference names used in this documentation (see section [Bibliography](https://web.eecs.umich.edu/~ryanph/jhu/cs318/fall22/project/pintos_14.html#SEC176)).

If you remove existing Pintos code, please delete it from your source file entirely. Don't just put it into a comment or a conditional compilation directive, because that makes the resulting code hard to read.

We're only going to do a compile in the directory for the project being submitted. You don't need to make sure that the previous projects also compile.

Project code should be written so that all of the subproblems for the project function together, that is, without the need to rebuild with different macros defined, etc. If you do extra credit work that changes normal Pintos behavior so as to interfere with grading, then you must implement it so that it only acts that way when given a special command-line option of the form -name, where name is a name of your choice. You can add such an option by modifying <span style="color:Crimson">`parse_options()`</span> in `threads/init.c`.

The introduction describes additional coding style requirements (see section [Design](../grading.md#code-style)).

## C99

The Pintos source code uses a few features of the "C99" standard library that were not in the original 1989 standard for C. Many programmers are unaware of these features, so we will describe them. The new features used in Pintos are mostly in new headers:

* <span style="color:CornFlowerBlue">`stdbool.h`</span>

  Defines macros <span style="color:Crimson">`bool`</span>, a 1-bit type that takes on only the values 0 and 1, <span style="color:Crimson">`true`</span>, which expands to 1, and <span style="color:Crimson">`false`</span>, which expands to 0.
* <span style="color:CornFlowerBlue">`stdint.h`</span>

    On systems that support them, this header defines types <span style="color:Crimson">`intn_t`</span> and <span style="color:Crimson">`uintn_t`</span> for n = 8, 16, 32, 64, and possibly other values. These are 2's complement signed and unsigned types, respectively, with the given number of bits.

    On systems where it is possible, this header also defines types <span style="color:Crimson">`intptr_t`</span> and <span style="color:Crimson">`uintptr_t`</span>, which are integer types big enough to hold a pointer.

    On all systems, this header defines types <span style="color:Crimson">`intmax_t`</span> and <span style="color:Crimson">`uintmax_t`</span>, which are the system's signed and unsigned integer types with the widest ranges.

    For every signed integer type <span style="color:Crimson">`type_t`</span> defined here, as well as for <span style="color:Crimson">`ptrdiff_t`</span> defined in <span style="color:Crimson">`<stddef.h>`</span>, this header also defines macros <span style="color:Crimson">`TYPE_MAX`</span> and <span style="color:Crimson">`TYPE_MIN`</span> that give the type's range. Similarly, for every unsigned integer type `type_t` defined here, as well as for `size_t` defined in `<stddef.h>`, this header defines a `TYPE_MAX` macro giving its maximum value.

* <span style="color:CornFlowerBlue">`inttypes.h`</span>

    <span style="color:Crimson">`<stdint.h>`</span> provides no straightforward way to format the types it defines with `printf()` and related functions. This header provides macros to help with that.

    For every <span style="color:Crimson">`intn_t`</span> defined by <span style="color:Crimson">`<stdint.h>`</span>, it provides macros <span style="color:Crimson">`PRIdn`</span> and <span style="color:Crimson">`PRIin`</span> for formatting values of that type with <span style="color:Crimson">`"%d"`</span> and <span style="color:Crimson">`"%i"`</span>. Similarly, for every <span style="color:Crimson">`uintn_t`</span>, it provides <span style="color:Crimson">`PRIon`</span>, <span style="color:Crimson">`PRIun`</span>, <span style="color:Crimson">`PRIux`</span>, and <span style="color:Crimson">`PRIuX`</span>.

    You use these something like this, taking advantage of the fact that the C compiler concatenates adjacent string literals:

    ```
    #include <inttypes.h>
    ...
    int32_t value = ...;
    printf ("value=%08"PRId32"\n", value);
    ```

    The % is not supplied by the <span style="color:Crimson">`PRI`</span> macros. As shown above, you supply it yourself and follow it by any flags, field width, etc.

* <span style="color:CornFlowerBlue;">`stdio.h`</span>
  * The <span style="color:Crimson">`printf()`</span> function has some new type modifiers for printing standard types:
    * **j:**
      For <span style="color:Crimson">`intmax_t`</span> (e.g. %jd) or <span style="color:Crimson">`uintmax_t`</span> (e.g. %ju).
    * **z:**
      For <span style="color:Crimson">`size_t`</span> (e.g. %zu).
    * **t:**
      For <span style="color:Crimson">`ptrdiff_t`</span> (e.g. %td).
  * Pintos <span style="color:Crimson">`printf()`</span> also implements a nonstandard " ' " flag that groups large numbers with commas to make them easier to read.


## Unsafe String Functions

A few of the string functions declared in the standard `<string.h>` and `<stdio.h>` headers are notoriously unsafe. The worst offenders are intentionally not included in the Pintos C library (this is completely different from the standard C library, you can see this [FAQ](../project-description/lab0-booting/faq.md#kernel-monitor-faq) for details):

* <span style="color:CornFlowerBlue;">**strcpy**</span>
  * When used carelessly this function can overflow the buffer reserved for its output string.
  * Use **`strlcpy()`** instead. Refer to comments in its source code in `lib/string.c` for documentation.
* <span style="color:CornFlowerBlue;">**strncpy**</span>
  * This function can leave its destination buffer without a null string terminator. It also has performance problems.&#x20;
  * Again, use **`strlcpy()`**.
* <span style="color:CornFlowerBlue;">**strcat**</span>
  * Same issue as `strcpy()`.&#x20;
  * Use **`strlcat()`** instead. Again, refer to comments in its source code in `lib/string.c` for documentation.
* <span style="color:CornFlowerBlue;">**strncat**</span>
  * The meaning of its buffer size argument is surprising.&#x20;
  * Again, use **`strlcat()`**.
* <span style="color:CornFlowerBlue;">**strtok**</span>
  * Uses global data, so it is unsafe in threaded programs such as kernels.&#x20;
  * Use **`strtok_r()`** instead, and see its source code in `lib/string.c` for documentation and an example.
* <span style="color:CornFlowerBlue;">**sprintf**</span>
  * Same issue as `strcpy()`.
  * Use **`snprintf()`** instead. Refer to comments in `lib/stdio.h` for documentation.
* <span style="color:CornFlowerBlue;">**vsprintf**</span>
  * Same issue as `strcpy()`.
  * Use **`vsnprintf()`** instead.

If you try to use any of these functions, **the error message will give you a hint** by referring to an identifier like `dont_use_sprintf_use_snprintf`.
