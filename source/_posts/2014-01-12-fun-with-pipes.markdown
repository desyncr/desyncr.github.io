---
layout: post
title: "Fun with pipes"
date: 2014-01-12 11:20
comments: true
categories: 
---
One of the things that attracts me and make me love Linux for development is the flexibility and the infinite amount of ways to solve a problem it gives you.
The unix [mantra][1] says:

    "Write programs that do one thing and do it well. Write programs to work together.
    Write programs to handle text streams, because that is a universal interface."

One of the many tools this philosophy is plastered into is in names pipes. Named pipes are an easy way to provide intercomunication between different programs. While one program read from it, another one can write to it. Basically sending each other messages, communicating.
<!-- more -->

One example is the [BSPWM][3] (Binary space partitioning window manager) which uses named pipes to communicate it's internal state, such as focused windows and desktops, desktop count and so on.
There are more complex solutions such as sockets but for small things and even more more scripting named pipes are just great.

# Using pipes

It's surprising but even the most novice linux user is using pipes on a day to day basis, not knowing it:


    ls | less

The "|" (the pipe character) is interpreted by the shell and created a unnamed pipe which is used to pass the result of `ls` command to `less` as it's input.

Named pipes on the other side are persistent; they must be created and remoted explictly.

    mkfifo pipe

Here `mkfifo` created a named pipe (fifo - First in first out). Now we can write to it as anyother file:

    date > pipe

If you open it you'll see not only that there is nothing but that the `cat` command keeps on reading it.

    cat pipe

That's because pipes are streams of text, nothing stays in a pipe, things just moves through them.
Now in other terminal execute the following: (keep `cat pipe` running)

    date > pipe

You should see the `cat` terminal displaying the current date. We basically mimiced `date | cat` but with named pipes.

Things to note here:

- Named pipes are persistent. They stay on the filesystem until removed
- More than one process can read and write to it
- It's useful to think of named pipes as the common unnamed pipes but in a back and forth way of communication

# Piped logs

Now onto something more interesting. A couple of days ago I was asked if there is a integrated solution for logging (apache/postfix/etc) into a database.
Quite stunned as that's not very unixy, so to speak. I wouldn't think there is such thing around here. Anyway my proposed solution was to use (guess!) named pipes.

I kinda liked to idea and despite not being adopted at all here is a couple of scripts to implement it.

The basic workflow goes as follow:

- A named pipe is created and replaces the usual log file (say Apache's error.log)
- The script keeps on reading from it as new logs are passed to it
- Logs are handled apropiately (ie: inserted into a database)

To keep it sort here is the core part of the script:

    tail -f $pipe | while read log; do
        log "$log"
    done

Quite simple. `tail -f` reads from the piped log and pass it to the `while read` which read the stream into the `log` variable, then passing it to the `log` function. Which internally inserts the log into a database.

The complete script is on [github][2] and currently logs Apache errors into a SQLite database but of course it can be used to log anything that is written to the filesystem. It lack of parsing, though, so don't spect to run very complex queries aroung a single string column table!

  [1]: http://www.faqs.org/docs/artu/ch01s06.html
  [2]: https://github.com/desyncr/log2db
  [3]: https://github.com/baskerville/bspwm


