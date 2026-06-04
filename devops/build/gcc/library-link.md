---
tocEndLevel: 5
---

* ## Intro(DLL | SO | 静态链接库 | 动态链接库 | 程序员的自我修养--链接、装载与库(高清带完整书签版).pdf.[阅读笔记])

    + ### 使用工具

        | - | [readelf](#readelf) | [objdump](#objdump) | [nm](#nm) |
        | -- | -- | -- | -- |
        | 查看段头 section-header   | `readelf -S SimpleSection.o` <br> ![](/.images/devops/build/gcc/library-link/ll-section-readelf-01.png ':size=10%') | `objdump -h SimpleSection.o` <br> ![](/.images/devops/build/gcc/library-link/ll-section-objdump-01.png ':size=10%')  | -- |
        | 查看代码 .text.* | -- | `objdump -d -S -w build/a.o` | -- |
        | 查看重定位 .rel* | `readelf -r -W build/Lib.so` | `objdump -r -w build/a.o` | -- |
        | 查看动态符号 .dynsym       | `readelf --dyn-syms -W  /bin/cat` <br> `readelf -sD -W  /bin/cat` | `objdump -t -w build/ab` | -- |
        | 查看版本 .gnu.*             | `readelf -V -W /bin/cat` | -- | -- |
        | 查看动态字符表 .dynstr     | `readelf -p .dynstr  /bin/cat` | -- | -- |
        | 查看跳转 .plt | -- | `objdump -d -j .plt /bin/cat` | -- |
        | 查看符号 | -- | -- | -- |

        另外还有，file、size等

        <!-- tabs:start -->
        #### **readelf**
        ```sh [data-file:GNU readelf (GNU Binutils for Ubuntu) 2.42,data-cc:600px]
        vscode ➜ /workspaces/c_learning (master) $ readelf --help
        Usage: readelf <option(s)> elf-file(s)
         Display information about the contents of ELF format files
         Options are:
          -a --all               Equivalent to: -h -l -S -s -r -d -V -A -I
          -h --file-header       Display the ELF file header
          -l --program-headers   Display the program headers
             --segments          An alias for --program-headers
          -S --section-headers   Display the sections' header
             --sections          An alias for --section-headers
          -g --section-groups    Display the section groups
          -t --section-details   Display the section details
          -e --headers           Equivalent to: -h -l -S
          -s --syms              Display the symbol table
             --symbols           An alias for --syms
             --dyn-syms          Display the dynamic symbol table
             --lto-syms          Display LTO symbol tables
             --sym-base=[0|8|10|16] 
                                 Force base for symbol sizes.  The options are 
                                 mixed (the default), octal, decimal, hexadecimal.
          -C --demangle[=STYLE]  Decode mangled/processed symbol names
                                   STYLE can be "none", "auto", "gnu-v3", "java",
                                   "gnat", "dlang", "rust"
             --no-demangle       Do not demangle low-level symbol names.  (default)
             --recurse-limit     Enable a demangling recursion limit.  (default)
             --no-recurse-limit  Disable a demangling recursion limit
             -U[dlexhi] --unicode=[default|locale|escape|hex|highlight|invalid]
                                 Display unicode characters as determined by the current locale
                                  (default), escape sequences, "<hex sequences>", highlighted
                                  escape sequences, or treat them as invalid and display as
                                  "{hex sequences}"
             -X --extra-sym-info Display extra information when showing symbols
             --no-extra-sym-info Do not display extra information when showing symbols (default)
          -n --notes             Display the contents of note sections (if present)
          -r --relocs            Display the relocations (if present)
          -u --unwind            Display the unwind info (if present)
          -d --dynamic           Display the dynamic section (if present)
          -V --version-info      Display the version sections (if present)
          -A --arch-specific     Display architecture specific information (if any)
          -c --archive-index     Display the symbol/file index in an archive
          -D --use-dynamic       Use the dynamic section info when displaying symbols
          -L --lint|--enable-checks
                                 Display warning messages for possible problems
          -x --hex-dump=<number|name>
                                 Dump the contents of section <number|name> as bytes
          -p --string-dump=<number|name>
                                 Dump the contents of section <number|name> as strings
          -R --relocated-dump=<number|name>
                                 Dump the relocated contents of section <number|name>
          -z --decompress        Decompress section before dumping it
          -w --debug-dump[a/=abbrev, A/=addr, r/=aranges, c/=cu_index, L/=decodedline,
                          f/=frames, F/=frames-interp, g/=gdb_index, i/=info, o/=loc,
                          m/=macro, p/=pubnames, t/=pubtypes, R/=Ranges, l/=rawline,
                          s/=str, O/=str-offsets, u/=trace_abbrev, T/=trace_aranges,
                          U/=trace_info]
                                 Display the contents of DWARF debug sections
          -wk --debug-dump=links Display the contents of sections that link to separate
                                  debuginfo files
          -P --process-links     Display the contents of non-debug sections in separate
                                  debuginfo files.  (Implies -wK)
          -wK --debug-dump=follow-links
                                 Follow links to separate debug info files (default)
          -wN --debug-dump=no-follow-links
                                 Do not follow links to separate debug info files
          --dwarf-depth=N        Do not display DIEs at depth N or greater
          --dwarf-start=N        Display DIEs starting at offset N
          --ctf=<number|name>    Display CTF info from section <number|name>
          --ctf-parent=<name>    Use CTF archive member <name> as the CTF parent
          --ctf-symbols=<number|name>
                                 Use section <number|name> as the CTF external symtab
          --ctf-strings=<number|name>
                                 Use section <number|name> as the CTF external strtab
          --sframe[=NAME]        Display SFrame info from section NAME, (default '.sframe')
          -I --histogram         Display histogram of bucket list lengths
          -W --wide              Allow output width to exceed 80 characters
          -T --silent-truncation If a symbol name is truncated, do not add [...] suffix
          @<file>                Read options from <file>
          -H --help              Display this information
          -v --version           Display the version number of readelf
        Report bugs to <https://sourceware.org/bugzilla/>
        ```

        #### **objdump**
        ```sh [data-file:GNU objdump (GNU Binutils for Ubuntu) 2.42,data-cc:600px]
        vscode ➜ /workspaces/c_learning (master) $ objdump --help
        Usage: objdump <option(s)> <file(s)>
         Display information from object <file(s)>.
         At least one of the following switches must be given:
          -a, --archive-headers    Display archive header information
          -f, --file-headers       Display the contents of the overall file header
          -p, --private-headers    Display object format specific file header contents
          -P, --private=OPT,OPT... Display object format specific contents
          -h, --[section-]headers  Display the contents of the section headers
          -x, --all-headers        Display the contents of all headers
          -d, --disassemble        Display assembler contents of executable sections
          -D, --disassemble-all    Display assembler contents of all sections
              --disassemble=<sym>  Display assembler contents from <sym>
          -S, --source             Intermix source code with disassembly
              --source-comment[=<txt>] Prefix lines of source code with <txt>
          -s, --full-contents      Display the full contents of all sections requested
          -Z, --decompress         Decompress section(s) before displaying their contents
          -g, --debugging          Display debug information in object file
          -e, --debugging-tags     Display debug information using ctags style
          -G, --stabs              Display (in raw form) any STABS info in the file
          -W, --dwarf[a/=abbrev, A/=addr, r/=aranges, c/=cu_index, L/=decodedline,
                      f/=frames, F/=frames-interp, g/=gdb_index, i/=info, o/=loc,
                      m/=macro, p/=pubnames, t/=pubtypes, R/=Ranges, l/=rawline,
                      s/=str, O/=str-offsets, u/=trace_abbrev, T/=trace_aranges,
                      U/=trace_info]
                                   Display the contents of DWARF debug sections
          -Wk,--dwarf=links        Display the contents of sections that link to
                                    separate debuginfo files
          -WK,--dwarf=follow-links
                                   Follow links to separate debug info files (default)
          -WN,--dwarf=no-follow-links
                                   Do not follow links to separate debug info files
          -L, --process-links      Display the contents of non-debug sections in
                                    separate debuginfo files.  (Implies -WK)
              --ctf[=SECTION]      Display CTF info from SECTION, (default `.ctf')
              --sframe[=SECTION]   Display SFrame info from SECTION, (default '.sframe')
          -t, --syms               Display the contents of the symbol table(s)
          -T, --dynamic-syms       Display the contents of the dynamic symbol table
          -r, --reloc              Display the relocation entries in the file
          -R, --dynamic-reloc      Display the dynamic relocation entries in the file
          @<file>                  Read options from <file>
          -v, --version            Display this program's version number
          -i, --info               List object formats and architectures supported
          -H, --help               Display this information
        
         The following switches are optional:
          -b, --target=BFDNAME           Specify the target object format as BFDNAME
          -m, --architecture=MACHINE     Specify the target architecture as MACHINE
          -j, --section=NAME             Only display information for section NAME
          -M, --disassembler-options=OPT Pass text OPT on to the disassembler
          -EB --endian=big               Assume big endian format when disassembling
          -EL --endian=little            Assume little endian format when disassembling
              --file-start-context       Include context from start of file (with -S)
          -I, --include=DIR              Add DIR to search list for source files
          -l, --line-numbers             Include line numbers and filenames in output
          -F, --file-offsets             Include file offsets when displaying information
          -C, --demangle[=STYLE]         Decode mangled/processed symbol names
                                           STYLE can be "none", "auto", "gnu-v3",
                                           "java", "gnat", "dlang", "rust"
              --recurse-limit            Enable a limit on recursion whilst demangling
                                          (default)
              --no-recurse-limit         Disable a limit on recursion whilst demangling
          -w, --wide                     Format output for more than 80 columns
          -U[d|l|i|x|e|h]                Controls the display of UTF-8 unicode characters
          --unicode=[default|locale|invalid|hex|escape|highlight]
          -z, --disassemble-zeroes       Do not skip blocks of zeroes when disassembling
              --start-address=ADDR       Only process data whose address is >= ADDR
              --stop-address=ADDR        Only process data whose address is < ADDR
              --no-addresses             Do not print address alongside disassembly
              --prefix-addresses         Print complete address alongside disassembly
              --[no-]show-raw-insn       Display hex alongside symbolic disassembly
              --insn-width=WIDTH         Display WIDTH bytes on a single line for -d
              --adjust-vma=OFFSET        Add OFFSET to all displayed section addresses
              --show-all-symbols         When disassembling, display all symbols at a given address
              --special-syms             Include special symbols in symbol dumps
              --inlines                  Print all inlines for source line (with -l)
              --prefix=PREFIX            Add PREFIX to absolute paths for -S
              --prefix-strip=LEVEL       Strip initial directory names for -S
              --dwarf-depth=N            Do not display DIEs at depth N or greater
              --dwarf-start=N            Display DIEs starting at offset N
              --dwarf-check              Make additional dwarf consistency checks.
              --ctf-parent=NAME          Use CTF archive member NAME as the CTF parent
              --visualize-jumps          Visualize jumps by drawing ASCII art lines
              --visualize-jumps=color    Use colors in the ASCII art
              --visualize-jumps=extended-color
                                         Use extended 8-bit color codes
              --visualize-jumps=off      Disable jump visualization
              --disassembler-color=off       Disable disassembler color output. (default)
              --disassembler-color=terminal  Enable disassembler color output if displaying on a terminal.
              --disassembler-color=on        Enable disassembler color output.
              --disassembler-color=extended  Use 8-bit colors in disassembler output.
        
        objdump: supported targets: elf64-x86-64 elf32-i386 elf32-iamcu elf32-x86-64 pei-i386 pe-x86-64 pei-x86-64 elf64-little elf64-big elf32-little elf32-big pe-bigobj-x86-64 pe-i386 pdb srec symbolsrec verilog tekhex binary ihex plugin
        objdump: supported architectures: i386 i386:x86-64 i386:x64-32 i8086 i386:intel i386:x86-64:intel i386:x64-32:intel iamcu iamcu:intel
        
        The following i386/x86-64 specific disassembler options are supported for use
        with the -M switch (multiple options should be separated by commas):
          x86-64      Disassemble in 64bit mode
          i386        Disassemble in 32bit mode
          i8086       Disassemble in 16bit mode
          att         Display instruction in AT&T syntax
          intel       Display instruction in Intel syntax
          att-mnemonic  (AT&T syntax only)
                      Display instruction with AT&T mnemonic
          intel-mnemonic  (AT&T syntax only)
                      Display instruction with Intel mnemonic
          addr64      Assume 64bit address size
          addr32      Assume 32bit address size
          addr16      Assume 16bit address size
          data32      Assume 32bit data size
          data16      Assume 16bit data size
          suffix      Always display instruction suffix in AT&T syntax
          amd64       Display instruction in AMD64 ISA
          intel64     Display instruction in Intel64 ISA
        
        Options supported for -P/--private switch:
        For PE files:
          header      Display the file header
          sections    Display the section headers
        Report bugs to <https://sourceware.org/bugzilla/>.
        ```

        #### **nm**
        ```sh [data-file:GNU nm (GNU Binutils for Ubuntu) 2.42,data-cc:600px]
        vscode ➜ /workspaces/c_learning (master) $ nm --help
        Usage: nm [option(s)] [file(s)]
         List symbols in [file(s)] (a.out by default).
         The options are:
          -a, --debug-syms       Display debugger-only symbols
          -A, --print-file-name  Print name of the input file before every symbol
          -B                     Same as --format=bsd
          -C, --demangle[=STYLE] Decode mangled/processed symbol names
                                   STYLE can be "none", "auto", "gnu-v3", "java",
                                   "gnat", "dlang", "rust"
              --no-demangle      Do not demangle low-level symbol names
              --recurse-limit    Enable a demangling recursion limit.  (default)
              --no-recurse-limit Disable a demangling recursion limit.
          -D, --dynamic          Display dynamic symbols instead of normal symbols
          -e                     (ignored)
          -f, --format=FORMAT    Use the output format FORMAT.  FORMAT can be `bsd',
                                   `sysv', `posix' or 'just-symbols'.
                                   The default is `bsd'
          -g, --extern-only      Display only external symbols
            --ifunc-chars=CHARS  Characters to use when displaying ifunc symbols
          -j, --just-symbols     Same as --format=just-symbols
          -l, --line-numbers     Use debugging information to find a filename and
                                   line number for each symbol
          -n, --numeric-sort     Sort symbols numerically by address
          -o                     Same as -A
          -p, --no-sort          Do not sort the symbols
          -P, --portability      Same as --format=posix
          -r, --reverse-sort     Reverse the sense of the sort
              --plugin NAME      Load the specified plugin
          -S, --print-size       Print size of defined symbols
          -s, --print-armap      Include index for symbols from archive members
              --quiet            Suppress "no symbols" diagnostic
              --size-sort        Sort symbols by size
              --special-syms     Include special symbols in the output
              --synthetic        Display synthetic symbols as well
          -t, --radix=RADIX      Use RADIX for printing symbol values
              --target=BFDNAME   Specify the target object format as BFDNAME
          -u, --undefined-only   Display only undefined symbols
          -U, --defined-only     Display only defined symbols
              --unicode={default|show|invalid|hex|escape|highlight}
                                 Specify how to treat UTF-8 encoded unicode characters
          -W, --no-weak          Ignore weak symbols
              --without-symbol-versions  Do not display version strings after symbol names
          -X 32_64               (ignored)
          @FILE                  Read options from FILE
          -h, --help             Display this information
          -V, --version          Display this program's version number
        nm: supported targets: elf64-x86-64 elf32-i386 elf32-iamcu elf32-x86-64 pei-i386 pe-x86-64 pei-x86-64 elf64-little elf64-big elf32-little elf32-big pe-bigobj-x86-64 pe-i386 pdb srec symbolsrec verilog tekhex binary ihex plugin
        Report bugs to <https://sourceware.org/bugzilla/>.
        ```

        #### **ld**
        ```sh [data-file:GNU ld (GNU Binutils for Ubuntu) 2.42,data-cc:600px]
        vscode ➜ /workspaces/c_learning (master) $ ld --help
        Usage: ld [options] file...
        Options:
          -a KEYWORD                  Shared library control for HP/UX compatibility
          -A ARCH, --architecture ARCH
                                      Set architecture
          -b TARGET, --format TARGET  Specify target for following input files
          -c FILE, --mri-script FILE  Read MRI format linker script
          -d, -dc, -dp                Force common symbols to be defined
          --dependency-file FILE      Write dependency file
          --force-group-allocation    Force group members out of groups
          -e ADDRESS, --entry ADDRESS Set start address
          -E, --export-dynamic        Export all dynamic symbols
          --no-export-dynamic         Undo the effect of --export-dynamic
          --enable-non-contiguous-regions
                                      Enable support of non-contiguous memory regions
          --enable-non-contiguous-regions-warnings
                                      Enable warnings when --enable-non-contiguous-regions may cause unexpected behaviour
          --disable-linker-version    Disable the LINKER_VERSION linker script directive
          --enable-linker-version     Enable the LINKER_VERSION linker script directive
          -EB                         Link big-endian objects
          -EL                         Link little-endian objects
          -f SHLIB, --auxiliary SHLIB Auxiliary filter for shared object symbol table
          -F SHLIB, --filter SHLIB    Filter for shared object symbol table
          -g                          Ignored
          -G SIZE, --gpsize SIZE      Small data size (if no size, same as --shared)
          -h FILENAME, -soname FILENAME
                                      Set internal name of shared library
          -I PROGRAM, --dynamic-linker PROGRAM
                                      Set PROGRAM as the dynamic linker to use
          --no-dynamic-linker         Produce an executable with no program interpreter header
          -l LIBNAME, --library LIBNAME
                                      Search for library LIBNAME
          -L DIRECTORY, --library-path DIRECTORY
                                      Add DIRECTORY to library search path
          --sysroot=<DIRECTORY>       Override the default sysroot location
          -m EMULATION                Set emulation
          -M, --print-map             Print map file on standard output
          -n, --nmagic                Do not page align data
          -N, --omagic                Do not page align data, do not make text readonly
          --no-omagic                 Page align data, make text readonly
          -o FILE, --output FILE      Set output file name
          -O                          Optimize output file
          --out-implib FILE           Generate import library
          -plugin PLUGIN              Load named plugin
          -plugin-opt ARG             Send arg to last-loaded plugin
          -flto                       Ignored for GCC LTO option compatibility
          -flto-partition=            Ignored for GCC LTO option compatibility
          -fuse-ld=                   Ignored for GCC linker option compatibility
          --map-whole-files           Ignored for gold option compatibility
          --no-map-whole-files        Ignored for gold option compatibility
          -Qy                         Ignored for SVR4 compatibility
          -q, --emit-relocs           Generate relocations in final output
          -r, -i, --relocatable       Generate relocatable output
          -R FILE, --just-symbols FILE
                                      Just link symbols (if directory, same as --rpath)
          --remap-inputs-file FILE    Provide a FILE containing input remapings
          --remap-inputs PATTERN=FILE Remap input files matching PATTERN to FILE
          -s, --strip-all             Strip all symbols
          -S, --strip-debug           Strip debugging symbols
          --strip-discarded           Strip symbols in discarded sections
          --no-strip-discarded        Do not strip symbols in discarded sections
          -t, --trace                 Trace file opens
          -T FILE, --script FILE      Read linker script
          --default-script FILE, -dT  Read default linker script
          -u SYMBOL, --undefined SYMBOL
                                      Start with undefined reference to SYMBOL
          --require-defined SYMBOL    Require SYMBOL be defined in the final output
          --unique [=SECTION]         Don't merge input [SECTION | orphan] sections
          -Ur                         Build global constructor/destructor tables
          -v, --version               Print version information
          -V                          Print version and emulation information
          -x, --discard-all           Discard all local symbols
          -X, --discard-locals        Discard temporary local symbols (default)
          --discard-none              Don't discard any local symbols
          -y SYMBOL, --trace-symbol SYMBOL
                                      Trace mentions of SYMBOL
          -Y PATH                     Default search path for Solaris compatibility
          -(, --start-group           Start a group
          -), --end-group             End a group
          --accept-unknown-input-arch Accept input files whose architecture cannot be determined
          --no-accept-unknown-input-arch
                                      Reject input files whose architecture is unknown
          --as-needed                 Only set DT_NEEDED for following dynamic libs if used
          --no-as-needed              Always set DT_NEEDED for dynamic libraries mentioned on
                                        the command line
          -assert KEYWORD             Ignored for SunOS compatibility
          -Bdynamic, -dy, -call_shared
                                      Link against shared libraries
          -Bstatic, -dn, -non_shared, -static
                                      Do not link against shared libraries
          -Bno-symbolic               Don't bind global references locally
          -Bsymbolic                  Bind global references locally
          -Bsymbolic-functions        Bind global function references locally
          --check-sections            Check section addresses for overlaps (default)
          --no-check-sections         Do not check section addresses for overlaps
          --copy-dt-needed-entries    Copy DT_NEEDED links mentioned inside DSOs that follow
          --no-copy-dt-needed-entries Do not copy DT_NEEDED links mentioned inside DSOs that follow
          --cref                      Output cross reference table
          --defsym SYMBOL=EXPRESSION  Define a symbol
          --demangle [=STYLE]         Demangle symbol names [using STYLE]
          --disable-multiple-abs-defs Do not allow multiple definitions with symbols included
                                        in filename invoked by -R or --just-symbols
          --embedded-relocs           Generate embedded relocs
          --fatal-warnings            Treat warnings as errors
          --no-fatal-warnings         Do not treat warnings as errors (default)
          -fini SYMBOL                Call SYMBOL at unload-time
          --force-exe-suffix          Force generation of file with .exe suffix
          --gc-sections               Remove unused sections (on some targets)
          --no-gc-sections            Don't remove unused sections (default)
          --print-gc-sections         List removed unused sections on stderr
          --no-print-gc-sections      Do not list removed unused sections
          --gc-keep-exported          Keep exported symbols when removing unused sections
          --hash-size=<NUMBER>        Set default hash table size close to <NUMBER>
          --help                      Print option help
          -init SYMBOL                Call SYMBOL at load-time
          -Map FILE/DIR               Write a linker map to FILE or DIR/<outputname>.map
          --no-define-common          Do not define Common storage
          --no-demangle               Do not demangle symbol names
          --no-keep-memory            Use less memory and more disk I/O
          --no-undefined              Do not allow unresolved references in object files
          -w, --no-warnings           Do not display any warning or error messages
          --allow-shlib-undefined     Allow unresolved references in shared libraries
          --no-allow-shlib-undefined  Do not allow unresolved references in shared libs
          --allow-multiple-definition Allow multiple definitions
          --error-handling-script SCRIPT
                                      Provide a script to help with undefined symbol errors
          --undefined-version         Allow undefined version
          --no-undefined-version      Disallow undefined version
          --default-symver            Create default symbol version
          --default-imported-symver   Create default symbol version for imported symbols
          --no-warn-mismatch          Don't warn about mismatched input files
          --no-warn-search-mismatch   Don't warn on finding an incompatible library
          --no-whole-archive          Turn off --whole-archive
          --noinhibit-exec            Create an output file even if errors occur
          -nostdlib                   Only use library directories specified on
                                        the command line
          --oformat TARGET            Specify target of output file
          --print-output-format       Print default output format
          --print-sysroot             Print current sysroot
          -qmagic                     Ignored for Linux compatibility
          --reduce-memory-overheads   Reduce memory overheads, possibly taking much longer
          --max-cache-size=SIZE       Set the maximum cache size to SIZE bytes
          --relax                     Reduce code size by using target specific optimizations
          --no-relax                  Do not use relaxation techniques to reduce code size
          --retain-symbols-file FILE  Keep only symbols listed in FILE
          -rpath PATH                 Set runtime shared library search path
          -rpath-link PATH            Set link time shared library search path
          -shared, -Bshareable        Create a shared library
          -pie, --pic-executable      Create a position independent executable
          -no-pie                     Create a position dependent executable (default)
          --sort-common [=ascending|descending]
                                      Sort common symbols by alignment [in specified order]
          --sort-section name|alignment
                                      Sort sections by name or maximum alignment
          --spare-dynamic-tags COUNT  How many tags to reserve in .dynamic section
          --split-by-file [=SIZE]     Split output sections every SIZE octets
          --split-by-reloc [=COUNT]   Split output sections every COUNT relocs
          --stats                     Print memory usage statistics
          --target-help               Display target specific options
          --task-link SYMBOL          Do task level linking
          --traditional-format        Use same format as native linker
          --section-start SECTION=ADDRESS
                                      Set address of named section
          -Tbss ADDRESS               Set address of .bss section
          -Tdata ADDRESS              Set address of .data section
          -Ttext ADDRESS              Set address of .text section
          -Ttext-segment ADDRESS      Set address of text segment
          -Trodata-segment ADDRESS    Set address of rodata segment
          -Tldata-segment ADDRESS     Set address of ldata segment
          --unresolved-symbols=<method>
                                      How to handle unresolved symbols.  <method> is:
                                        ignore-all, report-all, ignore-in-object-files,
                                        ignore-in-shared-libs
          --verbose [=NUMBER]         Output lots of information during link
          --version-script FILE       Read version information script
          --version-exports-section SYMBOL
                                      Take export symbols list from .exports, using
                                        SYMBOL as the version.
          --dynamic-list-data         Add data symbols to dynamic list
          --dynamic-list-cpp-new      Use C++ operator new/delete dynamic list
          --dynamic-list-cpp-typeinfo Use C++ typeinfo dynamic list
          --dynamic-list FILE         Read dynamic list
          --export-dynamic-symbol SYMBOL
                                      Export the specified symbol
          --export-dynamic-symbol-list FILE
                                      Read export dynamic symbol list
          --warn-common               Warn about duplicate common symbols
          --warn-constructors, --error-execstack, --no-error-execstack, --warn-execstack-objects, --warn-execstack, --no-warn-execstack, --error-rwx-segments, --no-error-rwx-segments, --warn-rwx-segments, --no-warn-rwx-segments
                                      Warn if global constructors/destructors are seen
          --warn-multiple-gp          Warn if the multiple GP values are used
          --warn-once                 Warn only once per undefined symbol
          --warn-section-align        Warn if start of section changes due to alignment
          --warn-textrel              Warn if output has DT_TEXTREL (default)
          --warn-alternate-em         Warn if an object has alternate ELF machine code
          --warn-unresolved-symbols   Report unresolved symbols as warnings
          --error-unresolved-symbols  Report unresolved symbols as errors
          --whole-archive             Include all objects from following archives
          --wrap SYMBOL               Use wrapper functions for SYMBOL
          --ignore-unresolved-symbol SYMBOL
                                      Unresolved SYMBOL will not cause an error or warning
          --push-state                Push state of flags governing input file handling
          --pop-state                 Pop state of flags governing input file handling
          --print-memory-usage        Report target memory usage
          --orphan-handling =MODE     Control how orphan sections are handled.
          --print-map-discarded       Show discarded sections in map file output (default)
          --no-print-map-discarded    Do not show discarded sections in map file output
          --print-map-locals          Show local symbols in map file output
          --no-print-map-locals       Do not show local symbols in map file output (default)
          --ctf-variables             Emit names and types of static variables in CTF
          --no-ctf-variables          Do not emit names and types of static variables in CTF
          --ctf-share-types=<method>  How to share CTF types between translation units.
                                        <method> is: share-unconflicted (default),
                                                     share-duplicated
          @FILE                       Read options from FILE
        ld: supported targets: elf64-x86-64 elf32-i386 elf32-iamcu elf32-x86-64 pei-i386 pe-x86-64 pei-x86-64 elf64-little elf64-big elf32-little elf32-big pe-bigobj-x86-64 pe-i386 pdb srec symbolsrec verilog tekhex binary ihex plugin
        ld: supported emulations: elf_x86_64 elf32_x86_64 elf_i386 elf_iamcu i386pep i386pe
        ld: emulation specific options:
        ELF emulations:
          --ld-generated-unwind-info  Generate exception handling info for PLT
          --no-ld-generated-unwind-info
                                      Don't generate exception handling info for PLT
          --build-id[=STYLE]          Generate build ID note
          --package-metadata[=JSON]   Generate package metadata note
          --compress-debug-sections=[none|zlib|zlib-gnu|zlib-gabi|zstd]
                                      Compress DWARF debug sections
                                        Default: none
          -z common-page-size=SIZE    Set common page size to SIZE
          -z max-page-size=SIZE       Set maximum page size to SIZE
          -z defs                     Report unresolved symbols in object files
          -z undefs                   Ignore unresolved symbols in object files
          -z muldefs                  Allow multiple definitions
          -z stack-size=SIZE          Set size of stack segment
          -z execstack                Mark executable as requiring executable stack
          -z noexecstack              Mark executable as not requiring executable stack
          --warn-execstack-objects    Generate a warning if an object file requests an executable stack
          --warn-execstack            Generate a warning if creating an executable stack (default)
          --no-warn-execstack         Do not generate a warning if creating an executable stack
          --error-execstack           Turn warnings about executable stacks into errors
          --no-error-execstack         Do not turn warnings about executable stacks into errors
          --warn-rwx-segments         Generate a warning if a LOAD segment has RWX permissions (default)
          --no-warn-rwx-segments      Do not generate a warning if a LOAD segments has RWX permissions
          --error-rwx-segments        Turn warnings about loadable RWX segments into errors
          --no-error-rwx-segments     Do not turn warnings about loadable RWX segments into errors
          -z unique-symbol            Avoid duplicated local symbol names
          -z nounique-symbol          Keep duplicated local symbol names (default)
          -z globalaudit              Mark executable requiring global auditing
          -z start-stop-gc            Enable garbage collection on __start/__stop
          -z nostart-stop-gc          Don't garbage collect __start/__stop (default)
          -z start-stop-visibility=V  Set visibility of built-in __start/__stop symbols
                                        to DEFAULT, PROTECTED, HIDDEN or INTERNAL
          -z sectionheader            Generate section header (default)
          -z nosectionheader          Do not generate section header
          --audit=AUDITLIB            Specify a library to use for auditing
          -Bgroup                     Selects group name lookup rules for DSO
          --disable-new-dtags         Disable new dynamic tags
          --enable-new-dtags          Enable new dynamic tags
          --eh-frame-hdr              Create .eh_frame_hdr section
          --no-eh-frame-hdr           Do not create .eh_frame_hdr section
          --exclude-libs=LIBS         Make all symbols in LIBS hidden
          --hash-style=STYLE          Set hash style to sysv/gnu/both.  Default: both
          -P AUDITLIB, --depaudit=AUDITLIB
                                      Specify a library to use for auditing dependencies
          -z combreloc                Merge dynamic relocs into one section and sort
          -z nocombreloc              Don't merge dynamic relocs into one section
          -z global                   Make symbols in DSO available for subsequently
                                        loaded objects
          -z initfirst                Mark DSO to be initialized first at runtime
          -z interpose                Mark object to interpose all DSOs but executable
          -z unique                   Mark DSO to be loaded at most once by default, and only in the main namespace
          -z nounique                 Don't mark DSO as a loadable at most once
          -z lazy                     Mark object lazy runtime binding (default)
          -z loadfltr                 Mark object requiring immediate process
          -z nocopyreloc              Don't create copy relocs
          -z nodefaultlib             Mark object not to use default search paths
          -z nodelete                 Mark DSO non-deletable at runtime
          -z nodlopen                 Mark DSO not available to dlopen
          -z nodump                   Mark DSO not available to dldump
          -z now                      Mark object non-lazy runtime binding
          -z origin                   Mark object requiring immediate $ORIGIN
                                        processing at runtime
          -z relro                    Create RELRO program header (default)
          -z norelro                  Don't create RELRO program header
          -z separate-code            Create separate code program header (default)
          -z noseparate-code          Don't create separate code program header
          -z common                   Generate common symbols with STT_COMMON type
          -z nocommon                 Generate common symbols with STT_OBJECT type
          -z text                     Treat DT_TEXTREL in output as error
          -z notext                   Don't treat DT_TEXTREL in output as error
          -z textoff                  Don't treat DT_TEXTREL in output as error
        elf_x86_64: 
          -z noextern-protected-data  Do not treat protected data symbol as external
          -z indirect-extern-access   Enable indirect external access
          -z noindirect-extern-access Disable indirect external access (default)
          -z dynamic-undefined-weak   Make undefined weak symbols dynamic
          -z nodynamic-undefined-weak Do not make undefined weak symbols dynamic
          -z noreloc-overflow         Disable relocation overflow check
          -z call-nop=PADDING         Use PADDING as 1-byte NOP for branch
          -z ibtplt                   Generate IBT-enabled PLT entries
          -z ibt                      Generate GNU_PROPERTY_X86_FEATURE_1_IBT
          -z shstk                    Generate GNU_PROPERTY_X86_FEATURE_1_SHSTK
          -z cet-report=[none|warning|error] (default: none)
                                      Report missing IBT and SHSTK properties
          -z report-relative-reloc    Report relative relocations
          -z x86-64-{baseline|v[234]} Mark x86-64-{baseline|v[234]} ISA level as needed
          -z lam-u48                  Generate GNU_PROPERTY_X86_FEATURE_1_LAM_U48
          -z lam-u48-report=[none|warning|error] (default: none)
                                      Report missing LAM_U48 property
          -z lam-u57                  Generate GNU_PROPERTY_X86_FEATURE_1_LAM_U57
          -z lam-u57-report=[none|warning|error] (default: none)
                                      Report missing LAM_U57 property
          -z lam-report=[none|warning|error] (default: none)
                                      Report missing LAM_U48 and LAM_U57 properties
          -z mark-plt                 Mark PLT with dynamic tags
          -z nomark-plt               Do not mark PLT with dynamic tags (default)
          -z pack-relative-relocs     Pack relative relocations
          -z nopack-relative-relocs   Do not pack relative relocations (default)
        elf32_x86_64: 
          -z noextern-protected-data  Do not treat protected data symbol as external
          -z indirect-extern-access   Enable indirect external access
          -z noindirect-extern-access Disable indirect external access (default)
          -z dynamic-undefined-weak   Make undefined weak symbols dynamic
          -z nodynamic-undefined-weak Do not make undefined weak symbols dynamic
          -z noreloc-overflow         Disable relocation overflow check
          -z call-nop=PADDING         Use PADDING as 1-byte NOP for branch
          -z ibtplt                   Generate IBT-enabled PLT entries
          -z ibt                      Generate GNU_PROPERTY_X86_FEATURE_1_IBT
          -z shstk                    Generate GNU_PROPERTY_X86_FEATURE_1_SHSTK
          -z cet-report=[none|warning|error] (default: none)
                                      Report missing IBT and SHSTK properties
          -z report-relative-reloc    Report relative relocations
          -z x86-64-{baseline|v[234]} Mark x86-64-{baseline|v[234]} ISA level as needed
          -z mark-plt                 Mark PLT with dynamic tags
          -z nomark-plt               Do not mark PLT with dynamic tags (default)
          -z pack-relative-relocs     Pack relative relocations
          -z nopack-relative-relocs   Do not pack relative relocations (default)
        elf_i386: 
          -z noextern-protected-data  Do not treat protected data symbol as external
          -z indirect-extern-access   Enable indirect external access
          -z noindirect-extern-access Disable indirect external access (default)
          -z dynamic-undefined-weak   Make undefined weak symbols dynamic
          -z nodynamic-undefined-weak Do not make undefined weak symbols dynamic
          -z call-nop=PADDING         Use PADDING as 1-byte NOP for branch
          -z ibtplt                   Generate IBT-enabled PLT entries
          -z ibt                      Generate GNU_PROPERTY_X86_FEATURE_1_IBT
          -z shstk                    Generate GNU_PROPERTY_X86_FEATURE_1_SHSTK
          -z cet-report=[none|warning|error] (default: none)
                                      Report missing IBT and SHSTK properties
          -z report-relative-reloc    Report relative relocations
          -z x86-64-{baseline|v[234]} Mark x86-64-{baseline|v[234]} ISA level as needed
          -z pack-relative-relocs     Pack relative relocations
          -z nopack-relative-relocs   Do not pack relative relocations (default)
        elf_iamcu: 
          -z noextern-protected-data  Do not treat protected data symbol as external
          -z indirect-extern-access   Enable indirect external access
          -z noindirect-extern-access Disable indirect external access (default)
          -z dynamic-undefined-weak   Make undefined weak symbols dynamic
          -z nodynamic-undefined-weak Do not make undefined weak symbols dynamic
          -z call-nop=PADDING         Use PADDING as 1-byte NOP for branch
        i386pep: 
          --base_file <basefile>             Generate a base file for relocatable DLLs
          --dll                              Set image base to the default for DLLs
          --file-alignment <size>            Set file alignment
          --heap <size>                      Set initial size of the heap
          --image-base <address>             Set start address of the executable
          --major-image-version <number>     Set version number of the executable
          --major-os-version <number>        Set minimum required OS version
          --major-subsystem-version <number> Set minimum required OS subsystem version
          --minor-image-version <number>     Set revision number of the executable
          --minor-os-version <number>        Set minimum required OS revision
          --minor-subsystem-version <number> Set minimum required OS subsystem revision
          --section-alignment <size>         Set section alignment
          --stack <size>                     Set size of the initial stack
          --subsystem <name>[:<version>]     Set required OS subsystem [& version]
          --support-old-code                 Support interworking with old code
          --[no-]leading-underscore          Set explicit symbol underscore prefix mode
          --[no-]insert-timestamp            Use a real timestamp rather than zero (default)
                                             This makes binaries non-deterministic
          --add-stdcall-alias                Export symbols with and without @nn
          --disable-stdcall-fixup            Don't link _sym to _sym@nn
          --enable-stdcall-fixup             Link _sym to _sym@nn without warnings
          --exclude-symbols sym,sym,...      Exclude symbols from automatic export
          --exclude-all-symbols              Exclude all symbols from automatic export
          --exclude-libs lib,lib,...         Exclude libraries from automatic export
          --exclude-modules-for-implib mod,mod,...
                                             Exclude objects, archive members from auto
                                             export, place into import library instead
          --export-all-symbols               Automatically export all globals to DLL
          --kill-at                          Remove @nn from exported symbols
          --output-def <file>                Generate a .DEF file for the built DLL
          --warn-duplicate-exports           Warn about duplicate exports
          --compat-implib                    Create backward compatible import libs;
                                               create __imp_<SYMBOL> as well
          --enable-auto-image-base           Automatically choose image base for DLLs
                                               unless user specifies one
          --disable-auto-image-base          Do not auto-choose image base (default)
          --dll-search-prefix=<string>       When linking dynamically to a dll without
                                               an importlib, use <string><basename>.dll
                                               in preference to lib<basename>.dll 
          --enable-auto-import               Do sophisticated linking of _sym to
                                               __imp_sym for DATA references
          --disable-auto-import              Do not auto-import DATA items from DLLs
          --enable-runtime-pseudo-reloc      Work around auto-import limitations by
                                               adding pseudo-relocations resolved at
                                               runtime
          --disable-runtime-pseudo-reloc     Do not add runtime pseudo-relocations for
                                               auto-imported DATA
          --enable-extra-pep-debug            Enable verbose debug output when building
                                               or linking to DLLs (esp. auto-import)
          --enable-long-section-names        Use long COFF section names even in
                                               executable image files
          --disable-long-section-names       Never use long COFF section names, even
                                               in object files
          --[disable-]high-entropy-va        Image is compatible with 64-bit address space
                                               layout randomization (ASLR)
          --[disable-]dynamicbase            Image base address may be relocated using
                                               address space layout randomization (ASLR)
          --enable-reloc-section             Create the base relocation table
          --disable-reloc-section            Do not create the base relocation table
          --[disable-]forceinteg             Code integrity checks are enforced
          --[disable-]nxcompat               Image is compatible with data execution
                                               prevention
          --[disable-]no-isolation           Image understands isolation but do not
                                               isolate the image
          --[disable-]no-seh                 Image does not use SEH; no SE handler may
                                               be called in this image
          --[disable-]no-bind                Do not bind this image
          --[disable-]wdmdriver              Driver uses the WDM model
          --[disable-]tsaware                Image is Terminal Server aware
          --build-id[=STYLE]                 Generate build ID
        i386pe: 
          --base_file <basefile>             Generate a base file for relocatable DLLs
          --dll                              Set image base to the default for DLLs
          --file-alignment <size>            Set file alignment
          --heap <size>                      Set initial size of the heap
          --image-base <address>             Set start address of the executable
          --major-image-version <number>     Set version number of the executable
          --major-os-version <number>        Set minimum required OS version
          --major-subsystem-version <number> Set minimum required OS subsystem version
          --minor-image-version <number>     Set revision number of the executable
          --minor-os-version <number>        Set minimum required OS revision
          --minor-subsystem-version <number> Set minimum required OS subsystem revision
          --section-alignment <size>         Set section alignment
          --stack <size>                     Set size of the initial stack
          --subsystem <name>[:<version>]     Set required OS subsystem [& version]
          --support-old-code                 Support interworking with old code
          --[no-]leading-underscore          Set explicit symbol underscore prefix mode
          --thumb-entry=<symbol>             Set the entry point to be Thumb <symbol>
          --[no-]insert-timestamp            Use a real timestamp rather than zero (default).
                                             This makes binaries non-deterministic
          --add-stdcall-alias                Export symbols with and without @nn
          --disable-stdcall-fixup            Don't link _sym to _sym@nn
          --enable-stdcall-fixup             Link _sym to _sym@nn without warnings
          --exclude-symbols sym,sym,...      Exclude symbols from automatic export
          --exclude-all-symbols              Exclude all symbols from automatic export
          --exclude-libs lib,lib,...         Exclude libraries from automatic export
          --exclude-modules-for-implib mod,mod,...
                                             Exclude objects, archive members from auto
                                             export, place into import library instead.
          --export-all-symbols               Automatically export all globals to DLL
          --kill-at                          Remove @nn from exported symbols
          --output-def <file>                Generate a .DEF file for the built DLL
          --warn-duplicate-exports           Warn about duplicate exports
          --compat-implib                    Create backward compatible import libs;
                                               create __imp_<SYMBOL> as well.
          --enable-auto-image-base[=<address>] Automatically choose image base for DLLs
                                               (optionally starting with address) unless
                                               specifically set with --image-base
          --disable-auto-image-base          Do not auto-choose image base. (default)
          --dll-search-prefix=<string>       When linking dynamically to a dll without
                                               an importlib, use <string><basename>.dll
                                               in preference to lib<basename>.dll 
          --enable-auto-import               Do sophisticated linking of _sym to
                                               __imp_sym for DATA references
          --disable-auto-import              Do not auto-import DATA items from DLLs
          --enable-runtime-pseudo-reloc      Work around auto-import limitations by
                                               adding pseudo-relocations resolved at
                                               runtime.
          --disable-runtime-pseudo-reloc     Do not add runtime pseudo-relocations for
                                               auto-imported DATA.
          --enable-extra-pe-debug            Enable verbose debug output when building
                                               or linking to DLLs (esp. auto-import)
          --large-address-aware              Executable supports virtual addresses
                                               greater than 2 gigabytes
          --disable-large-address-aware      Executable does not support virtual
                                               addresses greater than 2 gigabytes
          --enable-long-section-names        Use long COFF section names even in
                                               executable image files
          --disable-long-section-names       Never use long COFF section names, even
                                               in object files
          --[disable-]dynamicbase            Image base address may be relocated using
                                               address space layout randomization (ASLR)
          --enable-reloc-section             Create the base relocation table
          --disable-reloc-section            Do not create the base relocation table
          --[disable-]forceinteg             Code integrity checks are enforced
          --[disable-]nxcompat               Image is compatible with data execution
                                               prevention
          --[disable-]no-isolation           Image understands isolation but do not
                                               isolate the image
          --[disable-]no-seh                 Image does not use SEH. No SE handler may
                                               be called in this image
          --[disable-]no-bind                Do not bind this image
          --[disable-]wdmdriver              Driver uses the WDM model
          --[disable-]tsaware                Image is Terminal Server aware
          --build-id[=STYLE]                 Generate build ID
        
        Report bugs to <https://sourceware.org/bugzilla/>
        ```

        #### **ldd**
        ```sh [data-file:ldd (Ubuntu GLIBC 2.39-0ubuntu8.7) 2.39，Written by Roland McGrath and Ulrich Drepper.]
        vscode ➜ /workspaces/c_learning (master) $ ldd --help
        Usage: ldd [OPTION]... FILE...
              --help              print this help and exit
              --version           print version information and exit
          -d, --data-relocs       process data relocations
          -r, --function-relocs   process data and function relocations
          -u, --unused            print unused direct dependencies
          -v, --verbose           print all information
        
        For bug reporting instructions, please see:
        <https://bugs.launchpad.net/ubuntu/+source/glibc/+bugs>.
        ```
        <!-- tabs:end -->

    + ### 答疑

        - #### 静态共享库
        - #### 延迟绑定PLT
        - #### 动态链接时进程堆栈初始化信息

            如[l-dynamic/Makefile](https://github.com/12302-bak/project-vscode-remote/blob/c41ecd9bc226abb6522679aeb3ac246545183c5e/book/l-dynamic/Makefile) 一样构建程序 Program1，然后使用 gdb 进行辅佐验证。或者手动扫描栈内存获取相关数据。

            * ##### 使用 GDB 直接获取

                使用命令`gdb build/Program1`启动 gdb。然后使用命令`start`启动程序。执行`info auxv`查看**辅助信息数组(Auxiliary Vector)**。
                ![](/.images/devops/build/gcc/library-link/ll-dynamic-aux-01.png ':size=60%')

            * ##### 手动扫描栈内存获取

                手动获取步骤如下：
                
                1、 `info proc mappings` 查看整个程序内存映射，得知 **stack** 的起始（0xfffdd000，0xffffe000）。
                ![](/.images/devops/build/gcc/library-link/ll-dynamic-aux-02.png ':size=60%')

                2、计算当前栈顶位置`print $esp`，然后扫描至栈底。匹配内容为`info auxv` 里面的 **AT_PLATFORM** = 0xffffd1cb，使用 [find](https://sourceware.org/gdb/download/onlinedocs/gdb.html/Searching-Memory.html#index-find) 有问题，所以改为 python 找到大致位置 **0xffffd1a0**。

                <details><summary>扫描代码</summary>

                ```python
                start_addr = 0xffffcfb0 # $esp
                end_addr   = 0xffffe000
                target_val = 0xffffd1cb

                inf = gdb.selected_inferior()
                print(f"[*] Starting resilient scan from {hex(start_addr)} to {hex(end_addr)}...")

                # 按 1 字节步长滑动窗口扫描（支持错位对齐的数据）
                curr = start_addr
                found_count = 0

                while curr <= (end_addr - 4):
                    try:
                        # 尝试读取 4 个字节
                        mem = inf.read_memory(curr, 4)
                        value = int.from_bytes(mem.tobytes(), byteorder='little')
                        if value == target_val:
                            print(f"[+] Found {hex(target_val)} at address: {hex(curr)}")
                            found_count += 1
                        curr += 1
                    except gdb.MemoryError:
                        # 遇到不可读的内存碎片，自动跳过 1 字节继续尝试
                        curr += 1
                        print(f"[*] exception from {hex(curr)}!")

                print(f"[*] Scan finished. End with: {hex(curr)}, Total found: {found_count}")
                ```
                </details>

                ![](/.images/devops/build/gcc/library-link/ll-dynamic-aux-03.png ':size=40%') 

                3、然后将 **0xffffd1a0** 往下放 0x60 个字节，就是多看附近 6 行内容，`echo 'ibase=16;obase=10; FFFFD1A0 - 60' | bc` = FFFFD140，就可以发现数据都在附近。

                ![](/.images/devops/build/gcc/library-link/ll-dynamic-aux-04.png ':size=90%') 

* ## Reference

    + https://sourceware.org/gdb/download/onlinedocs/gdb.html