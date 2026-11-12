---

---

* ## Intro(Hashcat)

    + ### 简单介绍

        > [!] <https://github.com/hashcat/hashcat.git>
        <br><br>安装位置：
        <br><span style='padding:2.3em'>`ls -hal  ~/software/hashcat`:[v6.2.6-53-g00126598b]</span>
        <br><span style='padding:2.3em'>`/usr/local/Cellar/hashcat/8.0.0`</span>
        <br>位置参数的顺序是固定的：首先是哈希值或哈希文件，然后是单词列表或掩码。选项没有位置限制，可以放在任何位置，包括掩码之后。
        <br>最多支持 8 种自定义字符集。如果要在掩码或自定义字符集中使用问号（“?”，不带引号），则需要用额外的问号对其进行转义，即 `??` 表示该问号用作字面符号
        <br><br>破解速度计算
        <br>在传统的暴力破解攻击中，我们需要一个包含全部大写字母、全部小写字母和全部数字的字符集（也称为“混合字母数字”）。密码长度为 9，因此我们需要尝试 62^9 (13,537,086,546,263,552) 种组合。
        <br>以 100M/s 的破解速度计算，这需要超过4 年的时间才能完成。
        <br>`echo 'scale=3; 62^9 / (100 * 1000 * 1000) / (60 * 60 * 24 * 365)' | bc` ≈ 4 year
        <br><br>掩码攻击可以利用人们选择密码的常见模式。上面的密码就遵循一个常见模式：姓名后附加年份。我们可以将攻击配置为仅尝试第一个位置使用大写字母。仅第二个或第三个位置使用大写字母的情况并不常见。
        <br>这会将密钥空间缩小到 52*26*26*26*26*10*10*10*10 (237,627,520,000) 种组合。在 100M/s 的破解速率下，只需 40 分钟即可完成。
        <br>`echo 'scale=3; 52*26*26*26*26*10*10*10*10 / (100 * 1000 * 1000) / 60' | bc` ≈ 39.604 min

        ```shell [data-cc:400px]
        ➜  hashcat -h
        hashcat (v6.2.6-53-g00126598b) starting in help mode

        Usage: hashcat [options]... hash|hashfile|hccapxfile [dictionary|mask|directory]...

        - [ Options ] -

        Options Short / Long           | Type | Description                                          | Example
        ================================+======+======================================================+=======================
        -m, --hash-type                | Num  | Hash-type, references below (otherwise autodetect)   | -m 1000
        -a, --attack-mode              | Num  | Attack-mode, see references below                    | -a 3
        -V, --version                  |      | Print version                                        |
        -h, --help                     |      | Print help                                           |
            --quiet                    |      | Suppress output                                      |
            --hex-charset              |      | Assume charset is given in hex                       |
            --hex-salt                 |      | Assume salt is given in hex                          |
            --hex-wordlist             |      | Assume words in wordlist are given in hex            |
            --force                    |      | Ignore warnings                                      |
            --deprecated-check-disable |      | Enable deprecated plugins                            |
            --status                   |      | Enable automatic update of the status screen         |
            --status-json              |      | Enable JSON format for status output                 |
            --status-timer             | Num  | Sets seconds between status screen updates to X      | --status-timer=1
            --stdin-timeout-abort      | Num  | Abort if there is no input from stdin for X seconds  | --stdin-timeout-abort=300
            --machine-readable         |      | Display the status view in a machine-readable format |
            --keep-guessing            |      | Keep guessing the hash after it has been cracked     |
            --self-test-disable        |      | Disable self-test functionality on startup           |
            --loopback                 |      | Add new plains to induct directory                   |
            --markov-hcstat2           | File | Specify hcstat2 file to use                          | --markov-hcstat2=my.hcstat2
            --markov-disable           |      | Disables markov-chains, emulates classic brute-force |
            --markov-classic           |      | Enables classic markov-chains, no per-position       |
            --markov-inverse           |      | Enables inverse markov-chains, no per-position       |
        -t, --markov-threshold         | Num  | Threshold X when to stop accepting new markov-chains | -t 50
            --runtime                  | Num  | Abort session after X seconds of runtime             | --runtime=10
            --session                  | Str  | Define specific session name                         | --session=mysession
            --restore                  |      | Restore session from --session                       |
            --restore-disable          |      | Do not write restore file                            |
            --restore-file-path        | File | Specific path to restore file                        | --restore-file-path=x.restore
        -o, --outfile                  | File | Define outfile for recovered hash                    | -o outfile.txt
            --outfile-format           | Str  | Outfile format to use, separated with commas         | --outfile-format=1,3
            --outfile-autohex-disable  |      | Disable the use of $HEX[] in output plains           |
            --outfile-check-timer      | Num  | Sets seconds between outfile checks to X             | --outfile-check-timer=30
            --wordlist-autohex-disable |      | Disable the conversion of $HEX[] from the wordlist   |
        -p, --separator                | Char | Separator char for hashlists and outfile             | -p :
            --stdout                   |      | Do not crack a hash, instead print candidates only   |
            --show                     |      | Compare hashlist with potfile; show cracked hashes   |
            --left                     |      | Compare hashlist with potfile; show uncracked hashes |
            --username                 |      | Enable ignoring of usernames in hashfile             |
            --remove                   |      | Enable removal of hashes once they are cracked       |
            --remove-timer             | Num  | Update input hash file each X seconds                | --remove-timer=30
            --potfile-disable          |      | Do not write potfile                                 |
            --potfile-path             | File | Specific path to potfile                             | --potfile-path=my.pot
            --encoding-from            | Code | Force internal wordlist encoding from X              | --encoding-from=iso-8859-15
            --encoding-to              | Code | Force internal wordlist encoding to X                | --encoding-to=utf-32le
            --debug-mode               | Num  | Defines the debug mode (hybrid only by using rules)  | --debug-mode=4
            --debug-file               | File | Output file for debugging rules                      | --debug-file=good.log
            --induction-dir            | Dir  | Specify the induction directory to use for loopback  | --induction=inducts
            --outfile-check-dir        | Dir  | Specify the directory to monitor 3rd party outfiles  | --outfile-check-dir=x
            --logfile-disable          |      | Disable the logfile                                  |
            --hccapx-message-pair      | Num  | Load only message pairs from hccapx matching X       | --hccapx-message-pair=2
            --nonce-error-corrections  | Num  | The BF size range to replace AP's nonce last bytes   | --nonce-error-corrections=16
            --keyboard-layout-mapping  | File | Keyboard layout mapping table for special hash-modes | --keyb=german.hckmap
            --truecrypt-keyfiles       | File | Keyfiles to use, separated with commas               | --truecrypt-keyf=x.png
            --veracrypt-keyfiles       | File | Keyfiles to use, separated with commas               | --veracrypt-keyf=x.txt
            --veracrypt-pim-start      | Num  | VeraCrypt personal iterations multiplier start       | --veracrypt-pim-start=450
            --veracrypt-pim-stop       | Num  | VeraCrypt personal iterations multiplier stop        | --veracrypt-pim-stop=500
        -b, --benchmark                |      | Run benchmark of selected hash-modes                 |
            --benchmark-all            |      | Run benchmark of all hash-modes (requires -b)        |
            --speed-only               |      | Return expected speed of the attack, then quit       |
            --progress-only            |      | Return ideal progress step size and time to process  |
        -c, --segment-size             | Num  | Sets size in MB to cache from the wordfile to X      | -c 32
            --bitmap-min               | Num  | Sets minimum bits allowed for bitmaps to X           | --bitmap-min=24
            --bitmap-max               | Num  | Sets maximum bits allowed for bitmaps to X           | --bitmap-max=24
            --cpu-affinity             | Str  | Locks to CPU devices, separated with commas          | --cpu-affinity=1,2,3
            --hook-threads             | Num  | Sets number of threads for a hook (per compute unit) | --hook-threads=8
            --hash-info                |      | Show information for each hash-mode                  |
            --example-hashes           |      | Alias of --hash-info                                 |
            --backend-ignore-cuda      |      | Do not try to open CUDA interface on startup         |
            --backend-ignore-hip       |      | Do not try to open HIP interface on startup          |
            --backend-ignore-metal     |      | Do not try to open Metal interface on startup        |
            --backend-ignore-opencl    |      | Do not try to open OpenCL interface on startup       |
        -I, --backend-info             |      | Show system/evironment/backend API info              | -I or -II
        -d, --backend-devices          | Str  | Backend devices to use, separated with commas        | -d 1
        -D, --opencl-device-types      | Str  | OpenCL device-types to use, separated with commas    | -D 1
        -O, --optimized-kernel-enable  |      | Enable optimized kernels (limits password length)    |
        -M, --multiply-accel-disable   |      | Disable multiply kernel-accel with processor count   |
        -w, --workload-profile         | Num  | Enable a specific workload profile, see pool below   | -w 3
        -n, --kernel-accel             | Num  | Manual workload tuning, set outerloop step size to X | -n 64
        -u, --kernel-loops             | Num  | Manual workload tuning, set innerloop step size to X | -u 256
        -T, --kernel-threads           | Num  | Manual workload tuning, set thread count to X        | -T 64
            --backend-vector-width     | Num  | Manually override backend vector-width to X          | --backend-vector=4
            --spin-damp                | Num  | Use CPU for device synchronization, in percent       | --spin-damp=10
            --hwmon-disable            |      | Disable temperature and fanspeed reads and triggers  |
            --hwmon-temp-abort         | Num  | Abort if temperature reaches X degrees Celsius       | --hwmon-temp-abort=100
            --scrypt-tmto              | Num  | Manually override TMTO value for scrypt to X         | --scrypt-tmto=3
        -s, --skip                     | Num  | Skip X words from the start                          | -s 1000000
        -l, --limit                    | Num  | Limit X words from the start + skipped words         | -l 1000000
            --keyspace                 |      | Show keyspace base:mod values and quit               |
        -j, --rule-left                | Rule | Single rule applied to each word from left wordlist  | -j 'c'
        -k, --rule-right               | Rule | Single rule applied to each word from right wordlist | -k '^-'
        -r, --rules-file               | File | Multiple rules applied to each word from wordlists   | -r rules/best64.rule
        -g, --generate-rules           | Num  | Generate X random rules                              | -g 10000
            --generate-rules-func-min  | Num  | Force min X functions per rule                       |
            --generate-rules-func-max  | Num  | Force max X functions per rule                       |
            --generate-rules-func-sel  | Str  | Pool of rule operators valid for random rule engine  | --generate-rules-func-sel=ioTlc
            --generate-rules-seed      | Num  | Force RNG seed set to X                              |
        -1, --custom-charset1          | CS   | User-defined charset ?1                              | -1 ?l?d?u
        -2, --custom-charset2          | CS   | User-defined charset ?2                              | -2 ?l?d?s
        -3, --custom-charset3          | CS   | User-defined charset ?3                              |
        -4, --custom-charset4          | CS   | User-defined charset ?4                              |
            --identify                 |      | Shows all supported algorithms for input hashes      | --identify my.hash
        -i, --increment                |      | Enable mask increment mode                           |
            --increment-min            | Num  | Start mask incrementing at X                         | --increment-min=4
            --increment-max            | Num  | Stop mask incrementing at X                          | --increment-max=8
        -S, --slow-candidates          |      | Enable slower (but advanced) candidate generators    |
            --brain-server             |      | Enable brain server                                  |
            --brain-server-timer       | Num  | Update the brain server dump each X seconds (min:60) | --brain-server-timer=300
        -z, --brain-client             |      | Enable brain client, activates -S                    |
            --brain-client-features    | Num  | Define brain client features, see below              | --brain-client-features=3
            --brain-host               | Str  | Brain server host (IP or domain)                     | --brain-host=127.0.0.1
            --brain-port               | Port | Brain server port                                    | --brain-port=13743
            --brain-password           | Str  | Brain server authentication password                 | --brain-password=bZfhCvGUSjRq
            --brain-session            | Hex  | Overrides automatically calculated brain session     | --brain-session=0x2ae611db
            --brain-session-whitelist  | Hex  | Allow given sessions only, separated with commas     | --brain-session-whitelist=0x2ae611db

        - [ License ] -

        hashcat is licensed under the MIT license
        Copyright and license terms are listed in docs/license.txt

        If you still have no idea what just happened, try the following pages:

        * https://hashcat.net/wiki/#howtos_videos_papers_articles_etc_in_the_wild
        * https://hashcat.net/faq/

        If you think you need help by a real human come to the hashcat Discord:

        * https://hashcat.net/discord
        ```

        <!-- tabs:start -->
        ##### **Brain Client Features**
        ```shell
        - [ Brain Client Features ] -

        # | Features
        ===+========
        1 | Send hashed passwords
        2 | Send attack positions
        3 | Send hashed passwords and attack positions
        ```

        ##### **Outfile Formats**
        ```shell
        - [ Outfile Formats ] -

        # | Format
        ===+========
        1 | hash[:salt]
        2 | plain
        3 | hex_plain
        4 | crack_pos
        5 | timestamp absolute
        6 | timestamp relative
        ```
        
        ##### **Rule Debugging Modes**
        ```shell
        - [ Rule Debugging Modes ] -

        # | Format
        ===+========
        1 | Finding-Rule
        2 | Original-Word
        3 | Original-Word:Finding-Rule
        4 | Original-Word:Finding-Rule:Processed-Word
        5 | Original-Word:Finding-Rule:Processed-Word:Wordlist
        ```

        ##### **Attack Modes**
        ```shell {11}
        - [ Attack Modes ] -

        # | Mode
        ===+======
        # tries all words in a list. This is also called “straight” mode (attack mode 0, -a 0) 
        # https://hashcat.net/wiki/doku.php?id=dictionary_attack
        0 | Straight

        # concatenates words from multiple wordlists (-a 1)
        # 类似于 12 `-a 12 ?w?q left.dict right.dict `
        # https://hashcat.net/wiki/doku.php?id=combinator_attack
        1 | Combination

        # and Mask attack - tries all characters from given charsets at each position (-a 3)
        # https://hashcat.net/wiki/doku.php?id=mask_attack
        3 | Brute-force


        # https://hashcat.net/wiki/doku.php?id=hybrid_attack
        6 | Hybrid Wordlist + Mask
        7 | Hybrid Mask + Wordlist
        12 | Hybrid, mask says where the word goes v8.0.0（support）

        # uses a username, filename, hint or other information that may have influenced a password to attack its specific hash (-a 9)
        # https://hashcat.net/wiki/doku.php?id=association_attack
        9 | Association
        ```

        ##### **Built-in Charsets**
        ```shell
        - [ Built-in Charsets ] -

        ? | Charset
        ===+=========
        l | abcdefghijklmnopqrstuvwxyz [a-z]
        u | ABCDEFGHIJKLMNOPQRSTUVWXYZ [A-Z]
        d | 0123456789                 [0-9]
        h | 0123456789abcdef           [0-9a-f]
        H | 0123456789ABCDEF           [0-9A-F]
        s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~
        a | ?l?u?d?s
        b | 0x00 - 0xff
        ```

        ##### **OpenCL Device Types**
        ```shell
        - [ OpenCL Device Types ] -

        # | Device Type
        ===+=============
        1 | CPU
        2 | GPU
        3 | FPGA, DSP, Co-Processor
        ```

        ##### **Workload Profiles**
        ```shell
        - [ Workload Profiles ] -

        # | Performance | Runtime | Power Consumption | Desktop Impact
        ===+=============+=========+===================+=================
        1 | Low         |   2 ms  | Low               | Minimal
        2 | Default     |  12 ms  | Economic          | Noticeable
        3 | High        |  96 ms  | High              | Unresponsive
        4 | Nightmare   | 480 ms  | Insane            | Headless
        ```
        
        ##### **Basic Examples**
        ```shell
        - [ Basic Examples ] -

        Attack-          | Hash- |
        Mode             | Type  | Example command
        ==================+=======+==================================================================
        Wordlist         | $P$   | hashcat -a 0 -m 400 example400.hash example.dict
        Wordlist + Rules | MD5   | hashcat -a 0 -m 0 example0.hash example.dict -r rules/best64.rule
        Brute-Force      | MD5   | hashcat -a 3 -m 0 example0.hash ?a?a?a?a?a?a
        Combinator       | MD5   | hashcat -a 1 -m 0 example0.hash example.dict example.dict
        Association      | $1$   | hashcat -a 9 -m 500 example500.hash 1word.dict -r rules/best64.rule
        ```
        <!-- tabs:end -->

    + ### 示例用法

        ```shell
        # 查看您的系统上有哪些设备和后端可用
        hashcat -I

        # 预览生成的密码
        hashcat -a 3 -1 '?d?l' '?1?1?1?1?1?1'  --stdout | head -20

        # 预览增量生成的密码
        hashcat -a 3 --increment --increment-min 2 -1 '?l?u' 'e?d?l?l?l?l19?d?d' --stdout | head -n 20

        # 预览规则变形后的密码（取前 20 行）
        hashcat test.dict -r rules/best64.rule --stdout | head -100

        # -j/-k [参考: How do I work with -j or -k? Can I use rules files?](https://hashcat.net/wiki/doku.php?id=frequently_asked_questions#:~:text=How%20do%20I%20work%20with)
        echo "password" | hashcat -j 'c $1 $!'  --stdout
        hashcat -a 1 -j 'c' -k '$!' --stdout left.txt right.txt

        # --stdout 貌似不支持 -a 9 的模式。
        # 关联攻击
        echo "alice:`md5 -s 'alice123s0'`:s0" > user.hash && echo "j.smith:`md5 -s 'jsmith000Salt'`:Salt" >> user.hash
        /usr/local/Cellar/hashcat/8.0.0/bin/hashcat -m 10 -a 9 user.hash -o cracked.txt
        ```

    + ### 实际案例

        - #### Rar crack

            ```shell
            # 提取hash值，注意裁剪文件名
            rar2john 9.rar > example0.hash
            # output:
            # $rar5$16$8345875512857bc69f0a8059ffce0166$15$7880153df518399181c9e2df158b7249$8$f627000464e14963
            # 使用hashcat撞库
            hashcat -a 3 -m 13000 -O -1 '?d?l' -D 1,2 -d 3,5 example0.hash '?l?l?l123'
            # 自定义字符集
            hashcat -a 3 -m 13000 -1 '?d?l' -D 1,2 -d 3,5 example0.hash '?1?1?1?1?1?1'
            # hashcat.potfile
            # /Users/stevenobelia/.local/share/hashcat/hashcat.potfile
            # 查看破解记录
            hashcat --show example0.hash
            ```
        - #### Zip crack

            > [!NOTE] 使用三种提取工具分别提取由 (ezip、zip）创建的 zip 压缩包，最终还是 [zip2hashcat](https://github.com/hashstation/zip2hashcat) 完胜。
            <br>不同的工具提取的 hash 值是不一样的，有些压缩包可以使用不同的 hash type 处理，效果一样。

            ```shell [data-file:ezip.app 压缩]
            # ❌ /usr/local/Cellar/john-jumbo/1.9.0_1/share/john/zip2john
            zip2john /Users/stevenobelia/Downloads/temporary-download/test.zip > hash_test.txt
            # $pkzip2$1*2*2*0*0*25c11*0*0*4c*8*0*0000*8aea**$/pkzip2$
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 17225 -a 3 -1 654321 hash_test.txt '?1?1?1?1?1?1'
            # Hash parsing error in hashfile: 'hash_test.txt' on line 1 ($pkzip...*0*0*4c*8*0*0000*8aea**$/pkzip2$): Token length exception

            # ❌ wget https://raw.githubusercontent.com/oliverjueguen/zip2hashcat/main/zip2hashcat.py && chmod +x zip2hashcat.py
            ./zip2hashcat.py /Users/stevenobelia/Downloads/temporary-download/test.zip -o hash_test.txt
            # $pkzip$2*2*1*0*17edf*25c11*54e3a3e9*0*4c*8*17edf*8aea*ace804d7bed7a55a2ea54d881a...
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 17225 -a 3 -1 654321 hash_test.txt '?1?1?1?1?1?1'
            # Hash parsing error in hashfile: 'hash_test.txt' on line 1 ($pkzip...e97f057a1c62a0b768d1e2d*$/pkzip$): Hash-value exception

            # ✅ https://github.com/hashstation/zip2hashcat
            ./zip2hashcat /Users/stevenobelia/Downloads/temporary-download/test.zip > ~/Desktop/hash_test.txt
            # $pkzip$2*1*1*0*8*24*aee0*6e0710fdfe22fd8b997009a95f...pkzip$
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 17225 -a 3 -1 654321 ~/Desktop/hash_test.txt '?1?1?1?1?1?1'
            ```

            ```shell {17-20}[data-file:MacOSX 自带 zip 命令压缩的文件]
            zip -er archive.zip myemployees.sql Netfilter-packet-flow.svg

            # ✅ /usr/local/Cellar/john-jumbo/1.9.0_1/share/john/zip2john
            zip2john /Users/stevenobelia/Downloads/temporary-download/archive.zip > hash_archive.txt
            # $pkzip2$2*2*1*0*8*24*94d8*b6c3*1b07dbddf0efb76993b1...kzip2$
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 17225 -a 3 -1 654321 hash_archive.txt '?1?1?1?1?1?1'

            # ❌ wget https://raw.githubusercontent.com/oliverjueguen/zip2hashcat/main/zip2hashcat.py && chmod +x zip2hashcat.py
            ./zip2hashcat.py /Users/stevenobelia/Downloads/temporary-download/archive.zip -o hash_archive.txt
            # $pkzip$2*2*1*0*14e0*4478*91d21547*0*49*8*14e0*9eb8*bee2515f5ef6e76c6c89bfb3baf28...
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 17225 -a 3 -1 654321 hash_archive.txt '?1?1?1?1?1?1'
            # Hash parsing error in hashfile: 'hash_archive.txt' on line 1 ($pkzip...8f5902d038d6305c379df7e*$/pkzip$): Hash-value exception

            # ✅ https://github.com/hashstation/zip2hashcat
            ./zip2hashcat /Users/stevenobelia/Downloads/temporary-download/archive.zip > ~/Desktop/hash_archive.txt
            # $pkzip$2*1*1*0*8*24*b6c3*1b07dbddf0efb76993b1656152...pkzip$ 
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1    -m 17225 -a 3 -1 654321 ~/Desktop/hash_archive.txt '?1?1?1?1?1?1'
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1    -m 17220 -a 3 -1 654321 ~/Desktop/hash_archive.txt '?1?1?1?1?1?1'
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable --force -m 17225 -a 3 -1 654321 ~/Desktop/hash_archive.txt '?1?1?1?1?1?1'
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat -m 17225 --show hash.txt
            ```

        - #### 验证 linux 密码

            ```shell
            cat /etc/shadow
            # eli:$y$j9T$xOfM8qC.nB8Nk4XPzAxSx.$qluCHZANgLaY908YZqVcDSxSxLorI2p.ylbNACSysV9:20769:0:99999:7:::

            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat -m 36100 -a 3 --increment --increment-min 6 '$y$j9T$xOfM8qC.nB8Nk4XPzAxSx.$qluCHZANgLaY908YZqVcDSxSxLorI2p.ylbNACSysV9' 'pass?l?l?l?l'
            ```
        - #### Windows NT-hash，LM-hash

            ```shell
            # 可以用 saminside 获取 NT-hash，LM-hash 的值
            # NT-hash
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -m 1000 -a 3 '209C6174DA490CAEB422F3FA5A7AE634' '?l?l?l?l?l'
            # LM-hash:
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -m 3000 -a 3 'F0D412BD764FFE81AAD3B435B51404EE' '?u?u?u?u?u'
            ```

        - #### Mysql 密码支持

            ```shell
            # 测试版本（5.7.21）
            CREATE USER 'test_user'@'localhost' IDENTIFIED BY 'p123456!';
            select User, Host, authentication_string from mysql.user;
            # *46AE77295E4D754C5D9E061527F8B72E5BB8B808
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -D 1 -m 300 -a 3 -1 654321 '46AE77295E4D754C5D9E061527F8B72E5BB8B808' 'p?1?1?1?1?1?1!'

            # 删除临时测试用户
            DROP USER IF EXISTS 'test_user'@'localhost';
            ```
        
        - #### Wifi 密码支持

            ```shell
            iwconfig

            # 将无线网卡切换为监控模式
            airmon-ng start wlan0

            # 扫描
            airodump-ng wlan0

            # 抓包
            airodump-ng --bssid 3C:CD:57:D5:9A:86  -c 1 -w capture/12-1-2702_2 wlan0

            # 踢除其他客户端
            # 不要关闭捕获握手包，重新开一个窗口进行攻击，如果在捕获过程中没有用户连接到 Wi-Fi 或没有获取到握手包，可以使用 aireplay-ng 发送断开（deauth）请求，强制客户端重新连接，以便捕获握手包：
            aireplay-ng -0 50 -a 3C:CD:57:D5:9A:86 -c 10:3F:44:9F:DC:6A wlan0

            ls capture/
            cp capture/12-1-2702_2-01.cap /media/psf/Home/Desktop/

            # 握手包转换工具在线：https://hashcat.net/cap2hashcat/
            /usr/local/Cellar/hashcat/8.0.0/bin/hashcat --potfile-disable -m 22000 -a 3 /Users/stevenobelia/Downloads/temporary-download/6d6b2ab4cf2db66a88cb00ab463d578d.hc22000 'c?d?d?d?d?d?d?s?s?s'
            ```

    + ### Issues

        * #### 注意：

            > [!CAUTION] 安装john-ripper后需要链接rar2john
            <br> <https://superuser.com/questions/1652553/john-the-ripper-zip2john-command-not-found-mac/1670026>
            <br> 创建软链:`ln -s /usr/local/Cellar/john-jumbo/1.9.0_1/share/john/rar2john /usr/local/bin/rar2john`

        * #### 错误1:

            /Users/user/.cache/hashcat/kernels/shared.77c81869.kernel: No such file or directory

            \* Device #2: Kernel /usr/local/share/hashcat/OpenCL/shared.cl build failed.

            > [!WARNING] <https://github.com/hashcat/hashcat/issues/3121>
            <br> `mkdir -p ~/.cache/hashcat/kernels/`

        * #### 错误2

            /Users/user1/.local/share/hashcat/sessions/usage.pid: No such file or directory

            /Users/user1/.local/share/hashcat: No such file or directory

            > [!WARNING] <https://github.com/hashcat/hashcat/issues/3044>
            <br> `mkdir -p ~/share/hashcat/sessions`

* ## reference

    + [hashcat 官网](https://hashcat.net)
    + [Hashcat 的使用手册总结 ](https://www.cnblogs.com/chk141/p/12220288.html)
    + [hash type](https://hashcat.net/wiki/doku.php?id=example_hashes)
    + [explain of potfile [short:pot of gold file]](https://labex.io/tutorials/kali-use-the-potfile-to-manage-cracked-hashes-594517)