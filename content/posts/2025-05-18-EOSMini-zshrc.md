+++
date = '2025-05-18'
draft = false
title = 'EOS Mini: zshrc'
+++

Welcome to the first Embracing Open Source mini! There are a ton of Linux-adjacent topics that aren't often covered as a part of bigger series, so I wanted to highlight those smaller topics in mini posts. Today's subject is the `.zshrc` file. If you use fish, dash, bash, or any other shell, this won't really be specific to you, but you may still find some value here.

A command line window is made up of two things in Linux. The first, more obvious one is the Terminal Emulator. This is the program that displays the text from the system that describes commands happening on the system and allows you to interact with and start those commands. The other part is the shell itself. You can think of the shell as the program that interprets what you write into it into commands to give to the system. I use [kitty](https://sw.kovidgoyal.net/kitty/) as my terminal emulator and [zsh](https://zsh.sourceforge.io/) (Z Shell) as my shell.

`.zshrc` is the file that zsh looks at every time it's opened to set its configuration. The overall configuration for zsh is loaded at different times from different files. For example, if you log into the computer with zsh, then it will load `.zprofile` at login. All of these files are normally stored at `/home/username>/`. Since I log in using SDDM, the only zsh config file I use is `.zshrc`. Let's take a look at the file. Don't worry, we'll do it in chunks.

```shell
## Environment Variables
# Default Programs
export EDITOR="code"
export BROWSER="firefox"

# Follow XDG Base Dir Specifications
export XDG_CONFIG_HOME="$HOME/.config"
export XDG_DATA_HOME="$HOME/.local/.share"
export XDG_CACHE_HOME="$HOME/.cache"
export XDG_STATE_HOME="$HOME/.local/state"

# History files
export LESSHISTFILE="$XDG_CACHE_HOME/zsh-less-history"
export PYTHON_HISTORY="$XDG_DATA_HOME/python/history"
HISTFILE="~/.cache/zsh/zsh-history" # Moves history file to cache
# History options
HISTSIZE=1000000
SAVEHIST=1000000
HISTCONTROL=ignoreboth # Ignores duplicate and commands starting with a space
```

Here's the first section of my config. It looks like a lot, but really it's not too bad. The comments should guide us a bit. In case you've never seen scripting before, a comment is a line that the computer doesn't try to interpret. In this config, comments start with one or more hashes (#). As you can see, then, the first section sets some environment variables for default editor and browser.

Next, we have the XDG directory specification. These are commonly used locations for specific types of data. Configuration files should go into `/home/<username>/.config/` for example. The `$HOME` variable here translates to the `/home/<username>` part, so if anyone else put this line into their config, it would still work. the `XDG_DATA_HOME` can be thought of as `%APPDATA%` on a Windows machine, basically a home-folder-bound place to store application files so that the user doesn't need extended permissions to use them. The cache directory is where applications should stick their temporary files. The State directory is like a *slightly* more important version of the cache. It should house data for applications that they may need between restarts, but isn't important enough to go into the data directory.

Next, we have the history file setup. The less history file stores output history anytime the user uses the -less flag on a command or opens manual pages by executing `man <command>`. The Python history is for python commands, of course. The HISTFILE variable sets up where zsh should send command history. The options after are for the total number of lines to store and how much of that should be stored between sessions. The next line, as the comment says, tells zsh to ignore repeated commands or commands that start with a space.

```shell
# add ~/.scripts to PATH
export PATH="~/.scripts:$PATH"

# Set XDG specification locations for various environment variables
export CARGO_HOME="$XDG_DATA_HOME"/cargo
export DISCORD_USER_DATA_DIR="${XDG_DATA_HOME}"
export FFMPEG_DATADIR="$XDG_CONFIG_HOME"/ffmpeg
export GOPATH="$XDG_DATA_HOME"/go
export GTK_RC_FILES="$XDG_CONFIG_HOME"/gtk-1.0/gtkrc
export GTK2_RC_FILES="$XDG_CONFIG_HOME/gtk-2.0/gtkrc":"$XDG_CONFIG_HOME/gtk-2.0/gtkrc.mine"
export NODE_REPL_HISTORY="$XDG_DATA_HOME"/node_repl_history
export NPM_CONFIG_USERCONFIG=$XDG_CONFIG_HOME/npm/npmrc
export NUGET_PACKAGES="$XDG_CACHE_HOME"/NuGetPackages
export BUNDLE_USER_CACHE=$XDG_CACHE_HOME/bundle
export BUNDLE_USER_CONFIG=$XDG_CONFIG_HOME/bundle/config
export BUNDLE_USER_PLUGIN=$XDG_DATA_HOME/bundle
export XINITRC="$XDG_CONFIG_HOME"/X11/xinitrc
export XSERVERRC="$XDG_CONFIG_HOME"/X11/xserverrc
```

Here's another section. The first part adds my home folder Scripts directory to the PATH variable so they can be called from anywhere. You might recall a script that would be useful to call from anywhere from the [How the new Taming Knots works](/content/posts/2025-05-16-newsite). This is where that script lives.

The following section is just setting up environment variables for a bunch of stuff I have installed to make them use the correct XDG directories, since not all programs follow the specification.

```shell
# Source external files
## add wal colors support for TTY
source $XDG_CACHE_HOME/wal/colors-tty.sh

# Load modules
zmodload zsh/complist # Completion
autoload -U compinit && compinit
autoload -U colors && colors # Shell Colors

# Completion Options
zstyle ':completion:*' menu select # Set completion menu to TAB
zstyle ':completion:*' special-dirs true # force . and .. to show in Completion menu
zstyle ':completion:*' list-colors ${(s.:.)LS_COLORS} ma=0\;33 # Colorize the completion menu
zstyle ':completion:*' squeeze-slashes false # Explicitly disabled to allow /*/ expansion
```

The next section sets up colors for TTY, then starts loading modules. The first two lines are to set up the Zsh completion module. This module gives me a menu when autocomplete finds multiple entries that fit what's been typed so far. The next module sets up color highlighting for commands, including the completion menu.

![an example of the completion menu in action](/2025-05-18-16-25-24-image.png)

The next section is options for the completion module. The comments are pretty self explanatory here.

```shell
#Main options
setopt append_history inc_append_history share_history # History improvements:
# History is appended rather than overwriting, is appended as code is executed, 
# and is shared across sessions
setopt auto_menu menu_complete # Autocomplete first menu match
setopt autocd # Type the name of a dir to CD into it
setopt auto_param_slash # When a dir is autocompleted, add a slash, not a space
setopt no_case_glob no_case_match # makes autocomplete not case sensitive
setopt globdots # include dotfiles in autocomplete
setopt extended_glob # matches ~,#,^
setopt interactive_comments # allow comments in shell
setopt notify # notify in terminal when a long process completes
stty stop undef # stops accidental terminal stop
```

The next section is where we get into zsh's main options. The first one sets up some extra history stuff to make it easier to go back and check it later. The next option sets up completion so that it will automatically write what I select from the menu, so I can just keep typing to get a longer path or set arguments when running a command. The next one is really nice, it lets me `cd`, or change directory, without typing cd at the beginning of the command. For example, if I open a new shell and want to get to the Taming Knots files, I could just type `doc<TAB>P<TAB>t<TAB><Return>` to get to `Documents/Projects/taming-knots`. Similarly, the next option just makes the autocomplete end with a slash unless another key is pressed so that it's set up for moving through directories automatically and the next option makes it so that the shell doesn't care about case *only in the context of autocomplete*. Useful if I don't know what case a file might use.

The glob options below make it easier to match to files with special characters or hidden files. Interactive comments adds comments to the shell itself when running commands. By default, zsh doesn't send a notification to the system when a long command finishes. Setting the Notify option fixes that. The last one on the list is disabling the ctrl+S hotkey, which normally stops the terminal in its tracks. I never use it and it causes more problems than it solves.

```shell
# keybinds 
bindkey "^a" beginning-of-line
bindkey "^e" end-of-line

# Use Oh My Posh for prompt
eval "$(oh-my-posh init zsh --config $XDG_CONFIG_HOME/oh-my-posh/.posh.json)"

# Import colorscheme from 'wal' asynchronously
# &   # Run the process in the background.
# ( ) # Hide shell job control messages.
# Not supported in the "fish" shell.
(cat ~/.cache/wal/sequences &)
```

Final section! The first two just set some useful keybinds to go to the start or end of a line. The next one tells zsh to load up [Oh My Posh](https://ohmyposh.dev/) to make the prompt prettier. Finally, the last line tells the terminal to use colors from my wallpaper using [PyWal16](https://github.com/eylles/pywal16). 

Overall, my configuration is pretty tame, but so seldom do I see examples of configurations in Linux that I figured it would be a good idea to share and explain what I've done and why.
