Tips on Installation of [Tmux](https://github.com/tmux/tmux)
=================



1. Download the source code from https://github.com/tmux/tmux
2. Install the libevent-2.x, to solve the compile error `control.c:62: error: ‘EVBUFFER_EOL_LF’ undeclared (first use in this function)`
    *. remove the old libevent, like `sudo yum remove libevent libevent-devel libevent-headers`
    *. get the libevent-2.x from https://github.com/downloads/libevent/libevent/, ./configure && sudo make install
3. compile the tmux source.
    *. ./configure
    *. open the `Makefile`, find this line:
        `LIBS = -lutil -lcurses -levent -lrt`
        change it to this:
        `LIBS = -lutil -lcurses -levent -lrt -lresolv`
        to solve the `tty.c:1067: undefined reference to `__b64_ntop'` error
    *. make
    *. sudo make install
4. create the link file for libevent-2.x to run the tmux.
    *. sudo ln -s /usr/local/lib/libevent-2.0.so.5 /usr/lib64/libevent-2.0.so.5
5. run `tmux`, and `exit` to exit.