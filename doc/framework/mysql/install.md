## 压缩包安装

<!-- tabs:start -->
####  **Centos7**
- 准备
```shell
# 下载MySQL5.6.49
$ wget -c https://cdn.mysql.com/archives/mysql-5.6/mysql-5.6.49-linux-glibc2.12-x86_64.tar.gz
# 检查是否安装过mysql,如果有先卸载, 
# 用yum -y remove删除,如果有多个,一个一个执行,卸载不掉的用 rpm -ev
$ rpm -qa|grep -i mysql
# 删除所有mysql的文件夹
$ find / -name mysql|xargs rm -rf
# 卸载系统自带的Mariadb
$ rpm -qa|grep mariadb
$ rpm -ev --nodeps mariadb-libs-5.5.68-1.el7.x86_64
# 创建my.cnf 文件,或者使用原有的进行拷贝
$ cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf
# 创建mysql用户组并且创建一个用户名为mysql的用户并加入mysql用户组
$ groupadd mysql && useradd -g mysql mysql
# 创建数据目录或者使用原有的数据目录
$ mkdir data && mv data mysql-5.6.49/
# 移动安装文件夹到 /usr/local/ 并且重命名
$ mv mysql-5.6.49-linux-glibc2.12-x86_64 mysql-5.6.49 && mv mysql-5.6.49 /usr/local/
# 设置目录所属组和用户
$ chown -R mysql:mysql mysql-5.6.49/
```

- 正式安装
```shell
# 初始化
./scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql-5.6.49 --datadir=/usr/local/mysql-5.6.49/mysql_data/mysql --explicit_defaults_for_timestamp
# 自启动
$ cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
$ chmod +x /etc/rc.d/init.d/mysqld
$ chkconfig --add mysqld
$ chkconfig --list mysqld
$ service mysqld start
# 关闭防火墙
$ systemctl status firewalld
$ systemctl stop firewalld
```
- 测试及后续
```shell
# 以root账户登录mysql,默认是没有密码的
$ bin/mysql -uroot -p
# 修改密码
$ use mysql;
$ update user set password=password('12345678') where user='mysql' and host='localhost';
# 设置远程登录
$ GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
$ FLUSH PRIVILEGES;
```

- 注意事项
```shell
[1] 通过 systemctl 或者 service 启动mysql服务的时候，是通过调用 bin/mysqld_safe 作为守护进程去启动 bin/mysqld 进程来启动服务。
[2] bin/mysqld 通过 bin/mysqld --help --verbose 可以查看启动配置参数。其中my.cnf配置文件的查找过程就在输出中可以看见。
```

####  **Ubuntu**
```shell
# omitted
```

####  **Docker**
1. 配置环境
    ```shell
    # 启动数据卷
    $ docker run -dit --rm --name mysql-data registry.cn-hangzhou.aliyuncs.com/eli_w/busybox-with-mysql-datadir:1.2.0

    # 挂载数据卷并启动 
    #       [因为数据卷中的数据是经过密码保存的，所以此处需要有密码认证。如果不挂载数据卷的情况，可以使用`-e MYSQL_ALLOW_EMPTY_PASSWORD=yes`来空密码连接]
    $ docker run -d -p 13306:3306 -e MYSQL_ROOT_PASSWORD='*******' --name mysql-5.6.49 \
        --volumes-from mysql-data  mysql:5.6.49  --datadir=/mysql-data/mysql  --character-set-server=utf8mb4  --collation-server=utf8mb4_unicode_ci  --port=3306
    ```

2. 测试
    ```shell
    # 使用mysql容器内部客户端进行访问
    #       [需要注意的是，让当前测试容器和宿主机共用一个网络栈。目的就是让 127.0.0.1 变成宿主机的ip，而不是docker容器内部的。]
    #       [-e LANG="C.UTF-8" 指定当前操作系统可以支持的字符集，确保可以输入中文。有无双引号都可以,]
    #           [（en_US.UTF-8是不可以的）。]
    #           [ 还需要注意必须在mysql连接命令之前执行。不然的话，mysql是识别不到的。]
    $ docker run -it --rm --network=host -e LANG="C.UTF-8" mysql:5.6.49 mysql  -h 127.0.0.1 -u root -P 3336 -p
    ```
    ![](/.images/doc/framework/mysql/mysql-install-01.png)

##### Reference
   * https://hub.docker.com/_/mysql

<!-- tabs:end -->

## 源码安装
> [!] mysql的大部分安装包都包含了额服务器程序和客户端程序，不过在Linux下使用RPM包时会有单独的服务器RPM包和客户端RPM包需要分别安装。

## 配置文件
* ### my.cnf
    ```shell
    # For advice on how to change settings please see
    # http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

    [mysqld]
    #
    # Remove leading # and set to the amount of RAM for the most important data
    # cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
    # innodb_buffer_pool_size = 128M
    #
    # Remove leading # to turn on a very important data integrity option: logging
    # changes to the binary log between backups.
    # log_bin
    #
    # Remove leading # to set options mainly useful for reporting servers.
    # The server defaults are faster for transactions and fast SELECTs.
    # Adjust sizes as needed, experiment to find the optimal values.
    # join_buffer_size = 128M
    # sort_buffer_size = 2M
    # read_rnd_buffer_size = 2M
    basedir=/usr/local/mysql-5.6.49
    datadir=/usr/local/mysql-5.6.49/mysql_data/mysql
    #socket=/var/lib/mysql/mysql.sock

    # Disabling symbolic-links is recommended to prevent assorted security risks
    symbolic-links=0
    # addr
    bind-address=0.0.0.0
    # port
    port=3306
    # skip-grant-tables
    # 服务端使用的字符集默认为UTF8
    character-set-server=utf8
    # 创建新表时将使用的默认存储引擎
    default-storage-engine=INNODB
    # Recommended in standard MySQL setup
    sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
    max_connections=200
    max_allowed_packet=16M
    lower_case_table_names=1

    [mysql]
    # 设置mysql客户端默认字符集
    default-character-set=utf8

    [client]
    # # 设置mysql客户端连接服务端时默认使用的端口
    # port=3306
    default-character-set=utf8

    [mysqld_safe]
    log-error=/var/log/mysqld.log
    pid-file=/var/run/mysqld/mysqld.pid
    ```

* ### mysql_install_db
    <details><summary>代码示例</summary>

    ```shell
    #!/usr/bin/perl
    # -*- cperl -*-
    #
    # Copyright (c) 2007, 2017, Oracle and/or its affiliates. All rights reserved.
    #
    # This program is free software; you can redistribute it and/or modify
    # it under the terms of the GNU General Public License, version 2.0,
    # as published by the Free Software Foundation.
    #
    # This program is also distributed with certain software (including
    # but not limited to OpenSSL) that is licensed under separate terms,
    # as designated in a particular file or component or in included license
    # documentation.  The authors of MySQL hereby grant you an additional
    # permission to link the program and your derivative works with the
    # separately licensed software that they have included with MySQL.
    #
    # This program is distributed in the hope that it will be useful,
    # but WITHOUT ANY WARRANTY; without even the implied warranty of
    # MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    # GNU General Public License, version 2.0, for more details.
    #
    # You should have received a copy of the GNU General Public License
    # along with this program; if not, write to the Free Software
    # Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA

    ##############################################################################
    #
    #  This scripts creates the MySQL Server system tables.
    #
    #  This script try to match the shell script version as close as possible,
    #  but in addition being compatible with ActiveState Perl on Windows.
    #
    #  All unrecognized arguments to this script are passed to mysqld.
    #
    #  NOTE: This script in 5.0 doesn't really match the shell script
    #        version 100%, it is more close to the 5.1 version.
    #
    #  NOTE: This script was deliberately written to be as close to the shell
    #        script as possible, to make the maintenance of both in parallel
    #        easier.
    #
    ##############################################################################

    use strict;
    use warnings;

    #############################################################################
    #
    # Check if all needed modules are available, exit if something is missing.
    #############################################################################
    #

    BEGIN {
        my @req_mods = (
            'Fcntl',         'File::Basename', 'File::Copy', 'Getopt::Long',
            'Sys::Hostname', 'Data::Dumper'
        );
        my @missing_mods;
        my $req;
        foreach $req (@req_mods) {
            eval 'require ' . $req;
            if ($@) {
                push( @missing_mods, $req );
            }
            else {
                $req->import();
            }
        }

        # this avoids the confusing "BEGIN failed--compilation aborted" message
        local $SIG{__DIE__} = sub { warn @_; exit 1 };

        if (@missing_mods) {
            my $msg =
                "FATAL ERROR: please install the following Perl modules "
            . "before executing $0:\n"
            . join( "\n", @missing_mods ) . "\n";
            die $msg;
        }
    }

    Getopt::Long::Configure("pass_through");

    my @args;    # Argument list filled in
    my $basedir;

    ##############################################################################
    #
    #  Usage information
    #
    ##############################################################################

    sub usage {
        print <<EOF1;
    Usage: $0 [OPTIONS]
    --basedir=path       The path to the MySQL installation directory.
    --builddir=path      If using --srcdir with out-of-directory builds, you
    will need to set this to the location of the build
    directory where built files reside.
    --cross-bootstrap    For internal use.  Used when building the MySQL system
    tables on a different host than the target.
    --datadir=path       The path to the MySQL data directory.
    If missing, the directory will be created, but its
    parent directory must already exist and be writable.
    --defaults-extra-file=name
    Read this file after the global files are read.
    --defaults-file=name Only read default options from the given file name.
    --force              Causes mysql_install_db to run even if DNS does not
    work.  In that case, grant table entries that
    normally use hostnames will use IP addresses.
    --help               Display this help and exit.                     
    --ldata=path         The path to the MySQL data directory. Same as --datadir.
    --no-defaults        Don't read default options from any option file.
    --keep-my-cnf        Don't try to create my.cnf based on template.
    Useful for systems with working, updated my.cnf.
    Deprecated, will be removed in future version.
    EOF1
        if ( $^O !~ m/^(MSWin32|cygwin)$/ ) {
            print <<EOF2;
    --random-passwords   Create and set a random password for all root accounts
    and set the "password expired" flag,
    also remove the anonymous accounts.
    EOF2
        }
        print <<EOF3;
    --rpm                For internal use.  This option is used by RPM files
    during the MySQL installation process.
    --skip-name-resolve  Use IP addresses rather than hostnames when creating
    grant table entries.  This option can be useful if
    your DNS does not work.
    --srcdir=path        The path to the MySQL source directory.  This option
    uses the compiled binaries and support files within the
    source tree, useful for if you don't want to install
    MySQL yet and just want to create the system tables.
    EOF3
        if ( $^O !~ m/^(MSWin32|cygwin)$/ ) {
            print <<EOF4;
    --user=user_name     The login username to use for running mysqld.  Files
    and directories created by mysqld will be owned by this
    user.  You must be root to use this option.  By default
    mysqld runs using your current login name and files and
    directories that it creates will be owned by you.
    EOF4
        }
        print <<EOF5;
    Any other options are passed to the mysqld program.

    EOF5
        exit 1;
    }

    ##############################################################################
    #
    #  Parse an argument list
    #
    #  We only need to pass arguments through to the server if we don't
    #  handle them here.  So, we collect unrecognized options (passed on
    #  the command line) into the args variable.
    #
    ##############################################################################

    sub parse_arguments {
        my $opt = shift;

        my @saved_ARGV = @ARGV;
        @ARGV = @_;    # Set ARGV so GetOptions works

        my $pick_args;
        if ( @ARGV and $ARGV[0] eq 'PICK-ARGS-FROM-ARGV' ) {
            $pick_args = 1;
            shift @ARGV;
        }

        GetOptions(
            $opt,
            "force",
            "basedir=s",
            "builddir=s",    # FIXME not documented
            "srcdir=s",
            "ldata|datadir=s",

            # Note that the user will be passed to mysqld so that it runs
            # as 'user' (crucial e.g. if log-bin=/some_other_path/
            # where a chown of datadir won't help)
            "user=s",

            "skip-name-resolve",
            "verbose",
            "keep-my-cnf",
            "rpm",
            "help",
            "random-passwords",

            # These options will also be pased to mysqld.
            "defaults-file=s",
            "defaults-extra-file=s",
            "no-defaults",

            # Used when building the MySQL system tables on a different host than
            # the target. The platform-independent files that are created in
            # --datadir on the host can be copied to the target system.
            #
            # The most common use for this feature is in the Windows installer
            # which will take the files from datadir and include them as part of
            # the install package.  See top-level 'dist-hook' make target.
            #
            # --windows is a deprecated alias
            "cross-bootstrap|windows",
        ) or usage();

        usage() if $opt->{help};

        if (
            $opt->{'no-defaults'}
            && (   $opt->{'defaults-extra-file'}
                || $opt->{'defaults-file'} )
        )
        {
            error( $opt,
                "Cannot use both --no-defaults and --defaults-[extra-]file" );
        }

        @args = @ARGV if $pick_args;

        @ARGV = @saved_ARGV;    # Set back ARGV
    }

    ##############################################################################
    #
    #  Try to find a specific file within --basedir which can either be a binary
    #  release or installed source directory and return the path.
    #
    ##############################################################################

    sub find_in_basedir {
        my $opt   = shift;
        my $mode  = shift;    # "dir" or "file"
        my $files = shift;

        foreach my $file ( @{ ref($files) ? $files : [$files] } ) {
            foreach my $dir (@_) {
                foreach my $part (
                    "$file",             "$file.exe",
                    "release/$file.exe", "debug/$file.exe",
                    "relwithdebinfo/$file.exe"
                )
                {
                    my $path = "$basedir/$dir/$part";
                    if ( -f $path ) {
                        return $mode eq "dir" ? dirname($path) : $path;
                    }
                }
            }
        }
    }

    ##############################################################################
    #
    #  Just a function to write out an error report
    #
    ##############################################################################

    sub cannot_find_file {
        my $file = shift;

        print "FATAL ERROR: Could not find $file\n";
        print "\n";
        print "If you compiled from source, you need to run 'make install' to\n";
        print "copy the software into the correct location ready for operation.\n";
        print "\n";
        print "If you are using a binary release, you must either be at the top\n";
        print "level of the extracted archive, or pass the --basedir option\n";
        print "pointing to that location.\n";
        print "\n";

        exit 1;
    }

    ##############################################################################
    #
    #  Form a command line that can handle spaces in paths and arguments
    #
    ##############################################################################

    # FIXME this backslash escaping needed if using '"..."' ?
    # This regexp makes sure that any special chars are quoted,
    # so the arg gets passed exactly to the server.
    # XXX: This is broken; true fix requires using eval and proper
    # quoting of every single arg ($opt->{basedir}, $opt->{ldata}, etc.)
    #  join(" ", map {s/([^\w\_\.\-])/\\$1/g}

    sub quote_options {
        my @cmd;
        foreach my $opt (@_) {
            next unless $opt;    # If undefined or empty, just skip
            push( @cmd, "\"$opt\"" );    # Quote argument
        }
        return join( " ", @cmd );
    }

    ##############################################################################
    #
    #  Simple escape mechanism (\-escape any ' and \), suitable for two contexts:
    #  - single-quoted SQL strings
    #  - single-quoted option values on the right hand side of = in my.cnf
    #
    ##############################################################################

    # (Function and comment copied from 'mysql_secure_installation')

    # These two contexts don't handle escapes identically.  SQL strings allow
    # quoting any character (\C => C, for any C), but my.cnf parsing allows
    # quoting only \, ' or ".  For example, password='a\b' quotes a 3-character
    # string in my.cnf, but a 2-character string in SQL.
    #
    # This simple escape works correctly in both places.

    # FIXME: What about double quote in password? Not handled here - not needed?

    sub basic_single_escape {
        my ($str) = @_;

        # Inside a character class, \ is not special; this escapes both \ and '
        $str =~ s/([\'])/\\$1/g;
        return $str;
    }

    ##############################################################################
    #
    #  Handle the files with confidential contents
    #
    ##############################################################################

    my $secret_file;         # full path name of the confidential file
    my $escaped_password;    # the password, with special characters escaped

    sub ensure_secret_file {
        $secret_file = $ENV{HOME} . "/.mysql_secret";

        # Create safe files to avoid leaking info to other users
        # Loop may be extended if we need more ...
        foreach my $file ($secret_file) {
            next if -f $file;    # Already exists
            local *FILE;
            sysopen( FILE, $file, O_CREAT, 0600 )
            or die "ERROR: can't create $file: $!";
            close FILE;
        }
    }

    ##############################################################################
    #
    #  Append an arbitrary number of lines to an existing file
    #
    ##############################################################################

    sub append_file {
        my $file = shift;
        -f $file or die "ERROR: file is missing \"$file\": $!";
        open( FILE, ">>$file" ) or die "ERROR: can't append to file \"$file\": $!";
        foreach my $line (@_) {
            print FILE $line, "\n";    # Add EOL char
        }
        close FILE;
    }

    ##############################################################################
    #
    #  Inform the user about the generated random password
    #
    ##############################################################################

    sub tell_root_password {
        my $now = localtime();    # scalar context = printable string

        # Now, we need to tell the user the new root password.
        # We use "append_file" to protect the user in case they are doing multiple
        # installations intermixed with backups and restores.
        # While this would be really bad practice, it still might happen.
        # As long as this file is not destroyed, the time stamps may rescue them.
        # Having the comment and the password on the same line makes it easier
        # to automatically extract the password (automated testing!), and the final
        # empty line is for better redability.
        append_file(
            $secret_file,
            "# The random password set for the root user at $now (local time): "
            . $escaped_password,
            ""
        );
        print
    "A random root password has been set. You will find it in '$secret_file'.\n";
    }

    ##############################################################################
    #
    #  Generate a random password
    #
    ##############################################################################

    sub generate_random_password {

        # On Linux, Solaris, Max OS X and FreeBSD we have a random device available.
        my $randfile = "/dev/urandom";
        open( FD, $randfile ) || return "";
        my $password = "";
        my $pass_len = 16;
        my $c;
        while ( length($password) < $pass_len ) {
            $c = getc(FD);
            if ( $c =~ /\w/ ) {
                $password .= $c;
            }
        }
        close(FD);
        return $password;
    }

    ##############################################################################
    #
    #  Ok, let's go.  We first need to parse arguments which are required by
    #  my_print_defaults so that we can execute it first, then later re-parse
    #  the command line to add any extra bits that we need.
    #
    ##############################################################################

    my $opt = {};
    parse_arguments( $opt, 'PICK-ARGS-FROM-ARGV', @ARGV );

    # ----------------------------------------------------------------------
    # Actual basedir, not to be confused with --basedir option
    # ----------------------------------------------------------------------

    if ( $opt->{srcdir} ) {
        $basedir = $opt->{builddir};
    }
    else {
        $basedir = $opt->{basedir};
    }
    $basedir = "." if !$basedir;    # Default

    # ----------------------------------------------------------------------
    # We can now find my_print_defaults.  This script supports:
    #
    #   --srcdir=path pointing to compiled source tree
    #   --basedir=path pointing to installed binary location
    #
    # or default to compiled-in locations.
    # ----------------------------------------------------------------------

    my $print_defaults;
    my $keep_my_cnf = 0;

    if ( $opt->{srcdir} and $opt->{basedir} ) {
        error( $opt, "Specify either --basedir or --srcdir, not both" );
    }
    if ( $opt->{'keep-my-cnf'} ) {
        $keep_my_cnf = 1;
    }
    if ( $opt->{srcdir} ) {
        $opt->{builddir} = $opt->{srcdir} unless $opt->{builddir};
        $print_defaults = "$opt->{builddir}/extra/my_print_defaults";
    }
    else {
        $print_defaults =
        find_in_basedir( $opt, "file", "my_print_defaults", "bin", "extra" );
    }
    if ( !$print_defaults ) {
        $print_defaults = './bin/my_print_defaults';
    }

    -x $print_defaults
    or -f "$print_defaults.exe"
    or cannot_find_file($print_defaults);

    my $config_file;
    my $copy_cfg_file;

    # ----------------------------------------------------------------------
    # This will be the default config file (unless creation is unwanted)
    # ----------------------------------------------------------------------

    my $cnfext = ( $^O =~ m/^(MSWin32|cygwin)$/ ) ? "ini" : "cnf";

    $config_file = "$basedir/my.$cnfext";

    my $cfg_template = find_in_basedir( $opt, "file", "my-default.$cnfext",
        ".", "share", "share/mysql", "support-files" );

    # Distros might move files
    if ( ( !-r $cfg_template ) && ( -r "./share/my-default.cnf" ) ) {
        $cfg_template = "./share/my-default.cnf";
    }

    -e $cfg_template or cannot_find_file("my-default.$cnfext");

    $copy_cfg_file = $config_file;
    my $failed_write_cfg = 0;
    if ( -e $copy_cfg_file ) {
        $copy_cfg_file =~ s/my.$cnfext/my-new.$cnfext/;

        # Too early to print warning here, the user may not notice
    }

    if ( !$keep_my_cnf ) {
        open( TEMPL, $cfg_template )
        or error( $opt, "Could not open config template $cfg_template" );
        if ( open( CFG, "> $copy_cfg_file" ) ) {
            while (<TEMPL>) {

                # Remove lines beginning with # *** which are template comments
                print CFG $_ unless /^# \*\*\*/;
            }
            close CFG;
        }
        else {
            warning( $opt, "Could not write to config file $copy_cfg_file: $!" );
            $failed_write_cfg = 1;
        }
        close TEMPL;
    }

    # ----------------------------------------------------------------------
    # Now we can get arguments from the groups [mysqld] and [mysql_install_db]
    # in the my.cfg file, then re-run to merge with command line arguments.
    # ----------------------------------------------------------------------

    my $cmd;
    my @default_options;
    if ( $opt->{'defaults-file'} ) {
        $cmd =
        quote_options( $print_defaults, "--defaults-file=$opt->{'defaults-file'}",
            "mysqld", "mysql_install_db" );
    }
    else {
        $cmd = quote_options( $print_defaults, "mysqld", "mysql_install_db" );
    }

    open( PIPE, "$cmd |" ) or error( $opt, "can't run $cmd: $!" );
    while (<PIPE>) {
        chomp;
        next unless /\S/;
        push( @default_options, $_ );
    }
    close PIPE;
    $opt = {};    # Reset the arguments FIXME ?
    parse_arguments( $opt, @default_options );
    parse_arguments( $opt, 'PICK-ARGS-FROM-ARGV', @ARGV );

    # ----------------------------------------------------------------------
    # Create a random password for root, if requested and implemented
    # ----------------------------------------------------------------------

    if ( $opt->{'random-passwords'} ) {

    # Add other non-working OS like this: $^O =~ m/^(solaris|linux|freebsd|darwin)$/
    # and maintain "usage()".
    # Issue 1: random password creation
    # Issue 2: confidential file
        if ( $^O =~ m/^(MSWin32|cygwin)$/ ) {
            print
    "Random password not yet implemented for $^O - option will be ignored\n";
            delete $opt->{'random-passwords'};
        }
        else {
            ensure_secret_file();
            my $password = generate_random_password();
            if ($password) {

                # "true" means "string is non-empty"
                $escaped_password = basic_single_escape($password);
            }
            else {
            # Whatever the reason (missing "/dev/urandom"), an empty password is bad
                print "Could not generate a random password - not setting one\n";
                delete $opt->{'random-passwords'};
            }
        }
    }

    # ----------------------------------------------------------------------
    # Configure paths to support files
    # ----------------------------------------------------------------------

    # FIXME $extra_bindir is not used
    my ( $bindir, $extra_bindir, $mysqld, $pkgdatadir, $mysqld_opt, $scriptdir );

    if ( $opt->{srcdir} ) {
        $bindir       = "$basedir/client";
        $extra_bindir = "$basedir/extra";
        $mysqld       = "$basedir/sql/mysqld";
        $mysqld_opt   = "--language=$opt->{srcdir}/sql/share/english";
        $pkgdatadir   = "$opt->{srcdir}/scripts";
        $scriptdir    = "$opt->{srcdir}/scripts";
    }
    elsif ( $opt->{basedir} ) {
        $bindir       = "$opt->{basedir}/bin";
        $extra_bindir = $bindir;
        $mysqld       = find_in_basedir( $opt, "file", [ "mysqld-nt", "mysqld" ],
            "libexec", "sbin", "bin" )
        ||    # ,"sql"
        find_in_basedir( $opt, "file", "mysqld-nt", "bin" );    # ,"sql"
        $pkgdatadir = find_in_basedir( $opt, "dir", "fill_help_tables.sql",
            "share", "share/mysql" );                             # ,"scripts"
        $scriptdir = "$opt->{basedir}/scripts";
    }
    else {
        $bindir       = './bin';
        $extra_bindir = $bindir;
        $mysqld       = './bin/mysqld';
        $pkgdatadir   = './share';
        $scriptdir    = './bin';
    }

    unless ( $opt->{ldata} ) {
        $opt->{ldata} = './data';
    }

    if ( $opt->{srcdir} ) {
        $pkgdatadir = "$opt->{srcdir}/scripts";
    }

    # ----------------------------------------------------------------------
    # Set up paths to SQL scripts required for bootstrap
    # ----------------------------------------------------------------------

    my $fill_help_tables     = "$pkgdatadir/fill_help_tables.sql";
    my $create_system_tables = "$pkgdatadir/mysql_system_tables.sql";
    my $fill_system_tables   = "$pkgdatadir/mysql_system_tables_data.sql";
    my $security_commands    = "$pkgdatadir/mysql_security_commands.sql";

    foreach my $f (
        $fill_help_tables,   $create_system_tables,
        $fill_system_tables, $security_commands
    )
    {
        -f $f or cannot_find_file($f);
    }

    -x $mysqld or -f "$mysqld.exe" or cannot_find_file($mysqld);

    # Try to determine the hostname
    my $hostname = hostname();

    # ----------------------------------------------------------------------
    # Check if hostname is valid
    # ----------------------------------------------------------------------

    my $resolved;
    if ( !$opt->{'cross-bootstrap'} and !$opt->{rpm} and !$opt->{force} ) {
        my $resolveip = "$extra_bindir/resolveip";

        $resolved = `$resolveip $hostname 2>&1`;
        if ( $? != 0 ) {
            $resolved = `$resolveip localhost 2>&1`;
            if ( $? != 0 ) {
                error(
                    $opt,
    "Neither host '$hostname' nor 'localhost' could be looked up with",
                    "$resolveip",
                    "Please configure the 'hostname' command to return a correct",
                    "hostname.",
    "If you want to solve this at a later stage, restart this script",
                    "with the --force option"
                );
            }
            warning(
                $opt,
                "The host '$hostname' could not be looked up with $resolveip.",
    "This probably means that your libc libraries are not 100 % compatible",
    "with this binary MySQL version. The MySQL daemon, mysqld, should work",
    "normally with the exception that host name resolving will not work.",
                "This means that you should use IP addresses instead of hostnames",
                "when specifying MySQL privileges !"
            );
        }
    }

    # FIXME what does this really mean....
    if ( $opt->{'skip-name-resolve'} and $resolved and $resolved =~ /\s/ ) {
        $hostname = ( split( ' ', $resolved ) )[5];
    }

    # ----------------------------------------------------------------------
    # Create database directories mysql & test
    # ----------------------------------------------------------------------

    # FIXME The shell variant uses "mkdir -p":
    #  - because it is silent if the target exists, or
    #  - because it will cerate the path?
    # Path creation is demanded by testers in bug# 14731457, but that might be risky
    # in case of typos as this is run by root.
    # For now, give an error message:
    my $parent = dirname( $opt->{ldata} );
    if ( !-d $parent ) {
        error(
            $opt,
    "The parent directory for the data directory '$opt->{ldata}' does not exist.",
    "If that path was really intended, please create that directory path and then",
            "restart this script.",
    "If some other path was intended, please use the correct path when restarting this script."
        );
    }

    my $opt_user = $opt->{user};
    my @pwnam;
    if ($opt_user) {
        if ( $^O =~ m/^(MSWin32|cygwin)$/ ) {
            warning( $opt,
                "The --user option is not supported on Windows, ignoring" );
            $opt_user = undef;
        }
        else {
            @pwnam = getpwnam($opt_user);
        }
    }

    foreach my $dir ( $opt->{ldata}, "$opt->{ldata}/mysql", "$opt->{ldata}/test" ) {
        mkdir( $dir, 0700 ) unless -d $dir;
        if ( $opt_user and -w "/" ) {
            chown( $pwnam[2], $pwnam[3], $dir )
            or error( $opt, "Could not chown directory $dir" );
        }
    }

    push( @args, "--user=$opt->{user}" ) if $opt->{user};

    # ----------------------------------------------------------------------
    # Configure mysqld command line
    # ----------------------------------------------------------------------

    # FIXME use --init-file instead of --bootstrap ?!

    my $defaults_option = "";
    if ( $opt->{'no-defaults'} ) {
        $defaults_option = "--no-defaults";
    }
    elsif ( $opt->{'defaults-file'} ) {
        $defaults_option = "--defaults-file=$opt->{'defaults-file'}";
    }

    my $defaults_extra = "--defaults-extra-file=$opt->{'defaults-extra-file'}"
    if $opt->{'defaults-extra-file'};

    my $mysqld_bootstrap = $ENV{MYSQLD_BOOTSTRAP} || $mysqld;
    my $mysqld_install_cmd_line = quote_options(
        $mysqld_bootstrap,                 $defaults_option,
        $defaults_extra,                   $mysqld_opt,
        "--bootstrap",                     "--basedir=$basedir",
        "--datadir=$opt->{ldata}",         "--log-warnings=0",
        "--loose-skip-ndbcluster",         "--max_allowed_packet=8M",
        "--default-storage-engine=MyISAM", "--net_buffer_length=16K",
        @args,
    );

    # ----------------------------------------------------------------------
    # Create the system and help tables by passing them to "mysqld --bootstrap"
    # ----------------------------------------------------------------------

    report_verbose_wait( $opt, "Installing MySQL system tables..." );

    open( SQL, $create_system_tables )
    or error( $opt, "can't open $create_system_tables for reading: $!" );
    open( SQL2, $fill_system_tables )
    or error( $opt, "can't open $fill_system_tables for reading: $!" );

    # FIXME  > /dev/null ?
    if ( open( PIPE, "| $mysqld_install_cmd_line" ) ) {
        print PIPE "use mysql;\n";
        while (<SQL>) {

            # When doing a "cross bootstrap" install, no reference to the current
            # host should be added to the system tables.  So we filter out any
            # lines which contain the current host name.
            next if $opt->{'cross-bootstrap'} and /\@current_hostname/;

            print PIPE $_;
        }
        while (<SQL2>) {

            # When doing a "cross bootstrap" install, no reference to the current
            # host should be added to the system tables.  So we filter out any
            # lines which contain the current host name.
            next if $opt->{'cross-bootstrap'} and /\@current_hostname/;

            print PIPE $_;
        }

        if ( $opt->{'random-passwords'} ) {
            open( SQL3, $security_commands )
            or error( $opt, "can't open $security_commands for reading: $!" );
            while (<SQL3>) {

                # using the implicit variable $_ !
                s/ABC123xyz/$escaped_password/e
                ;    # Replace placeholder by random password
                print PIPE $_;
            }
            close SQL3;
            tell_root_password();
        }

        close PIPE;
        close SQL;
        close SQL2;

        report_verbose( $opt, "OK" );

        # ----------------------------------------------------------------------
        # Pipe fill_help_tables.sql to "mysqld --bootstrap"
        # ----------------------------------------------------------------------

        report_verbose_wait( $opt, "Filling help tables..." );
        open( SQL, $fill_help_tables )
        or error( $opt, "can't open $fill_help_tables for reading: $!" );

        # FIXME  > /dev/null ?
        if ( open( PIPE, "| $mysqld_install_cmd_line" ) ) {
            print PIPE "use mysql;\n";
            while (<SQL>) {
                print PIPE $_;
            }
            close PIPE;
            close SQL;

            report_verbose( $opt, "OK" );
        }
        else {
            warning(
                $opt,
                "HELP FILES ARE NOT COMPLETELY INSTALLED!",
                "The \"HELP\" command might not work properly"
            );
        }

        report_verbose(
            $opt,
            "To start mysqld at boot time you have to copy",
            "support-files/mysql.server to the right place " . "for your system"
        );

        if ( !$opt->{'cross-bootstrap'} ) {

        # This is not a true installation on a running system.  The end user must
        # set a password after installing the data files on the real host system.
        # At this point, there is no end user, so it does not make sense to print
        # this reminder.
            if ( $opt->{'random-passwords'} ) {
                report(
                    $opt,
                    "A RANDOM PASSWORD HAS BEEN SET FOR THE MySQL root USER !",
                    "You will find that password in '$secret_file'.",
                    "",
                    "You must change that password on your first connect,",
                    "no other statement but 'SET PASSWORD' will be accepted.",
    "See the manual for the semantics of the 'password expired' flag.",
                    "",
                    "Also, the account for the anonymous user has been removed.",
                    "",
                    "In addition, you can run:",
                    "",
                    "  $bindir/mysql_secure_installation",
                    "",
    "which will also give you the option of removing the test database.",
                    "This is strongly recommended for production servers.",
                    "",
                    "See the manual for more instructions."
                );
            }
            else {
                report(
                    $opt,
                    "PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !",
    "To do so, start the server, then issue the following commands:",
                    "",
                    "  $bindir/mysqladmin -u root password 'new-password'",
    "  $bindir/mysqladmin -u root -h $hostname password 'new-password'",
                    "",
                    "Alternatively you can run:",
                    "",
                    "  $bindir/mysql_secure_installation",
                    "",
                    "which will also give you the option of removing the test",
                    "databases and anonymous user created by default.  This is",
                    "strongly recommended for production servers.",
                    "",
                    "See the manual for more instructions."
                );
            }

            if ( !$opt->{rpm} ) {
                report(
                    $opt, "You can start the MySQL daemon with:",
                    "",   "  cd " . '.' . " ; $bindir/mysqld_safe &",
                    "",   "You can test the MySQL daemon with mysql-test-run.pl",
                    "",   "  cd mysql-test ; perl mysql-test-run.pl"
                );
            }
            report(
                $opt,
                "Please report any problems at http://bugs.mysql.com/",
                "",
                "The latest information about MySQL is available on the web at",
                "",
                "  http://www.mysql.com",
                "",
                "Support MySQL by buying support/licenses at http://shop.mysql.com"
            );

            if ($keep_my_cnf) {
                report(
                    $opt,
                    "Note: new default config file not created.",
                    "Please make sure your config file is current"
                );
            }
            elsif ( $copy_cfg_file eq $config_file and !$failed_write_cfg ) {
                report(
                    $opt,
                    "New default config file was created as $config_file and",
                    "will be used by default by the server when you start it.",
                    "You may edit this file to change server settings"
                );
            }
            elsif ($failed_write_cfg) {
                warning(
                    $opt,
                    "Could not copy config file template $cfg_template to",
                    "$copy_cfg_file, may not have access rights to do so.",
                    "You may want to copy the file manually, or create your own,",
    "it will then be used by default by the server when you start it."
                );
            }
            else {
                warning(
                    $opt,
                    "Found existing config file $config_file on the system.",
                    "Because this file might be in use, it was not replaced,",
                    "but was used in bootstrap (unless you used --defaults-file)",
                    "and when you later start the server.",
                    "The new default config file was created as $copy_cfg_file,",
    "please compare it with your file and take the changes you need."
                );
            }
            foreach my $cfg ( "/etc/my.$cnfext", "/etc/mysql/my.$cnfext" ) {
                check_sys_cfg_file( $opt, $cfg );
            }
        }
        exit 0;
    }
    else {
        error(
            $opt,
            "Installation of system tables failed!",
            "",
            "Examine the logs in $opt->{ldata} for more information.",
            "You can try to start the mysqld daemon with:",
            "$mysqld --skip-grant-tables &",
            "and use the command line tool",
            "$bindir/mysql to connect to the mysql",
            "database and look at the grant tables:",
            "",
            "shell> $bindir/mysql -u root mysql",
            "mysql> show tables",
            "",
            "Try 'mysqld --help' if you have problems with paths. Using --log",
            "gives you a log in $opt->{ldata} that may be helpful.",
            "",
            "The latest information about MySQL is available on the web at",
            "http://www.mysql.com",
    "Please consult the MySQL manual section: 'Problems running mysql_install_db',",
            "and the manual section that describes problems on your OS.",
            "Another information source is the MySQL email archive.",
            "",
            "Please check all of the above before submitting a bug report",
            "at http://bugs.mysql.com/"
        );
    }

    ##############################################################################
    #
    #  Misc
    #
    ##############################################################################

    sub check_sys_cfg_file {
        my $opt   = shift;
        my $fname = shift;

        if ( -e $fname ) {
            warning(
                $opt,
                "Default config file $fname exists on the system",
                "This file will be read by default by the MySQL server",
                "If you do not want to use this, either remove it, or use the",
                "--defaults-file argument to mysqld_safe when starting the server"
            );
        }
    }

    sub report_verbose {
        my $opt  = shift;
        my $text = shift;

        report_verbose_wait( $opt, $text, @_ );
        print "\n\n";
    }

    sub report_verbose_wait {
        my $opt  = shift;
        my $text = shift;

        if ( $opt->{verbose} or ( !$opt->{rpm} and !$opt->{'cross-bootstrap'} ) ) {
            print "$text";
            map { print "\n$_" } @_;
        }
    }

    sub report {
        my $opt  = shift;
        my $text = shift;

        print "$text\n";
        map { print "$_\n" } @_;
        print "\n";
    }

    sub error {
        my $opt  = shift;
        my $text = shift;

        print "FATAL ERROR: $text\n";
        map { print "$_\n" } @_;
        exit 1;
    }

    sub warning {
        my $opt  = shift;
        my $text = shift;

        print "WARNING: $text\n";
        map { print "$_\n" } @_;
        print "\n";
    }

    # Include dummy lines with patterns that generalized pkgadd script expects

    my $_pkgadd_fodder = "
    basedir=foo
    datadir=bar
    ";

    ```
    </details>

## 修改密码的几种方式
* 用SET PASSWORD命令
 
> [?] SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass'); FLUSH PRIVILEGES;
* 用mysqladmin
 
> [?] mysqladmin -u root password "newpass" <br>
\# 如果设置过 mysqladmin -u root password oldpass "newpass"
* 用UPDATE直接编辑user表
 
> [?] UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root'; FLUSH PRIVILEGES;
* 在丢失root密码的时候，可以这样
 
> [?] mysqld_safe --skip-grant-tables & mysql -u root mysql <br>
\# UPDATE user SET password=PASSWORD("new password") WHERE user='root'; FLUSH PRIVILEGES;

## 注意事项
1. `通过 systemctl 或者 service 启动mysql服务的时候，是通过调用 bin/mysqld_safe 作为守护进程去启动 bin/mysqld 进程来启动服务。`
2. `bin/mysqld 通过 bin/mysqld --help --verbose 可以查看启动配置参数。`
3. `其中my.cnf配置文件的查找过程就在输出中可以看见。 比如：` `$ bin/mysqld --help --verbose | head -n 20`

    ```text
    2022-11-08 21:26:12 0 [Note] --secure-file-priv is set to NULL. Operations related to importing and exporting data are disabled
    2022-11-08 21:26:12 0 [Note] bin/mysqld (mysqld 5.6.49) starting as process 6036 ...
    2022-11-08 21:26:12 6036 [Note] Plugin 'FEDERATED' is disabled.
    bin/mysqld  Ver 5.6.49 for linux-glibc2.12 on x86_64 (MySQL Community Server (GPL))
    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Starts the MySQL database server.

    Usage: bin/mysqld [OPTIONS]

    Default options are read from the following files in the given order:
    /etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf
    The following groups are read: mysqld server mysqld-5.6
    The following options may be given as the first argument:
    --print-defaults        Print the program argument list and exit.
    --no-defaults           Don't read default options from any option file,
                            except for login file.
    --defaults-file=#       Only read default options from the given file #.
    --defaults-extra-file=# Read this file after the global files are read.
    2022-11-08 21:26:12 6036 [Note] Binlog end
    2022-11-08 21:26:12 6036 [Note] Shutting down plugin 'CSV'
    2022-11-08 21:26:12 6036 [Note] Shutting down plugin 'MyISAM'
    ```

## Reference
* https://dev.mysql.com/downloads/mysql/
* https://developer.aliyun.com/article/951525
* [mysqld unknown option '--initialize'](https://stackoverflow.com/questions/46670368/mysqld-unknown-option-initialize)