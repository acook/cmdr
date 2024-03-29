CMDR: Command Ruby
======

Before even glancing at this project, check out [Pry](https://github.com/pry/pry), [Rubbish](https://github.com/hayeah/rubish), and [Rush](http://rush.heroku.com/). They're all complete, functional, and probably do what I'm going to do here a lot better. Pry in particular in incredible. For the moment I have rather different goals though.

Rather Different Goals?
------

So here's the deal, years ago I read about the new fancy-shmancy [Monad](http://en.wikipedia.org/wiki/Windows_PowerShell "Yes I still call it Monad because it was an awesome name, dammit!"). It's goal, as I understood it boiled down to *commands as objects*. It was a fantastic concept. Naturally, as it has with many things, Microsoft failed to deliver.

And again, as it often does, the [Oberon](http://en.wikipedia.org/wiki/Oberon_(programming_language "The most badass shit no one has heard of.") system did it first and better. In fact I use the Oberon OS long before Monad was even thought of, but Monad reminded me of the concept again.

In both cases they assumed the underlying system was also an object system and then gave you immense power over those elements, accessible from the command line just like a standard shell. Currently, I'm not working on my own operating system (yet) so I have so such luxury. However, Unix-y systems such as Linux, OS X, BSD, Hurd, and others provide a lot of process-level information and documentation that is exceedingly simple to access and parse (once you know where to look..).

Are you getting the point any time soon?
------

I'd like to encapsulate commands and external processes as objects and replace most built-in bash commands with more object-oriented (and prettier) versions.
Really not that complex at its most basic, but believe me, it'll get complicated fast. See below for some examples of the shiny functionality I'd like it to have.


Fine, I can't endorse this endeavor, but I am curious what it looks like.
------

Alright, well met me give you a couple of examples of what I'd like to be able to do with cmdr.
*this is all pseudocode at the moment*

- Commands are persistent objects that retain attributes *(yes this could get huge, there will be limits, don't worry)*

        $ ls
        .git/  README.markdown
        $ hist
        1 ls <succeded a few seconds ago>
        $ hist.raw
        [
            #<Cmdr>,
            {command: 'ls', output: [#<Cmdr::Output::Stdout: ".git/  README.markdown">], status: #<Cmdr::Status::External::Process: pid 82960 exit 0>, type: External, start_time: 011-08-24 00:27:59 -0700, exit_time: 011-08-24 00:27:59 -0700},
            {command: 'hist', output: [#<Cmdr::CommandHistory>], status: Success, type: Internal, start_time: 011-08-24 00:28:02 -0700, exit_time: 011-08-24 00:28:03 -0700},
            {command: 'hist.raw', output: [], status: Running, type: Internal, start_time: 011-08-24 00:28:05 -0700, exit_time: nil}
        ]
        $ cmdhist[1].output
        .git/  README.markdown

- Robust directory history

        $ cd ~
        $ cd /
        $ cd.hist
        pwd /
        1   ~
        2   /home/acook/pork/cmdr
        $ back
        ~
        $ pwd
        /home/acook
        $ cd.hist
        0   /
        pwd ~
        1   ~
        2   /home/acook/pork/cmdr
        $ fore
        /
        $ pwd
        /

- Easily overridable default command options *(much like what aliases are often used for in bash)*

        $ ls.opts.defaults << '1'
        $ ls
        .git/
        README.markdown
        $ ls.opts.only :b
        .git/  README.markdown

- Session environment persistence

        $ x = 1
        x = 1
        $ ls
        .git/  README.markdown
        $ exit                    # edit cmdr
        sh-3.2$ cmdr              # rerun cmdr from sh
        cmdr v0.1
        $ hist
        1 x = 1 <succeeded about a minute ago>
        2 ls    <succeeded less than a minute ago>
        3 exit  <succedded less than a minute ago>
        $ x
        => 1

Isn't that an awful lot of work?
------

Probably. But I've wanted to do this for a while, and I imagine I'll learn a lot in the process.

What will you be able to do with the initial release (if you ever get around to it)?
------

- Standard IRB functionality

    It'll likely depend on Wirble at first, I'll use that to make it less ugly early on.

- Execute arbitrary shell commands

    I'll just use method missing and probably Popen4 to execute commands.

- Ruby-ized shell commands

    You'll be able to pass in options using symbols like `ls :b`.
    The most glaring limitation here is the bash command `ls -1` since symbols can't start with a number.
    You'll have to make due with `ls :'1'` for now.

- Basic "object command mapping" and command history

    Command history will be stored in an object with basic metadata (such as output and status information).

- Basic directory history

    This will probably start out as an array.
    I'll just truncate the array when branching off at a different point in the directory history.
    At some point, this will be a proper traversable tree object.

- More useful prompt

    Essentially a more bash-like prompt rather than an IRB-esque one.

Any pie-in-the-sky plans for it?
-----

- Creating command lines using method chaining

    Inspirate by Arel in the most vague sense, this would be the ability to chain methods together to form command lines in a more ruby-ish way.
    I'm not even sure how this would work really, but it sounds really cool right now.

- Dynamic method creation

    Most commands would have to be parsed in method_missing.
    This tends to be slow, so I'll cache successful commands.
    The first time you run an unknown command it will dynamically define a method with that name to speed up subsequent runs.

- Tokenizer

    I'll need some sort of tokenizer to pre-process the eval loop if I don't want to have to use quotes everywhere.
    That might suck so there's no promises I'll do it.


Who came up with this harebrained idea anyway?
------

**Anthony M. Cook** *2011* - @anthony_m_cook / http://anthonymcook.com / http://github.com/acook

