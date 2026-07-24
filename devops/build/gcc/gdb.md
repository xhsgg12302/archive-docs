---
---

* ## Intro(GDB | gdb)

    > [?] hello GDB

    + ### x command

        ```sh
        (gdb) p/s (char *)0xffffd1f7
        $17 = 0xffffd1f7 "hello"
        (gdb) x/s 0xffffd1f7
        0xffffd1f7:     "hello"
        (gdb) 

        (gdb) x/32xw 0xff83765a-40
        ```

* ## Reference
    + https://sourceware.org/gdb/download/onlinedocs/gdb.html/index.html#SEC_Contents