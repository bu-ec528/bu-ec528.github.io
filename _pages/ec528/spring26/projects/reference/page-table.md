---
title: ""
permalink: /ec528/fall25/projects/reference/page_table
author_profile: false  
classes: ec528-page
layout: single
---


# Page Table

In Pintos, a page table is a data structure that the CPU uses to translate a virtual address to a physical address, that is, from a page to a frame. The page table format is dictated by the 80x86 architecture. Pintos provides page table management code in pagedir.c

The diagram below illustrates the relationship between pages and frames. The virtual address, on the left, consists of a page number and an offset. The page table translates the page number into a frame number, which is combined with the unmodified offset to obtain the physical address, on the right.

```	
                          +----------+
         .--------------->|Page Table|---------.
        /                 +----------+          |
   31   |   12 11    0                    31    V   12 11    0
  +-----------+-------+                  +------------+-------+
  |  Page Nr  |  Ofs  |                  |  Frame Nr  |  Ofs  |
  +-----------+-------+                  +------------+-------+
   Virt Addr      |                       Phys Addr       ^
                   \_____________________________________/
```

The code in `pagedir.c` is an abstract interface to the 80x86 hardware page table, also called a "page directory" by Intel processor documentation.

* The page table interface uses a **`uint32_t *`** to represent a page table because this is convenient for accessing their internal structure.
* The sections below describe the page table interface and internals.

## 1 Creation, Destruction, and Activation

These functions create, destroy, and activate page tables. The base Pintos code already calls these functions where necessary, so **it should not be necessary to call them yourself**.

* <span style="color:CornFlowerBlue;">**Function: uint32\_t \*pagedir\_create (void)**</span>
  * Creates and returns a new page table. The new page table contains Pintos's normal kernel virtual page mappings, but no user virtual mappings.
  * Returns a null pointer if memory cannot be obtained.
* <span style="color:CornFlowerBlue;">**Function: void pagedir\_destroy (uint32\_t \*pd)**</span>
  * Frees all of the resources held by *pd*, including the page table itself and the frames that it maps.
* <span style="color:CornFlowerBlue;">**Function: void pagedir\_activate (uint32\_t \*pd)**</span>
  * Activates *pd*. The active page table is the one used by the CPU to translate memory references.

## 2.Inspection and Updates

These functions examine or update the mappings from pages to frames encapsulated by a page table. They work on both active and inactive page tables (that is, those for running and suspended processes), flushing the TLB as necessary.

* <span style="color:CornFlowerBlue;">**Function: bool pagedir\_set\_page (uint32\_t \*pd, void \*upage, void \*kpage, bool writable)**</span>
  * Adds to *pd* ;a mapping from user page *upage*; to the frame identified by kernel virtual address *kpage*. If *writable* is true, the page is mapped read/write; otherwise, it is mapped read-only.
  * User page _upage_ must not already be mapped in _pd_.
  * Kernel page *kpage_ should be a kernel virtual address obtained from the user pool with `palloc_get_page(PAL_USER)`.
  * Returns true if successful, false on failure. Failure will occur if additional memory required for the page table cannot be obtained.
* <span style="color:CornFlowerBlue;">**Function: void \*pagedir\_get\_page (uint32\_t \*pd, const void \*uaddr)**</span>
  * Looks up the frame mapped to *uaddr* in *pd*.
  * Returns the kernel virtual address for that frame, if _uaddr_ is mapped, or a null pointer if it is not.
* <span style="color:CornFlowerBlue;">**Function: void pagedir\_clear\_page (uint32\_t \*pd, void \*page)**</span>
  * Marks *page* "not present" in *pd*. Later accesses to the _page_ will fault.
  * Other bits in the page table for *page* are preserved, permitting the accessed and dirty bits (see the next section) to be checked.
  * This function has no effect if _page_ is not mapped.

## 3.Accessed and Dirty Bits

80x86 hardware provides some assistance for implementing page replacement algorithms, through a pair of bits in the page table entry (PTE) for each page.

* On **any read or write** to a page, the CPU **sets the *accessed bit* to 1** in the page's PTE, and on **any write**, the CPU **sets the *dirty bit* to 1**.
* <span style="color:red;">**The CPU never resets these bits to 0, but the OS may do so.**</span>
* Proper interpretation of these bits requires understanding of *aliases*, that is, two (or more) pages that refer to the same frame. When an aliased frame is accessed, the accessed and dirty bits are updated in only one page table entry (the one for the page used for access). The accessed and dirty bits for the other aliases are not updated.
* In project 3, you will apply these bits in implementing **page replacement algorithms**.

The followings are some functions related to accessed and dirty bits:

* <span style="color:CornFlowerBlue;">**Function: bool pagedir\_is\_dirty (uint32\_t \*pd, const void \*page)**</span>
* <span style="color:CornFlowerBlue;">**Function: bool pagedir\_is\_accessed (uint32\_t \*pd, const void \*page)**</span>
  * Returns true if page directory _pd_ contains a page table entry for _page_ that is spaned dirty (or accessed). Otherwise, returns false.
* <span style="color:CornFlowerBlue;">**Function: void pagedir\_set\_dirty (uint32\_t \*pd, const void \*page, bool value)**</span>
* <span style="color:CornFlowerBlue;">**Function: void pagedir\_set\_accessed (uint32\_t \*pd, const void \*page, bool value)**</span>
  * If page directory _pd_ has a page table entry for _page_, then its dirty (or accessed) bit is set to _value_.

## 4.Page Table Details

The functions provided with Pintos are sufficient to implement the projects. However, you may still find it worthwhile to understand the hardware page table format, so we'll go into a little detail in this section. 

### 4.1 **Structure**

* The top-level paging data structure is a page called the "page directory" (PD) arranged as an array of 1,024 32-bit page directory entries (PDEs), each of which represents 4 MB of virtual memory.
* Each PDE may point to the physical address of another page called a "page table" (PT) arranged, similarly, as an array of 1,024 32-bit page table entries (PTEs), each of which translates a single 4 kB virtual page to a physical page.

### 4.2 Address Translation

Translation of a virtual address into a physical address follows **the three-step process** illustrated in the diagram below:

<div class="notice--info" markdown="1">
**Hint**

Actually, virtual to physical translation on the 80x86 architecture occurs via an intermediate "linear address," but Pintos (and most modern 80x86 OSes) set up the CPU so that linear and virtual addresses are one and the same. Thus, you can effectively ignore this CPU feature.

</div>

1. **The most-significant 10 bits of the virtual address (bits 22...31) index the page directory.**
   * If the PDE is marked "present," the physical address of a page table is read from the PDE thus obtained.
   * If the PDE is marked "not present," then a page fault occurs.
2. **The next 10 bits of the virtual address (bits 12...21) index the page table.**
   * If the PTE is marked "present," the physical address of a data page is read from the PTE thus obtained.
   * If the PTE is marked "not present," then a page fault occurs.
3. **The least-significant 12 bits of the virtual address (bits 0...11) are added to the data page's physical base address, yielding the final physical address.**

```
 31                  22 21                  12 11                   0
+----------------------+----------------------+----------------------+
| Page Directory Index |   Page Table Index   |    Page Offset       |
+----------------------+----------------------+----------------------+
             |                    |                     |
     _______/             _______/                _____/
    /                    /                       /
   /    Page Directory  /      Page Table       /    Data Page
  /     .____________. /     .____________.    /   .____________.
  |1,023|____________| |1,023|____________|    |   |____________|
  |1,022|____________| |1,022|____________|    |   |____________|
  |1,021|____________| |1,021|____________|    \__\|____________|
  |1,020|____________| |1,020|____________|       /|____________|
  |     |            | |     |            |        |            |
  |     |            | \____\|            |_       |            |
  |     |      .     |      /|      .     | \      |      .     |
  \____\|      .     |_      |      .     |  |     |      .     |
       /|      .     | \     |      .     |  |     |      .     |
        |      .     |  |    |      .     |  |     |      .     |
        |            |  |    |            |  |     |            |
        |____________|  |    |____________|  |     |____________|
       4|____________|  |   4|____________|  |     |____________|
       3|____________|  |   3|____________|  |     |____________|
       2|____________|  |   2|____________|  |     |____________|
       1|____________|  |   1|____________|  |     |____________|
       0|____________|  \__\0|____________|  \____\|____________|
                           /                      /
```

Pintos provides some macros and functions that are useful for working with raw page tables:

<details markdown="1">

<summary>Macros and Functions for Page Tables</summary>

* <span style="color:CornFlowerBlue;">**Macro: PTSHIFT**</span>
* <span style="color:CornFlowerBlue;">**Macro: PTBITS**</span>
  * The starting bit index (12) and number of bits (10), respectively, in a page table index.
* <span style="color:CornFlowerBlue;">**Macro: PTMASK**</span>
  * A bit mask with the bits in the page table index set to 1 and the rest set to 0 (0x3ff000).
* <span style="color:CornFlowerBlue;">**Macro: PTSPAN**</span>
  * The number of bytes of virtual address space that a single page table page covers (4,194,304 bytes, or 4 MB).
* <span style="color:CornFlowerBlue;">**Macro: PDSHIFT**</span>
* <span style="color:CornFlowerBlue;">**Macro: PDBITS**</span>
  * The starting bit index (22) and number of bits (10), respectively, in a page directory index.
* <span style="color:CornFlowerBlue;">**Macro: PDMASK**</span>
  * A bit mask with the bits in the page directory index set to 1 and other bits set to 0 (0xffc00000).
* <span style="color:CornFlowerBlue;">**Function: uintptr\_t pd\_no (const void \*va)**</span>
* <span style="color:CornFlowerBlue;">**Function: uintptr\_t pt\_no (const void \*va)**</span>
  * Returns the page directory index or page table index, respectively, for virtual address *va*. These functions are defined in `threads/pte.h`.
* <span style="color:CornFlowerBlue;">**Function: unsigned pg\_ofs (const void \*va)**</span>
  * Returns the page offset for virtual address *va*. This function is defined in `threads/vaddr.h`.

</details>

### 4.3 Page Table Entry Format 

You do not need to understand the PTE format to do the Pintos projects, unless you wish to incorporate the page table into your supplemental page table in project 3.

The actual format of a page table entry is summarized below..

```
 31                                   12 11 9      6 5     2 1 0
+---------------------------------------+----+----+-+-+---+-+-+-+
|           Physical Address            | AVL|    |D|A|   |U|W|P|
+---------------------------------------+----+----+-+-+---+-+-+-+
```

Some more information on each bit is given below. The names are **`threads/pte.h`** macros that represent the bits' values:

<details markdown="1">

<summary>Macros and Functions for Page Table Entry</summary>

* <span style="color:CornFlowerBlue;">**Macro: PTE\_P**</span>
  * **Bit 0, the "present" bit.**
  * When this bit is 1, the other bits are interpreted as described below. When this bit is 0, any attempt to access the page will page fault. The remaining bits are then not used by the CPU and may be used by the OS for any purpose.
* <span style="color:CornFlowerBlue;">**Macro: PTE\_W**</span>
  * **Bit 1, the "read/write" bit.**
  * When it is 1, the page is writable. When it is 0, write attempts will page fault.
* <span style="color:CornFlowerBlue;">**Macro: PTE\_U**</span>
  * **Bit 2, the "user/supervisor" bit.**
  * When it is 1, user processes may access the page. When it is 0, only the kernel may access the page (user accesses will page fault).
  * Pintos clears this bit in PTEs for kernel virtual memory, to prevent user processes from accessing them.
* <span style="color:CornFlowerBlue;">**Macro: PTE\_A**</span>
  * **Bit 5, the "accessed" bit.**
  * See section [Accessed and Dirty Bits](page-table.md#3accessed-and-dirty-bits).
* <span style="color:CornFlowerBlue;">**Macro: PTE\_D**</span>
  * **Bit 6, the "dirty" bit.**
  * See section [Accessed and Dirty Bits](page-table.md#3accessed-and-dirty-bits).
* <span style="color:CornFlowerBlue;">**Macro: PTE\_AVL**</span>
  * **Bits 9...11, available for operating system use.**
  * Pintos, as provided, does not use them and sets them to 0.
* <span style="color:CornFlowerBlue;">**Macro: PTE\_ADDR**</span>
  * **Bits 12...31, the top 20 bits of the physical address of a frame.** The low 12 bits of the frame's address are always 0.
* **Other bits are either reserved or uninteresting in a Pintos context and should be set to 0.**

Header **`threads/pte.h`** defines three functions for working with page table entries:

* <span style="color:CornFlowerBlue;">**Function: uint32\_t pte\_create\_kernel (uint32\_t \*page, bool writable)**</span>
  * Returns a page table entry that points to*page*, which should be a kernel virtual address.
  * The PTE's present bit will be set. It will be marked for kernel-only access.
  * If _writable_ is true, the PTE will also be marked read/write; otherwise, it will be read-only.
* <span style="color:CornFlowerBlue;">**Function: uint32\_t pte\_create\_user (uint32\_t \*page, bool writable)**</span>
  * Returns a page table entry that points to*page*, which should be the kernel virtual address of a frame in the user pool.
  * The PTE's present bit will be set and it will be marked to allow user-mode access.
  * If writable is true, the PTE will also be marked read/write; otherwise, it will be read-only.
* <span style="color:CornFlowerBlue;">**Function: void \*pte\_get\_page (uint32\_t pte)**</span>
  * Returns the kernel*virtual**_ **address for the frame that *pte* points to.
  * The _pte_ may be present or not-present; if it is not-present then the pointer returned is only meaningful if the address bits in the PTE actually represent a physical address.

</details>

### 4.4 Page Directory Entry Format

Page directory entries have the same format as PTEs, except that the physical address points to a page table page instead of a frame. Header **`threads/pte.h`** defines two functions for working with page directory entries:

* <span style="color:CornFlowerBlue;">**Function: uint32\_t pde\_create (uint32\_t \*pt)**</span>
  * Returns a page directory that points to*pt*, which should be the kernel virtual address of a page table page.
  * The PDE's present bit will be set, it will be marked to allow user-mode access, and it will be marked read/write.
* <span style="color:CornFlowerBlue;">**Function: uint32\_t \*pde\_get\_pt (uint32\_t pde)**</span>
  * Returns the kernel virtual address for the page table page that*pde*, which must be marked present, points to.
