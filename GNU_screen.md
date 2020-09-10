
# Background
I work a lot with terminal windows, particularly remotely log into a Unix system from MS-Windows machine.  The terminal session is lost whenever I close the lid on my MS-Windows laptop, and it's annoying to start the terminal from scratch again.  Luckily screen/tmux multiplexer saves my day by continously running on the remote host machine.  All I have to do is reattach my screen session.  This note contains some of the tips on using the `screen`.

# Introduction

## Built-in Help
By pressing "Ctrl+a --> ?" to help the Built-in Help
```
                                        Command key:  ^A   Literal ^A:  a

break       ^B b       hardcopy    h          monitor     M          remove      X          version     v
clear       C          help        ?          next        ^@ ^N sp n removebuf   =          width       W
colon       :          history     { }        number      N          reset       Z          windows     ^W w
copy        ^[ [       info        i          only        Q          screen      ^C c       wrap        ^R r
detach      ^D d       kill        ^K k       other       ^A         select      '          writebuf    >
digraph     ^V         lastmsg     ^M m       pow_break   B          silence     _          xoff        ^S s
displays    *          license     ,          pow_detach  D          split       S          xon         ^Q q
dumptermcap .          lockscreen  ^X x       prev        ^H ^P p ^? suspend     ^Z z
fit         F          log         H          quit        ^\         time        ^T t
flow        ^F f       login       L          readbuf     <          title       A
focus       ^I         meta        a          redisplay   ^L l       vbell       ^G

^]   paste .
"    windowlist -b
-    select -
0    select 0
1    select 1
2    select 2
3    select 3
4    select 4
5    select 5
6    select 6
7    select 7
8    select 8
9    select 9
]    paste .
|    split -v
:kB: focus prev
```
### info `Ctrl+a --> i`
When screen displays its info on its status-bar, the following information is display
```
(42,42)/(115,42)+10000 +flow UTF-8 0(bash)
                                      \_ user's shell(default) which uses /dev/pts/X pesudo-tty device
```

```
(1,24)/(84,43)+1024 +flow UTF-8 {RTS (CTS) DTR DSR (CD)} 0(ttyS0)
        \      \     \     \     \                       \   \_ tty device (physical serial port here)
         \      \     \     \     \                       \_ window number: 0
          \      \     \     \     \_ tty device settings (RS-232 serial settings here)
           \      \     \     \_ character encoding scheme
            \      \     \_ flow-control ??
             \      \_ scrollback buffer size  (see defscrollback or scrollback variables)
              \_ maximum (column,row) size for the current window (dynamically resizable when terminal size increases)    
                   
                   
```


## My .screenrc resource file
`screen` will not show the title-bar by default, so it's very difficult to tell which terminal we're in.  The user can assign a title by `Ctrl+a --> a` key-combo, however, I prefer the title-bar to be automatically assigned during screen startup.  Following is my `.screenrc` resource file which I found it to be very useful.

```text
### Custom Changes ###
### Terminal Type (only for colored display in Solaris 8 as the early xterm does not support color)
term "dtterm"

# skip the startup message
startup_message off
# Automatically detach on hangup.
autodetach on
# If a screen dies, don~Rt freeze the whole screen waiting for it.
nonblock on
# UTF-8 is necessary.
defutf8 on
# Change default scrollback value for new windows
defscrollback 10000
# scrollback 10000
# start with visual bell as default
vbell off
vbell_msg ~SBell on %t (%n)~T
# look and feel
# caption always ~S%3n %t%? @%u%?%? [%h]%?%=%c~T
# termcapinfo xterm ~Qhs:ts=\E]2;:fs=07:ds=\E]2;screen07'
hardstatus alwaysignore
# hardstatus alwayslastline ~Q%{g}%-w%{b}%n %t%{-}%+w %<%{kk}'
hardstatus alwayslastline '%{bk}%-w%<%{gk}%n %t%{-}%+w %<%{kk}'
activity "Activity in %t (%n)"
# bindkey -k k7 prev      ## not needed, since "C-a p" does the same thing.
# bindkey -k k8 next      ## not needed, since "C-a n" does the same thing.
# bindkey -k k9 title     ## can be achieve with "C-a A", but not easy to remember, so use "title" command
# bindkey -k k5 screen    ## not needed, since "C-a c" does the same thing.

# Enable mouse scrolling and scroll bar history scrolling
termcapinfo xterm* ti@:te@
```

# Split Window
I haven't quite figured out how split-window works in screen, but you can 'Ctrl+a --> |' for vertical split and 'Ctrl+a --> S' for horizontal split, then use 'Ctrl+a --> Ctrl+I' or 'Ctrl+a --> Tab' to navigate around the window.  I think `tmux` does better at this.  ## TODO ##



# Other Tips
## `screen` and serial port
mmm.. I couldn't get the `parenb`: parity enable to work in my Linux box.  Not sure why, its syntax seems to be correct as `screen /dev/ttyUSB0 9600,cs8` is working fine.
The communication settings are a comma separated list of control modes as would be passed to `stty`. See the man page for `stty` for more info. 
```
# ref. http://www.noah.org/wiki/Screen_notes
screen /dev/ttyS0 9600,cs8,-parenb,-cstopb,-hupcl
screen /dev/ttyS0 19200,cs8,-parenb,-cstopb,-hupcl
screen /dev/ttyS0 115200,cs8,-parenb,-cstopb,-hupcl
# use odd parity:
screen /dev/ttyS0 9600,cs8,parenb,parodd,-cstopb,-hupcl
# even parity:
screen /dev/ttyS0 9600,cs8,parenb,-parodd,-cstopb,-hupcl
```
However, the system doesn't seem to respect this. Here are the settings as reported by stty while I'm in screen with the above command:
``` text
$ sudo stty -F /dev/ttyUSB2 -a
speed 115200 baud; rows 0; columns 0; line = 0;
intr = ^C; quit = ^\; erase = ^?; kill = ^H; eof = ^D; eol = <undef>; eol2 = <undef>; swtch = <undef>; start = ^Q; stop = ^S;
susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V; flush = ^O; min = 100; time = 2;
-parenb -parodd cs8 -hupcl -cstopb cread clocal -crtscts
-ignbrk brkint ignpar -parmrk -inpck -istrip -inlcr -igncr -icrnl ixon -ixoff -iuclc -ixany -imaxbel -iutf8
-opost -olcuc -ocrnl -onlcr -onocr -onlret -ofill -ofdel nl0 cr0 tab0 bs0 vt0 ff0
-isig -icanon iexten -echo echoe echok -echonl -noflsh -xcase -tostop -echoprt echoctl echoke
```

The best way to work around serial setting problem with `screen` is via `stty` command.  Linux treats serial devices as tty with its control-character.

From the man page for stty:
* csN - set character size to N bits, N in [5..8]
* [-]parenb - generate parity bit in output and expect parity bit in input
* [-]parodd - set odd parity (even with '-')
* [-]cstopb - use two stop bits per character (one with '-')

lchu: mmm...  if you check the screen source, "braille.c" module, then you will understand why those extra arguments are ignored by screen.
```C
static int
open_braille_device(s)
char *s;
{
  char str[256];

  sprintf(str, "%d cs8 -istrip ixon ixoff", bd.bd_baud);
  bd.bd_fd = OpenTTY(bd.bd_port, str);                                /* <--- LOOK HERE */
  if (bd.bd_fd == -1)
    {
      Msg(errno, "open comm port failed: %s ", bd.bd_port);
      return -1;
    }
  fcntl(bd.bd_fd, F_SETFL, FNBLOCK);
  return 0;
}
```
