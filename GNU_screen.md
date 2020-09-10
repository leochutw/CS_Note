
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
I haven't quite figured out how split-window works in screen, but you can 'Ctrl+a --> |' for vertical split and 'Ctrl+a --> S' for horizontal split, then use 'Ctrl+a --> I' to navigate around the window.  I think `tmux` does better at this.  ## TODO ##
