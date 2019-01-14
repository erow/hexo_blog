---
title: "erl 命令"
date: "2015-05-02 13:01"
categories: erlang
---
完整文档在下面，我就挑几个常用的来讲。

erl 的参数分为3类
> 1. 环境参数（就是告诉erlang VM的）以 *+*打头
> 2. 运行参数（指明以何种方式启动）  以 *-*打头
> 3. 普通参数 跟在*-extra 或 -- *之后的都是

##环境参数

##运行参数

* 启动函数:``-s``,``-run`` + ``Mod [Func [Arg1, Arg2, ...]](init flag) ``，区别是``-s``传参的类型是atom，尔``-run``传递string。
* 分布式节点:设置节点名称 ``-name``,``-sname``，节点之间通过名称来互联。``-sname``适合本机。 ``-setcookie`` 互联的节点必须有相同的cookie。
* 执行脚本:``-eval``，后面跟上erlang代码
* 其他:``-noshell``,``-detached``:后台执行,``-pa`` ``-pz``:添加代码路径


##普通参数
`` init:get_plain_arguments/0 ``取得普通参数列表。



#完整文档
```
erl(1) User Commands erl(1)
NAME
 erl - The Erlang Emulator
DESCRIPTION
 The erl program starts an Erlang runtime system. The exact details (for example, whether erl is a script or a program and which other programs it calls) are system-dependent.
 Windows users probably wants to use the werl program instead, which runs in its own window with scrollbars and supports command-line editing. The erl program on Windows provides no line editing in its shell, and on Windows 95 there is no way to scroll back
 to text which has scrolled off the screen. The erl program must be used, however, in pipelines or if you want to redirect standard input or output.
 Note:
 As of ERTS version 5.9 (OTP-R15B) the runtime system will by default not bind schedulers to logical processors. For more information see documentation of the +sbt system flag.
EXPORTS
 erl <arguments>
 Starts an Erlang runtime system.
 The arguments can be divided into emulator flags, flags and plain arguments:
 * Any argument starting with the character + is interpreted as an emulator flag.
 As indicated by the name, emulator flags controls the behavior of the emulator.
 * Any argument starting with the character - (hyphen) is interpreted as a flag which should be passed to the Erlang part of the runtime system, more specifically to the init system process, see init(3erl).
 The init process itself interprets some of these flags, the init flags. It also stores any remaining flags, the user flags. The latter can be retrieved by calling init:get_argument/1.
 It can be noted that there are a small number of "-" flags which now actually are emulator flags, see the description below.
 * Plain arguments are not interpreted in any way. They are also stored by the init process and can be retrieved by calling init:get_plain_arguments/0. Plain arguments can occur before the first flag, or after a -- flag. Additionally, the flag
 -extra causes everything that follows to become plain arguments.
 Example:
 % erl +W w -sname arnie +R 9 -s my_init -extra +bertie
 (arnie@host)1> init:get_argument(sname).
 {ok,[["arnie"]]}
 (arnie@host)2> init:get_plain_arguments().
 ["+bertie"]
 Here +W w and +R 9 are emulator flags. -s my_init is an init flag, interpreted by init. -sname arnie is a user flag, stored by init. It is read by Kernel and will cause the Erlang runtime system to become distributed. Finally, everything after
 -extra (that is, +bertie) is considered as plain arguments.
 % erl -myflag 1
 1> init:get_argument(myflag).
 {ok,[["1"]]}
 2> init:get_plain_arguments().
 []
 Here the user flag -myflag 1 is passed to and stored by the init process. It is a user defined flag, presumably used by some user defined application.
FLAGS
 In the following list, init flags are marked (init flag). Unless otherwise specified, all other flags are user flags, for which the values can be retrieved by calling init:get_argument/1. Note that the list of user flags is not exhaustive, there may be
 additional, application specific flags which instead are documented in the corresponding application documentation.
 --(init flag):
 Everything following -- up to the next flag (-flag or +flag) is considered plain arguments and can be retrieved using init:get_plain_arguments/0.
 -Application Par Val:
 Sets the application configuration parameter Par to the value Val for the application Application, see app(5) and application(3erl).
 -args_file FileName:
 Command line arguments are read from the file FileName. The arguments read from the file replace the '-args_file FileName' flag on the resulting command line.
 The file FileName should be a plain text file and may contain comments and command line arguments. A comment begins with a # character and continues until next end of line character. Backslash (\\) is used as quoting character. All command line argu鈥? ments accepted by erl are allowed, also the -args_file FileName flag. Be careful not to cause circular dependencies between files containing the -args_file flag, though.
 The -extra flag is treated specially. Its scope ends at the end of the file. Arguments following an -extra flag are moved on the command line into the -extra section, i.e. the end of the command line following after an -extra flag.
 -async_shell_start:
 The initial Erlang shell does not read user input until the system boot procedure has been completed (Erlang 5.4 and later). This flag disables the start synchronization feature and lets the shell start in parallel with the rest of the system.
 -boot File:
 Specifies the name of the boot file, File.boot, which is used to start the system. See init(3erl). Unless File contains an absolute path, the system searches for File.boot in the current and $ROOT/bin directories.
 Defaults to $ROOT/bin/start.boot.
 -boot_var Var Dir:
 If the boot script contains a path variable Var other than $ROOT, this variable is expanded to Dir. Used when applications are installed in another directory than $ROOT/lib, see systools:make_script/1,2.
 -code_path_cache:
 Enables the code path cache of the code server, see code(3erl).
 -compile Mod1 Mod2 ...:
 Compiles the specified modules and then terminates (with non-zero exit code if the compilation of some file did not succeed). Implies -noinput. Not recommended - use erlc instead.
 -config Config:
 Specifies the name of a configuration file, Config.config, which is used to configure applications. See app(5) and application(3erl).
 -connect_all false:
 If this flag is present, global will not maintain a fully connected network of distributed Erlang nodes, and then global name registration cannot be used. See global(3erl).
 -cookie Cookie:
 Obsolete flag without any effect and common misspelling for -setcookie. Use -setcookie instead.
 -detached:
 Starts the Erlang runtime system detached from the system console. Useful for running daemons and backgrounds processes. Implies -noinput.
 -emu_args:
 Useful for debugging. Prints out the actual arguments sent to the emulator.
 -env Variable Value:
 Sets the host OS environment variable Variable to the value Value for the Erlang runtime system. Example:
 % erl -env DISPLAY gin:0
 In this example, an Erlang runtime system is started with the DISPLAY environment variable set to gin:0.
 -eval Expr(init flag):
 Makes init evaluate the expression Expr, see init(3erl).
 -extra(init flag):
 Everything following -extra is considered plain arguments and can be retrieved using init:get_plain_arguments/0.
 -heart:
 Starts heart beat monitoring of the Erlang runtime system. See heart(3erl).
 -hidden:
 Starts the Erlang runtime system as a hidden node, if it is run as a distributed node. Hidden nodes always establish hidden connections to all other nodes except for nodes in the same global group. Hidden connections are not published on either of the
 connected nodes, i.e. neither of the connected nodes are part of the result from nodes/0 on the other node. See also hidden global groups, global_group(3erl).
 -hosts Hosts:
 Specifies the IP addresses for the hosts on which Erlang boot servers are running, see erl_boot_server(3erl). This flag is mandatory if the -loader inet flag is present.
 The IP addresses must be given in the standard form (four decimal numbers separated by periods, for example "150.236.20.74". Hosts names are not acceptable, but a broadcast address (preferably limited to the local network) is.
 -id Id:
 Specifies the identity of the Erlang runtime system. If it is run as a distributed node, Id must be identical to the name supplied together with the -sname or -name flag.
 -init_debug:
 Makes init write some debug information while interpreting the boot script.
 -instr(emulator flag):
 Selects an instrumented Erlang runtime system (virtual machine) to run, instead of the ordinary one. When running an instrumented runtime system, some resource usage data can be obtained and analysed using the module instrument. Functionally, it
 behaves exactly like an ordinary Erlang runtime system.
 -loader Loader:
 Specifies the method used by erl_prim_loader to load Erlang modules into the system. See erl_prim_loader(3erl). Two Loader methods are supported, efile and inet. efile means use the local file system, this is the default. inet means use a boot server
 on another machine, and the -id, -hosts and -setcookie flags must be specified as well. If Loader is something else, the user supplied Loader port program is started.
 -make:
 Makes the Erlang runtime system invoke make:all() in the current working directory and then terminate. See make(3erl). Implies -noinput.
 -man Module:
 Displays the manual page for the Erlang module Module. Only supported on Unix.
 -mode interactive | embedded:
 Indicates if the system should load code dynamically (interactive), or if all code should be loaded during system initialization (embedded), see code(3erl). Defaults to interactive.
 -name Name:
 Makes the Erlang runtime system into a distributed node. This flag invokes all network servers necessary for a node to become distributed. See net_kernel(3erl). It is also ensured that epmd runs on the current host before Erlang is started. See
 epmd(1).
 The name of the node will be Name@Host, where Host is the fully qualified host name of the current host. For short names, use the -sname flag instead.
 -noinput:
 Ensures that the Erlang runtime system never tries to read any input. Implies -noshell.
 -noshell:
 Starts an Erlang runtime system with no shell. This flag makes it possible to have the Erlang runtime system as a component in a series of UNIX pipes.
 -nostick:
 Disables the sticky directory facility of the Erlang code server, see code(3erl).
 -oldshell:
 Invokes the old Erlang shell from Erlang 3.3. The old shell can still be used.
 -pa Dir1 Dir2 ...:
 Adds the specified directories to the beginning of the code path, similar to code:add_pathsa/1. See code(3erl). As an alternative to -pa, if several directories are to be prepended to the code path and the directories have a common parent directory,
 that parent directory could be specified in the ERL_LIBS environment variable. See code(3erl).
 -pz Dir1 Dir2 ...:
 Adds the specified directories to the end of the code path, similar to code:add_pathsz/1. See code(3erl).
 -path Dir1 Dir2 ...:
 Replaces the path specified in the boot script. See script(5).
 -proto_dist Proto:
 Specify a protocol for Erlang distribution.
 inet_tcp:
 TCP over IPv4 (the default)
 inet_tls:
 distribution over TLS/SSL
 inet6_tcp:
 TCP over IPv6
 For example, to start up IPv6 distributed nodes:
 % erl -name test@ipv6node.example.com -proto_dist inet6_tcp
 -remsh Node:
 Starts Erlang with a remote shell connected to Node.
 -rsh Program:
 Specifies an alternative to rsh for starting a slave node on a remote host. See slave(3erl).
 -run Mod [Func [Arg1, Arg2, ...]](init flag):
 Makes init call the specified function. Func defaults to start. If no arguments are provided, the function is assumed to be of arity 0. Otherwise it is assumed to be of arity 1, taking the list [Arg1,Arg2,...] as argument. All arguments are passed as
 strings. See init(3erl).
 -s Mod [Func [Arg1, Arg2, ...]](init flag):
 Makes init call the specified function. Func defaults to start. If no arguments are provided, the function is assumed to be of arity 0. Otherwise it is assumed to be of arity 1, taking the list [Arg1,Arg2,...] as argument. All arguments are passed as
 atoms. See init(3erl).
 -setcookie Cookie:
 Sets the magic cookie of the node to Cookie, see erlang:set_cookie/2.
 -shutdown_time Time:
 Specifies how long time (in milliseconds) the init process is allowed to spend shutting down the system. If Time ms have elapsed, all processes still existing are killed. Defaults to infinity.
 -sname Name:
 Makes the Erlang runtime system into a distributed node, similar to -name, but the host name portion of the node name Name@Host will be the short name, not fully qualified.
 This is sometimes the only way to run distributed Erlang if the DNS (Domain Name System) is not running. There can be no communication between nodes running with the -sname flag and those running with the -name flag, as node names must be unique in
 distributed Erlang systems.
 -smp [enable|auto|disable]:
 -smp enable and -smp starts the Erlang runtime system with SMP support enabled. This may fail if no runtime system with SMP support is available. -smp auto starts the Erlang runtime system with SMP support enabled if it is available and more than one
 logical processor are detected. -smp disable starts a runtime system without SMP support.
 NOTE: The runtime system with SMP support will not be available on all supported platforms. See also the +S flag.
 -version(emulator flag):
 Makes the emulator print out its version number. The same as erl +V.
EMULATOR FLAGS
 erl invokes the code for the Erlang emulator (virtual machine), which supports the following flags:
 +a size:
 Suggested stack size, in kilowords, for threads in the async-thread pool. Valid range is 16-8192 kilowords. The default suggested stack size is 16 kilowords, i.e, 64 kilobyte on 32-bit architectures. This small default size has been chosen since the
 amount of async-threads might be quite large. The default size is enough for drivers delivered with Erlang/OTP, but might not be sufficiently large for other dynamically linked in drivers that use the driver_async() functionality. Note that the value
 passed is only a suggestion, and it might even be ignored on some platforms.
 +A size:
 Sets the number of threads in async thread pool, valid range is 0-1024. If thread support is available, the default is 10.
 +B [c | d | i]:
 The c option makes Ctrl-C interrupt the current shell instead of invoking the emulator break handler. The d option (same as specifying +B without an extra option) disables the break handler. The i option makes the emulator ignore any break signal.
 If the c option is used with oldshell on Unix, Ctrl-C will restart the shell process rather than interrupt it.
 Note that on Windows, this flag is only applicable for werl, not erl (oldshell). Note also that Ctrl-Break is used instead of Ctrl-C on Windows.
 +c true | false:
 Enable or disable time correction:
 true:
 Enable time correction. This is the default if time correction is supported on the specific platform.
 false:
 Disable time correction.
 For backwards compatibility, the boolean value can be omitted. This is interpreted as +c false.
 +C no_time_warp | single_time_warp | multi_time_warp:
 Set time warp mode:
 no_time_warp:
 No Time Warp Mode (the default)
 single_time_warp:
 Single Time Warp Mode
 multi_time_warp:
 Multi Time Warp Mode
 +d:
 If the emulator detects an internal error (or runs out of memory), it will by default generate both a crash dump and a core dump. The core dump will, however, not be very useful since the content of process heaps is destroyed by the crash dump genera鈥? tion.
 The +d option instructs the emulator to only produce a core dump and no crash dump if an internal error is detected.
 Calling erlang:halt/1 with a string argument will still produce a crash dump. On Unix systems, sending an emulator process a SIGUSR1 signal will also force a crash dump.
 +e Number:
 Set max number of ETS tables.
 +ec:
 Force the compressed option on all ETS tables. Only intended for test and evaluation.
 +fnl:
 The VM works with file names as if they are encoded using the ISO-latin-1 encoding, disallowing Unicode characters with codepoints beyond 255.
 See STDLIB User's Guide for more infomation about unicode file names. Note that this value also applies to command-line parameters and environment variables (see STDLIB User's Guide).
 +fnu[{w|i|e}]:
 The VM works with file names as if they are encoded using UTF-8 (or some other system specific Unicode encoding). This is the default on operating systems that enforce Unicode encoding, i.e. Windows and MacOS X.
 The +fnu switch can be followed by w, i, or e to control the way wrongly encoded file names are to be reported. w means that a warning is sent to the error_logger whenever a wrongly encoded file name is "skipped" in directory listings, i means that
 those wrongly encoded file names are silently ignored and e means that the API function will return an error whenever a wrongly encoded file (or directory) name is encountered. w is the default. Note that file:read_link/1 will always return an error if
 the link points to an invalid file name.
 See STDLIB User's Guide for more infomation about unicode file names. Note that this value also applies to command-line parameters and environment variables (see STDLIB User's Guide).
 +fna[{w|i|e}]:
 Selection between +fnl and +fnu is done based on the current locale settings in the OS, meaning that if you have set your terminal for UTF-8 encoding, the filesystem is expected to use the same encoding for file names. This is default on all operating
 systems except MacOS X and Windows.
 The +fna switch can be followed by w, i, or e. This will have effect if the locale settings cause the behavior of +fnu to be selected. See the description of +fnu above. If the locale settings cause the behavior of +fnl to be selected, then w, i, or e
 will not have any effect.
 See STDLIB User's Guide for more infomation about unicode file names. Note that this value also applies to command-line parameters and environment variables (see STDLIB User's Guide).
 +hms Size:
 Sets the default heap size of processes to the size Size.
 +hmbs Size:
 Sets the default binary virtual heap size of processes to the size Size.
 +hpds Size:
 Sets the initial process dictionary size of processes to the size Size.
 +K true | false:
 Enables or disables the kernel poll functionality if the emulator supports it. Default is false (disabled). If the emulator does not support kernel poll, and the +K flag is passed to the emulator, a warning is issued at startup.
 +l:
 Enables auto load tracing, displaying info while loading code.
 +L:
 Don't load information about source file names and line numbers. This will save some memory, but exceptions will not contain information about the file names and line numbers.
 +MFlag Value:
 Memory allocator specific flags, see erts_alloc(3erl) for further information.
 +n Behavior:
 Control behavior of signals to ports.
 As of OTP-R16 signals to ports are truly asynchronously delivered. Note that signals always have been documented as asynchronous. The underlying implementation has, however, previously delivered these signals synchronously. Correctly written Erlang
 programs should be able to handle this without any issues. Bugs in existing Erlang programs that make false assumptions about signals to ports may, however, be tricky to find. This switch has been introduced in order to at least make it easier to com鈥? pare behaviors during a transition period. Note that this flag is deprecated as of its introduction, and is scheduled for removal in OTP-R17. Behavior should be one of the following characters:
 d:
 The default. Asynchronous signals. A process that sends a signal to a port may continue execution before the signal has been delivered to the port.
 s:
 Synchronous signals. A processes that sends a signal to a port will not continue execution until the signal has been delivered. Should only be used for testing and debugging.
 a:
 Asynchronous signals. As the default, but a processes that sends a signal will even more frequently continue execution before the signal has been delivered to the port. Should only be used for testing and debugging.
 +pc Range:
 Sets the range of characters that the system will consider printable in heuristic detection of strings. This typically affects the shell, debugger and io:format functions (when ~tp is used in the format string).
 Currently two values for the Range are supported:
 latin1:
 The default. Only characters in the ISO-latin-1 range can be considered printable, which means that a character with a code point > 255 will never be considered printable and that lists containing such characters will be displayed as lists of inte鈥? gers rather than text strings by tools.
 unicode:
 All printable Unicode characters are considered when determining if a list of integers is to be displayed in string syntax. This may give unexpected results if for example your font does not cover all Unicode characters.
 Se also io:printable_range/0.
 +P Number|legacy:
 Sets the maximum number of simultaneously existing processes for this system if a Number is passed as value. Valid range for Number is [1024-134217727]
 NOTE: The actual maximum chosen may be much larger than the Number passed. Currently the runtime system often, but not always, chooses a value that is a power of 2. This might, however, be changed in the future. The actual value chosen can be checked
 by calling erlang:system_info(process_limit).
 The default value is 262144
 If legacy is passed as value, the legacy algorithm for allocation of process identifiers will be used. Using the legacy algorithm, identifiers will be allocated in a strictly increasing fashion until largest possible identifier has been reached. Note
 that this algorithm suffers from performance issues and can under certain circumstances be extremely expensive. The legacy algoritm is deprecated, and the legacy option is scheduled for removal in OTP-R18.
 +Q Number|legacy:
 Sets the maximum number of simultaneously existing ports for this system if a Number is passed as value. Valid range for Number is [1024-134217727]
 NOTE: The actual maximum chosen may be much larger than the actual Number passed. Currently the runtime system often, but not always, chooses a value that is a power of 2. This might, however, be changed in the future. The actual value chosen can be
 checked by calling erlang:system_info(port_limit).
 The default value used is normally 65536. However, if the runtime system is able to determine maximum amount of file descriptors that it is allowed to open and this value is larger than 65536, the chosen value will increased to a value larger or equal
 to the maximum amount of file descriptors that can be opened.
 On Windows the default value is set to 8196 because the normal OS limitations are set higher than most machines can handle.
 Previously the environment variable ERL_MAX_PORTS was used for setting the maximum number of simultaneously existing ports. This environment variable is deprecated, and scheduled for removal in OTP-R17, but can still be used.
 If legacy is passed as value, the legacy algorithm for allocation of port identifiers will be used. Using the legacy algorithm, identifiers will be allocated in a strictly increasing fashion until largest possible identifier has been reached. Note that
 this algorithm suffers from performance issues and can under certain circumstances be extremely expensive. The legacy algoritm is deprecated, and the legacy option is scheduled for removal in OTP-R18.
 +R ReleaseNumber:
 Sets the compatibility mode.
 The distribution mechanism is not backwards compatible by default. This flags sets the emulator in compatibility mode with an earlier Erlang/OTP release ReleaseNumber. The release number must be in the range <current release>-2..<current release>. This
 limits the emulator, making it possible for it to communicate with Erlang nodes (as well as C- and Java nodes) running that earlier release.
 Note: Make sure all nodes (Erlang-, C-, and Java nodes) of a distributed Erlang system is of the same Erlang/OTP release, or from two different Erlang/OTP releases X and Y, where all Y nodes have compatibility mode X.
 +r:
 Force ets memory block to be moved on realloc.
 +rg ReaderGroupsLimit:
 Limits the amount of reader groups used by read/write locks optimized for read operations in the Erlang runtime system. By default the reader groups limit equals 64.
 When the amount of schedulers is less than or equal to the reader groups limit, each scheduler has its own reader group. When the amount of schedulers is larger than the reader groups limit, schedulers share reader groups. Shared reader groups degrades
 read lock and read unlock performance while a large amount of reader groups degrades write lock performance, so the limit is a tradeoff between performance for read operations and performance for write operations. Each reader group currently consumes
 64 byte in each read/write lock. Also note that a runtime system using shared reader groups benefits from binding schedulers to logical processors, since the reader groups are distributed better between schedulers.
 +S Schedulers:SchedulerOnline:
 Sets the number of scheduler threads to create and scheduler threads to set online when SMP support has been enabled. The maximum for both values is 1024. If the Erlang runtime system is able to determine the amount of logical processors configured and
 logical processors available, Schedulers will default to logical processors configured, and SchedulersOnline will default to logical processors available; otherwise, the default values will be 1. Schedulers may be omitted if :SchedulerOnline is not and
 vice versa. The number of schedulers online can be changed at run time via erlang:system_flag(schedulers_online, SchedulersOnline).
 If Schedulers or SchedulersOnline is specified as a negative number, the value is subtracted from the default number of logical processors configured or logical processors available, respectively.
 Specifying the value 0 for Schedulers or SchedulersOnline resets the number of scheduler threads or scheduler threads online respectively to its default value.
 This option is ignored if the emulator doesn't have SMP support enabled (see the -smp flag).
 +SP SchedulersPercentage:SchedulersOnlinePercentage:
 Similar to +S but uses percentages to set the number of scheduler threads to create, based on logical processors configured, and scheduler threads to set online, based on logical processors available, when SMP support has been enabled. Specified values
 must be greater than 0. For example, +SP 50:25 sets the number of scheduler threads to 50% of the logical processors configured and the number of scheduler threads online to 25% of the logical processors available. SchedulersPercentage may be omitted
 if :SchedulersOnlinePercentage is not and vice versa. The number of schedulers online can be changed at run time via erlang:system_flag(schedulers_online, SchedulersOnline).
 This option interacts with +S settings. For example, on a system with 8 logical cores configured and 8 logical cores available, the combination of the options +S 4:4 +SP 50:25 (in either order) results in 2 scheduler threads (50% of 4) and 1 scheduler
 thread online (25% of 4).
 This option is ignored if the emulator doesn't have SMP support enabled (see the -smp flag).
 +SDcpu DirtyCPUSchedulers:DirtyCPUSchedulersOnline:
 Sets the number of dirty CPU scheduler threads to create and dirty CPU scheduler threads to set online when threading support has been enabled. The maximum for both values is 1024, and each value is further limited by the settings for normal sched鈥? ulers: the number of dirty CPU scheduler threads created cannot exceed the number of normal scheduler threads created, and the number of dirty CPU scheduler threads online cannot exceed the number of normal scheduler threads online (see the +S and +SP
 flags for more details). By default, the number of dirty CPU scheduler threads created equals the number of normal scheduler threads created, and the number of dirty CPU scheduler threads online equals the number of normal scheduler threads online.
 DirtyCPUSchedulers may be omitted if :DirtyCPUSchedulersOnline is not and vice versa. The number of dirty CPU schedulers online can be changed at run time via erlang:system_flag(dirty_cpu_schedulers_online, DirtyCPUSchedulersOnline).
 This option is ignored if the emulator doesn't have threading support enabled. Currently, this option is experimental and is supported only if the emulator was configured and built with support for dirty schedulers enabled (it's disabled by default).
 +SDPcpu DirtyCPUSchedulersPercentage:DirtyCPUSchedulersOnlinePercentage:
 Similar to +SDcpu but uses percentages to set the number of dirty CPU scheduler threads to create and number of dirty CPU scheduler threads to set online when threading support has been enabled. Specified values must be greater than 0. For example,
 +SDPcpu 50:25 sets the number of dirty CPU scheduler threads to 50% of the logical processors configured and the number of dirty CPU scheduler threads online to 25% of the logical processors available. DirtyCPUSchedulersPercentage may be omitted if
 :DirtyCPUSchedulersOnlinePercentage is not and vice versa. The number of dirty CPU schedulers online can be changed at run time via erlang:system_flag(dirty_cpu_schedulers_online, DirtyCPUSchedulersOnline).
 This option interacts with +SDcpu settings. For example, on a system with 8 logical cores configured and 8 logical cores available, the combination of the options +SDcpu 4:4 +SDPcpu 50:25 (in either order) results in 2 dirty CPU scheduler threads (50%
 of 4) and 1 dirty CPU scheduler thread online (25% of 4).
 This option is ignored if the emulator doesn't have threading support enabled. Currently, this option is experimental and is supported only if the emulator was configured and built with support for dirty schedulers enabled (it's disabled by default).
 +SDio IOSchedulers:
 Sets the number of dirty I/O scheduler threads to create when threading support has been enabled. The valid range is 0-1024. By default, the number of dirty I/O scheduler threads created is 10, same as the default number of threads in the async thread
 pool .
 This option is ignored if the emulator doesn't have threading support enabled. Currently, this option is experimental and is supported only if the emulator was configured and built with support for dirty schedulers enabled (it's disabled by default).
 +sFlag Value:
 Scheduling specific flags.
 +sbt BindType:
 Set scheduler bind type.
 Schedulers can also be bound using the +stbt flag. The only difference between these two flags is how the following errors are handled:
 * Binding of schedulers is not supported on the specific platform.
 * No available CPU topology. That is the runtime system was not able to automatically detected the CPU topology, and no user defined CPU topology was set.
 If any of these errors occur when +sbt has been passed, the runtime system will print an error message, and refuse to start. If any of these errors occur when +stbt has been passed, the runtime system will silently ignore the error, and start up
 using unbound schedulers.
 Currently valid BindTypes:
 u:
 unbound - Schedulers will not be bound to logical processors, i.e., the operating system decides where the scheduler threads execute, and when to migrate them. This is the default.
 ns:
 no_spread - Schedulers with close scheduler identifiers will be bound as close as possible in hardware.
 ts:
 thread_spread - Thread refers to hardware threads (e.g. Intel's hyper-threads). Schedulers with low scheduler identifiers, will be bound to the first hardware thread of each core, then schedulers with higher scheduler identifiers will be bound to
 the second hardware thread of each core, etc.
 ps:
 processor_spread - Schedulers will be spread like thread_spread, but also over physical processor chips.
 s:
 spread - Schedulers will be spread as much as possible.
 nnts:
 no_node_thread_spread - Like thread_spread, but if multiple NUMA (Non-Uniform Memory Access) nodes exists, schedulers will be spread over one NUMA node at a time, i.e., all logical processors of one NUMA node will be bound to schedulers in
 sequence.
 nnps:
 no_node_processor_spread - Like processor_spread, but if multiple NUMA nodes exists, schedulers will be spread over one NUMA node at a time, i.e., all logical processors of one NUMA node will be bound to schedulers in sequence.
 tnnps:
 thread_no_node_processor_spread - A combination of thread_spread, and no_node_processor_spread. Schedulers will be spread over hardware threads across NUMA nodes, but schedulers will only be spread over processors internally in one NUMA node at a
 time.
 db:
 default_bind - Binds schedulers the default way. Currently the default is thread_no_node_processor_spread (which might change in the future).
 Binding of schedulers is currently only supported on newer Linux, Solaris, FreeBSD, and Windows systems.
 If no CPU topology is available when the +sbt flag is processed and BindType is any other type than u, the runtime system will fail to start. CPU topology can be defined using the +sct flag. Note that the +sct flag may have to be passed before the
 +sbt flag on the command line (in case no CPU topology has been automatically detected).
 The runtime system will by default not bind schedulers to logical processors.
 NOTE: If the Erlang runtime system is the only operating system process that binds threads to logical processors, this improves the performance of the runtime system. However, if other operating system processes (as for example another Erlang runtime
 system) also bind threads to logical processors, there might be a performance penalty instead. In some cases this performance penalty might be severe. If this is the case, you are advised to not bind the schedulers.
 How schedulers are bound matters. For example, in situations when there are fewer running processes than schedulers online, the runtime system tries to migrate processes to schedulers with low scheduler identifiers. The more the schedulers are spread
 over the hardware, the more resources will be available to the runtime system in such situations.
 NOTE: If a scheduler fails to bind, this will often be silently ignored. This since it isn't always possible to verify valid logical processor identifiers. If an error is reported, it will be reported to the error_logger. If you want to verify that
 the schedulers actually have bound as requested, call erlang:system_info(scheduler_bindings).
 +sbwt none|very_short|short|medium|long|very_long:
 Set scheduler busy wait threshold. Default is medium. The threshold determines how long schedulers should busy wait when running out of work before going to sleep.
 NOTE: This flag may be removed or changed at any time without prior notice.
 +scl true|false:
 Enable or disable scheduler compaction of load. By default scheduler compaction of load is enabled. When enabled, load balancing will strive for a load distribution which causes as many scheduler threads as possible to be fully loaded (i.e., not run
 out of work). This is accomplished by migrating load (e.g. runnable processes) into a smaller set of schedulers when schedulers frequently run out of work. When disabled, the frequency with which schedulers run out of work will not be taken into
 account by the load balancing logic.
 +scl false is similar to +sub true with the difference that +sub true also will balance scheduler utilization between schedulers.
 +sct CpuTopology:
 * <Id> = integer(); when 0 =< <Id> =< 65535
 * <IdRange> = <Id>-<Id>
 * <IdOrIdRange> = <Id> | <IdRange>
 * <IdList> = <IdOrIdRange>,<IdOrIdRange> | <IdOrIdRange>
 * <LogicalIds> = L<IdList>
 * <ThreadIds> = T<IdList> | t<IdList>
 * <CoreIds> = C<IdList> | c<IdList>
 * <ProcessorIds> = P<IdList> | p<IdList>
 * <NodeIds> = N<IdList> | n<IdList>
 * <IdDefs> = <LogicalIds><ThreadIds><CoreIds><ProcessorIds><NodeIds> | <LogicalIds><ThreadIds><CoreIds><NodeIds><ProcessorIds>
 * CpuTopology = <IdDefs>:<IdDefs> | <IdDefs>
 Set a user defined CPU topology. The user defined CPU topology will override any automatically detected CPU topology. The CPU topology is used when binding schedulers to logical processors.
 Upper-case letters signify real identifiers and lower-case letters signify fake identifiers only used for description of the topology. Identifiers passed as real identifiers may be used by the runtime system when trying to access specific hardware
 and if they are not correct the behavior is undefined. Faked logical CPU identifiers are not accepted since there is no point in defining the CPU topology without real logical CPU identifiers. Thread, core, processor, and node identifiers may be left
 out. If left out, thread id defaults to t0, core id defaults to c0, processor id defaults to p0, and node id will be left undefined. Either each logical processor must belong to one and only one NUMA node, or no logical processors must belong to any
 NUMA nodes.
 Both increasing and decreasing <IdRange>s are allowed.
 NUMA node identifiers are system wide. That is, each NUMA node on the system have to have a unique identifier. Processor identifiers are also system wide. Core identifiers are processor wide. Thread identifiers are core wide.
 The order of the identifier types imply the hierarchy of the CPU topology. Valid orders are either <LogicalIds><ThreadIds><CoreIds><ProcessorIds><NodeIds>, or <LogicalIds><ThreadIds><CoreIds><NodeIds><ProcessorIds>. That is, thread is part of a core
 which is part of a processor which is part of a NUMA node, or thread is part of a core which is part of a NUMA node which is part of a processor. A cpu topology can consist of both processor external, and processor internal NUMA nodes as long as each
 logical processor belongs to one and only one NUMA node. If <ProcessorIds> is left out, its default position will be before <NodeIds>. That is, the default is processor external NUMA nodes.
 If a list of identifiers is used in an <IdDefs>:
 * <LogicalIds> have to be a list of identifiers.
 * At least one other identifier type apart from <LogicalIds> also have to have a list of identifiers.
 * All lists of identifiers have to produce the same amount of identifiers.
 A simple example. A single quad core processor may be described this way:
 % erl +sct L0-3c0-3
 1> erlang:system_info(cpu_topology).
 [{processor,[{core,{logical,0}},
 {core,{logical,1}},
 {core,{logical,2}},
 {core,{logical,3}}]}]
 A little more complicated example. Two quad core processors. Each processor in its own NUMA node. The ordering of logical processors is a little weird. This in order to give a better example of identifier lists:
 % erl +sct L0-1,3-2c0-3p0N0:L7,4,6-5c0-3p1N1
 1> erlang:system_info(cpu_topology).
 [{node,[{processor,[{core,{logical,0}},
 {core,{logical,1}},
 {core,{logical,3}},
 {core,{logical,2}}]}]},
 {node,[{processor,[{core,{logical,7}},
 {core,{logical,4}},
 {core,{logical,6}},
 {core,{logical,5}}]}]}]
 As long as real identifiers are correct it is okay to pass a CPU topology that is not a correct description of the CPU topology. When used with care this can actually be very useful. This in order to trick the emulator to bind its schedulers as you
 want. For example, if you want to run multiple Erlang runtime systems on the same machine, you want to reduce the amount of schedulers used and manipulate the CPU topology so that they bind to different logical CPUs. An example, with two Erlang run鈥? time systems on a quad core machine:
 % erl +sct L0-3c0-3 +sbt db +S3:2 -detached -noinput -noshell -sname one
 % erl +sct L3-0c0-3 +sbt db +S3:2 -detached -noinput -noshell -sname two
 In this example each runtime system have two schedulers each online, and all schedulers online will run on different cores. If we change to one scheduler online on one runtime system, and three schedulers online on the other, all schedulers online
 will still run on different cores.
 Note that a faked CPU topology that does not reflect how the real CPU topology looks like is likely to decrease the performance of the runtime system.
 For more information, see erlang:system_info(cpu_topology).
 +secio true|false:
 Enable or disable eager check I/O scheduling. The default is currently true. The default was changed from false to true as of erts version 7.0. The behaviour before this flag was introduced corresponds to +secio false.
 The flag effects when schedulers will check for I/O operations possible to execute, and when such I/O operations will execute. As the name of the parameter implies, schedulers will be more eager to check for I/O when true is passed. This however also
 implies that execution of outstanding I/O operation will not be prioritized to the same extent as when false is passed.
 erlang:system_info(eager_check_io) returns the value of this parameter used when starting the VM.
 +sfwi Interval:
 Set scheduler forced wakeup interval. All run queues will be scanned each Interval milliseconds. While there are sleeping schedulers in the system, one scheduler will be woken for each non-empty run queue found. An Interval of zero disables this fea鈥? ture, which also is the default.
 This feature has been introduced as a temporary workaround for long-executing native code, and native code that does not bump reductions properly in OTP. When these bugs have be fixed the +sfwi flag will be removed.
 +stbt BindType:
 Try to set scheduler bind type. The same as the +sbt flag with the exception of how some errors are handled. For more information, see the documentation of the +sbt flag.
 +sub true|false:
 Enable or disable scheduler utilization balancing of load. By default scheduler utilization balancing is disabled and instead scheduler compaction of load is enabled which will strive for a load distribution which causes as many scheduler threads as
 possible to be fully loaded (i.e., not run out of work). When scheduler utilization balancing is enabled the system will instead try to balance scheduler utilization between schedulers. That is, strive for equal scheduler utilization on all sched鈥? ulers.
 +sub true is only supported on systems where the runtime system detects and uses a monotonically increasing high resolution clock. On other systems, the runtime system will fail to start.
 +sub true implies +scl false. The difference between +sub true and +scl false is that +scl false will not try to balance the scheduler utilization.
 +swct very_eager|eager|medium|lazy|very_lazy:
 Set scheduler wake cleanup threshold. Default is medium. This flag controls how eager schedulers should be requesting wake up due to certain cleanup operations. When a lazy setting is used, more outstanding cleanup operations can be left undone while
 a scheduler is idling. When an eager setting is used, schedulers will more frequently be woken, potentially increasing CPU-utilization.
 NOTE: This flag may be removed or changed at any time without prior notice.
 +sws default|legacy:
 Set scheduler wakeup strategy. Default strategy changed in erts-5.10/OTP-R16A. This strategy was previously known as proposal in OTP-R15. The legacy strategy was used as default from R13 up to and including R15.
 NOTE: This flag may be removed or changed at any time without prior notice.
 +swt very_low|low|medium|high|very_high:
 Set scheduler wakeup threshold. Default is medium. The threshold determines when to wake up sleeping schedulers when more work than can be handled by currently awake schedulers exist. A low threshold will cause earlier wakeups, and a high threshold
 will cause later wakeups. Early wakeups will distribute work over multiple schedulers faster, but work will more easily bounce between schedulers.
 NOTE: This flag may be removed or changed at any time without prior notice.
 +spp Bool:
 Set default scheduler hint for port parallelism. If set to true, the VM will schedule port tasks when doing so will improve parallelism in the system. If set to false, the VM will try to perform port tasks immediately, improving latency at the
 expense of parallelism. If this flag has not been passed, the default scheduler hint for port parallelism is currently false. The default used can be inspected in runtime by calling erlang:system_info(port_parallelism). The default can be overriden
 on port creation by passing the parallelism option to open_port/2.
 +sss size:
 Suggested stack size, in kilowords, for scheduler threads. Valid range is 4-8192 kilowords. The default stack size is OS dependent.
 +t size:
 Set the maximum number of atoms the VM can handle. Default is 1048576.
 +T Level:
 Enables modified timing and sets the modified timing level. Currently valid range is 0-9. The timing of the runtime system will change. A high level usually means a greater change than a low level. Changing the timing can be very useful for finding
 timing related bugs.
 Currently, modified timing affects the following:
 Process spawning:
 A process calling spawn, spawn_link, spawn_monitor, or spawn_opt will be scheduled out immediately after completing the call. When higher modified timing levels are used, the caller will also sleep for a while after being scheduled out.
 Context reductions:
 The amount of reductions a process is a allowed to use before being scheduled out is increased or reduced.
 Input reductions:
 The amount of reductions performed before checking I/O is increased or reduced.
 NOTE: Performance will suffer when modified timing is enabled. This flag is only intended for testing and debugging. Also note that return_to and return_from trace messages will be lost when tracing on the spawn BIFs. This flag may be removed or
 changed at any time without prior notice.
 +V:
 Makes the emulator print out its version number.
 +v:
 Verbose.
 +W w | i | e:
 Sets the mapping of warning messages for error_logger. Messages sent to the error logger using one of the warning routines can be mapped either to errors (+W e), warnings (+W w), or info reports (+W i). The default is warnings. The current mapping can
 be retrieved using error_logger:warning_map/0. See error_logger(3erl) for further information.
 +zFlag Value:
 Miscellaneous flags.
 +zdbbl size:
 Set the distribution buffer busy limit (dist_buf_busy_limit) in kilobytes. Valid range is 1-2097151. Default is 1024.
 A larger buffer limit will allow processes to buffer more outgoing messages over the distribution. When the buffer limit has been reached, sending processes will be suspended until the buffer size has shrunk. The buffer limit is per distribution
 channel. A higher limit will give lower latency and higher throughput at the expense of higher memory usage.
 +zdntgc time:
 Set the delayed node table garbage collection time (delayed_node_table_gc) in seconds. Valid values are either infinity or an integer in the range [0-100000000]. Default is 60.
 Node table entries that are not referred will linger in the table for at least the amount of time that this parameter determines. The lingering prevents repeated deletions and insertions in the tables from occurring.
ENVIRONMENT VARIABLES
 ERL_CRASH_DUMP:
 If the emulator needs to write a crash dump, the value of this variable will be the file name of the crash dump file. If the variable is not set, the name of the crash dump file will be erl_crash.dump in the current directory.
 ERL_CRASH_DUMP_NICE:
 Unix systems: If the emulator needs to write a crash dump, it will use the value of this variable to set the nice value for the process, thus lowering its priority. The allowable range is 1 through 39 (higher values will be replaced with 39). The high鈥? est value, 39, will give the process the lowest priority.
 ERL_CRASH_DUMP_SECONDS:
 Unix systems: This variable gives the number of seconds that the emulator will be allowed to spend writing a crash dump. When the given number of seconds have elapsed, the emulator will be terminated by a SIGALRM signal.
 If the environment variable is not set or it is set to zero seconds, ERL_CRASH_DUMP_SECONDS=0, the runtime system will not even attempt to write the crash dump file. It will just terminate.
 If the environment variable is set to negative valie, e.g. ERL_CRASH_DUMP_SECONDS=-1, the runtime system will wait indefinitely for the crash dump file to be written.
 This environment variable is used in conjuction with heart if heart is running:
 ERL_CRASH_DUMP_SECONDS=0:
 Suppresses the writing a crash dump file entirely, thus rebooting the runtime system immediately. This is the same as not setting the environment variable.
 ERL_CRASH_DUMP_SECONDS=-1:
 Setting the environment variable to a negative value will cause the termination of the runtime system to wait until the crash dump file has been completly written.
 ERL_CRASH_DUMP_SECONDS=S:
 Will wait for S seconds to complete the crash dump file and then terminate the runtime system.
 ERL_AFLAGS:
 The content of this environment variable will be added to the beginning of the command line for erl.
 The -extra flag is treated specially. Its scope ends at the end of the environment variable content. Arguments following an -extra flag are moved on the command line into the -extra section, i.e. the end of the command line following after an -extra
 flag.
 ERL_ZFLAGS and ERL_FLAGS:
 The content of these environment variables will be added to the end of the command line for erl.
 The -extra flag is treated specially. Its scope ends at the end of the environment variable content. Arguments following an -extra flag are moved on the command line into the -extra section, i.e. the end of the command line following after an -extra
 flag.
 ERL_LIBS:
 This environment variable contains a list of additional library directories that the code server will search for applications and add to the code path. See code(3erl).
 ERL_EPMD_ADDRESS:
 This environment variable may be set to a comma-separated list of IP addresses, in which case the epmd daemon will listen only on the specified address(es) and on the loopback address (which is implicitly added to the list if it has not been speci鈥? fied).
 ERL_EPMD_PORT:
 This environment variable can contain the port number to use when communicating with epmd. The default port will work fine in most cases. A different port can be specified to allow nodes of independent clusters to co-exist on the same host. All nodes
 in a cluster must use the same epmd port number.
CONFIGURATION
 The standard Erlang/OTP system can be re-configured to change the default behavior on start-up.
 The .erlang Start-up File:
 When Erlang/OTP is started, the system searches for a file named .erlang in the directory where Erlang/OTP is started. If not found, the user's home directory is searched for an .erlang file.
 If an .erlang file is found, it is assumed to contain valid Erlang expressions. These expressions are evaluated as if they were input to the shell.
 A typical .erlang file contains a set of search paths, for example:
 io:format("executing user profile in HOME/.erlang\n",[]).
 code:add_path("/home/calvin/test/ebin").
 code:add_path("/home/hobbes/bigappl-1.2/ebin").
 io:format(".erlang rc finished\n",[]).
 user_default and shell_default:
 Functions in the shell which are not prefixed by a module name are assumed to be functional objects (Funs), built-in functions (BIFs), or belong to the module user_default or shell_default.
 To include private shell commands, define them in a module user_default and add the following argument as the first line in the .erlang file.
 code:load_abs("..../user_default").
 erl:
 If the contents of .erlang are changed and a private version of user_default is defined, it is possible to customize the Erlang/OTP environment. More powerful changes can be made by supplying command line arguments in the start-up script erl. Refer to
 erl(1) and init(3erl) for further information.
SEE ALSO
 init(3erl), erl_prim_loader(3erl), erl_boot_server(3erl), code(3erl), application(3erl), heart(3erl), net_kernel(3erl), auth(3erl), make(3erl), epmd(1), erts_alloc(3erl)
Ericsson AB erts 7.3 erl(1)
```
