[

![Chi Thuc Nguyen](https://miro.medium.com/v2/resize:fill:88:88/1*y-XQSNb4DyflrONI4uKRKQ.jpeg)



](https://thucnc.medium.com/?source=post_page-----380d05a24745--------------------------------)

![](https://miro.medium.com/v2/resize:fit:700/1*bm3WGhLRaNtPMKclzYvjtQ.png)

Adding current git branch name in Bash prompt with _highlighted color_ can help to avoid a lot of problems with working on wrong git branches, which is a quite popular scenario to most of the developers sometime. This guide show to do it and explain some underneath concept of the hack.

## A quick hack

Adding this to `~/.bashrc`:

```
parse_git_branch() {     git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'}export PS1="\u@\h \[\e[32m\]\w \[\e[91m\]\$(parse_git_branch)\[\e[00m\]$ "
```

Restart the terminal, or source `~/.bashrc`, and it works:

![](https://miro.medium.com/v2/resize:fit:700/1*JFi3MdZFFVUhXWmi_AGrWA.png)

## TL; DR

## Parse git branch

The `parse_git_branch` function will return the current git branch if we are currently in a git repo directory, otherwise it will return empty string.

In this function, `git branch` command will be used. This command will list of local git branches with the `*` symbol before the current branch:

![](https://miro.medium.com/v2/resize:fit:628/1*von0p7N7REbSC_-OTuOjBg.png)

If we are not in a git repo directory, the command will output something like this to `stderr`:

![](https://miro.medium.com/v2/resize:fit:700/1*_8CONzELqUxEffJ3CzIMrA.png)

We surely don’t want this to appear in our prompt, so we redirect the `stderr` to `/dev/null` with this: `2> /dev/null`

The last part is using `sed` command to extract the current branch name (the line starting with symbol `*`) from the `git branch` output.

```
git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
```

Where `-e '/^[^*]/d'` will delete all lines that do not start with symbol \*, and the `-e 's/* \(.*\)/(\1)/'` will replace (substitute) the current branch line (with symbol `*` and a space at the beginning) with the branch name, putting it in a pair of bracket `()`.

![](https://miro.medium.com/v2/resize:fit:700/1*PQNpR2of90GQUZB1B3ACYA.png)

## What is PS1 variable

PS1 stands for _Prompt String 1_. It is the one of the prompt available in Linux/UNIX shell. When you open your terminal, it will display the content defined in `PS1` variable in your bash prompt.

Example of a PS1 string:

![](https://miro.medium.com/v2/resize:fit:649/1*XfkaC0Z69IytKf6TR-2-QQ.png)

To change this prompt, just set new value to PS1 string, for example: `PS1='\u@\h \w > '`

![](https://miro.medium.com/v2/resize:fit:484/1*ZReHPIoY-BbB26-mFq70jQ.png)

Where `\u`, `\h` and `\w` are control commands for PS1 string, which stand for username of the current user, the hostname and the current working directory respectively.

**Available control commands for** `**PS1**` **string:**

```
d - the date in "Weekday Month Date" format (e.g., "Tue May 26")e - an ASCII escape character (033)h - the hostname up to the first .H - the full hostnamej - the number of jobs currently run in backgroundl - the basename of the shells terminal device namen - newliner - carriage returns - the name of the shell, the basename of $0 (the portion following the final slash)t - the current time in 24-hour HH:MM:SS formatT - the current time in 12-hour HH:MM:SS format@ - the current time in 12-hour am/pm formatA - the current time in 24-hour HH:MM formatu - the username of the current userv - the version of bash (e.g., 4.00)V - the release of bash, version + patch level (e.g., 4.00.0)w - Complete path of current working directoryW - the basename of the current working directory! - the history number of this command# - the command number of this command$ - if the effective UID is 0, a #, otherwise a $nnn - the character corresponding to the octal number nnn\ - a backslash[ - begin a sequence of non-printing characters, which could be used to embed a terminal control sequence into the prompt] - end a sequence of non-printing characters
```

## How to set the colors

We use this sequence `\[\e[32m\]` to set the color of the text goes after that sequence, until another color is set. In this sequence, `\[` and `\]` are used to mark the start and end of non-printing characters. Between them, `\e` denotes an ASCII escape character, and follows by `[32m`, which is the actual color code.

All the color codes can be referred here: [https://misc.flogisoft.com/bash/tip\_colors\_and\_formatting](https://misc.flogisoft.com/bash/tip_colors_and_formatting)

So, in our hack:

```
export PS1="\u@\h \[\e[32m\]\w \[\e[91m\]\$(parse_git_branch)\[\e[00m\]$ "
```

the command prompt will start with _username@host_ and a _space_ (`\u@\h` ), in default terminal color (normally, white text on black background).

Then comes `\[\e[32m\]\w` — current working directory (`\w`) in green (`[32m`) color, and a space.

Next is current git branch (`\$(parse_git_branch)`) if we are in a git repo, in light red (`[91m`) color.

The last part of the prompt is symbol `$` in default (`[00m`) color.

Final result is as following:

![](https://miro.medium.com/v2/resize:fit:700/1*gx1rATaJ3noDeUAe8gKWLA.png)

## References
