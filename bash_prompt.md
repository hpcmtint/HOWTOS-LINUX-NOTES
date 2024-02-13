[![](https://www.cyberciti.biz/media/new/category/old/terminal.png)](https://www.cyberciti.biz/tips/category/shell-scripting "See all Bash/Shell scripting related tips/articles")

So how do you setup, change and pimp out Linux / UNIX shell prompt? Most of us work with a shell prompt. By default most Linux distro displays hostname and current working directory. You can easily customize your prompt to display information important to you. You change look and feel by adding colors. In this small howto I will explain howto setup:

  

nixCraft: Privacy First, Reader Supported

-   **nixCraft is a one-person operation**. I create all the content myself, with no help from AI or ML. I keep the content accurate and up-to-date.
-   **Your privacy is my top priority**. I don’t track you, show you ads, or spam you with emails. Just pure content in the true spirit of Linux and FLOSS.
-   **Fast and clean browsing experience**. nixCraft is designed to be fast and easy to use. You won’t have to deal with pop-ups, ads, cookie banners, or other distractions.
-   **Support independent content creators**. nixCraft is a labor of love, and it’s only possible thanks to the support of our readers. If you enjoy the content, please support us on Patreon or share this page on social media or your blog. Every bit helps.

[Join **Patreon** ➔](https://www.patreon.com/nixcraft)

1.  How to customizing a bash shell to get a good looking prompt
2.  Configure the appearance of the terminal
3.  Apply themes using bashish
4.  How to pimp out your shell prompt

## How To Customize Bash Prompt In Linux

Prompt is control via a special shell variable. You need to set PS1, PS2, PS3 and PS4 variable. If set, the value is executed as a command prior to issuing each primary prompt. From the bash command:

-   **PS0** – The value of this parameter is expanded (see PROMPTING below) and displayed by interactive shells after reading a command and before the command is executed.
-   **PS1** – The value of this parameter is expanded (see PROMPTING below) and used as the primary prompt string. The default value is **\\s-\\v\\$** .
-   **PS2**– The value of this parameter is expanded as with PS1 and used as the secondary prompt string. The default is **\>**
-   **PS3** – The value of this parameter is used as the prompt for the select command
-   **PS4** – The value of this parameter is expanded as with PS1 and the value is printed before each command bash displays during an execution trace. The first character of PS4 is replicated multiple times, as necessary, to indicate multiple levels of indirection. The default is **+**

I tested PS{0..4} on [bash version number](https://www.cyberciti.biz/faq/how-do-i-check-my-bash-version/):

```
GNU bash, version 5.0.17(1)-release
```

### How do I display current bash shell prompt setting?

Simply use the [echo command](https://bash.cyberciti.biz/guide/Echo_Command "Echo Command - Linux Bash Shell Scripting Tutorial Wiki") or [printf command](https://bash.cyberciti.biz/guide/Printf_command "Printf command - Linux Bash Shell Scripting Tutorial Wiki"), enter:  
`$ echo "$PS1"   $ printf "%s\n" "$PS1"`  
Sample output:

```
[\u@\h \W]\$
```

Of course, you can use the [grep command](https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/ "How to use grep command in Linux/ Unix with examples") or [egrep command](https://www.cyberciti.biz/faq/grep-regular-expressions/ "Regular expressions in grep ( regex ) with examples") to read the current settings from your ~/.bashrc or [~.bash\_profile](https://bash.cyberciti.biz/guide/.bash_profile ".bash profile - Linux Bash Shell Scripting Tutorial Wiki") as follows:  
`$ grep PS1 ~/.bash_profile`

```
export PS1<span>=</span><span>"\[\e[31m\][\[\e[m\]\[\e[38;5;172m\]\u\[\e[m\]@\[\e[38;5;153m\]\h\[\e[m\] \[\e[38;5;214m\]\W\[\e[m\]\[\e[31m\]]\[\e[m\]\\$ "</span>
```

Use the [cat command](https://www.cyberciti.biz/faq/linux-unix-appleosx-bsd-cat-command-examples/ "cat Command in Linux / Unix with examples") or [more command](https://bash.cyberciti.biz/guide/More_command "More command - Linux Bash Shell Scripting Tutorial Wiki") or [less command](https://bash.cyberciti.biz/guide/Less_command "Less command - Linux Bash Shell Scripting Tutorial Wiki") to read system wide PS1 and other settings:  
`$ more /etc/profile`

```
<span># /etc/profile: system-wide .profile file for the Bourne shell (sh(1))</span>
<span># and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).</span>
&nbsp;
<span>if</span> <span>[</span> <span>"<span>${PS1-}</span>"</span> <span>]</span>; <span>then</span>
  <span>if</span> <span>[</span> <span>"<span>${BASH-}</span>"</span> <span>]</span> <span>&amp;&amp;</span> <span>[</span> <span>"<span>$BASH</span>"</span> <span>!</span>= <span>"/bin/sh"</span> <span>]</span>; <span>then</span>
    <span># The file bash.bashrc already sets the default PS1.</span>
    <span># PS1='\h:\w\$ '</span>
    <span>if</span> <span>[</span> <span>-f</span> <span>/</span>etc<span>/</span>bash.bashrc <span>]</span>; <span>then</span>
      . <span>/</span>etc<span>/</span>bash.bashrc
    <span>fi</span>
  <span>else</span>
    <span>if</span> <span>[</span> <span>"<span>`id -u`</span>"</span> <span>-eq</span> <span>0</span> <span>]</span>; <span>then</span>
      <span>PS1</span>=<span>'# '</span>
    <span>else</span>
      <span>PS1</span>=<span>'$ '</span>
    <span>fi</span>
  <span>fi</span>
<span>fi</span>
&nbsp;
<span>if</span> <span>[</span> <span>-d</span> <span>/</span>etc<span>/</span>profile.d <span>]</span>; <span>then</span>
  <span>for</span> i <span>in</span> <span>/</span>etc<span>/</span>profile.d<span>/*</span>.sh; <span>do</span>
    <span>if</span> <span>[</span> <span>-r</span> <span>$i</span> <span>]</span>; <span>then</span>
      . <span>$i</span>
    <span>fi</span>
  <span>done</span>
  <span>unset</span> i
<span>fi</span>
```

### How do I modify or change the prompt?

Modifying the prompt is easy task. Just assign a new value to PS1 and hit enter key:  
My old prompt –> \[vivek@105r2 ~\]$  
`$ PS1="touch me : "`  
Sample outputs:

```
touch me : 
```

We can set PS0 too:  
`$ PS0='>>outputs<<\n'`  
So when executing interactively, bash displays the primary prompt PS1 when it is ready to read a command, and the secondary prompt PS2 when it needs more input to complete a command. Bash allows these prompt strings to be customized by inserting a number of backslash-escaped special characters that are decoded as follows:

-   **\\a** : an ASCII bell character (07)
-   **\\d** : the date in "Weekday Month Date" format (e.g., "Tue May 26")
-   **\\D{format}** : the format is passed to strftime(3) and the result is inserted into the prompt string; an empty format results in a locale-specific time representation. The braces are required
-   **\\e** : an ASCII escape character (033)
-   **\\h** : the hostname up to the first '.'
-   **\\H** : the hostname
-   **\\j** : the number of jobs currently managed by the shell
-   **\\l** : the basename of the shellâ€™s terminal device name
-   **\\n** : newline
-   **\\r** : carriage return
-   **\\s** : the name of the shell, the basename of $0 (the portion following the final slash)
-   **\\t** : the current time in 24-hour HH:MM:SS format
-   **\\T** : the current time in 12-hour HH:MM:SS format
-   **\\@** : the current time in 12-hour am/pm format
-   **\\A** : the current time in 24-hour HH:MM format
-   **\\u** : the username of the current user
-   **\\v** : the version of bash (e.g., 2.00)
-   **\\V** : the release of bash, version + patch level (e.g., 2.00.0)
-   **\\w** : the current working directory, with $HOME abbreviated with a tilde
-   **\\W** : the basename of the current working directory, with $HOME abbreviated with a tilde
-   **\\!** : the history number of this command
-   **\\#** : the command number of this command
-   **\\$** : if the effective UID is 0, a #, otherwise a $
-   **\\nnn** : the character corresponding to the octal number nnn
-   **\\\\** : a backslash
-   **\\\[** : begin a sequence of non-printing characters, which could be used to embed a terminal control sequence into the prompt
-   **\\\]** : end a sequence of non-printing characters

Use the [unset command](https://bash.cyberciti.biz/guide/Unset "Unset command in Linux - Linux Bash Shell Scripting Tutorial Wiki") to [remove or unset those bash variables](https://www.cyberciti.biz/faq/linux-osx-bsd-unix-bash-undefine-environment-variable/) to bash defaults. For example:  
`$ unset PS0`

### Examples

Let us try to set the prompt so that it can display today's date and hostname:  
`PS1="\d \h $ "`  
Sample output:

```
Sat Jun 02 server $ 
```

Now setup prompt to display date/time, hostname and the current working directory:  
`PS1="[\d \t \u@\h:\w ] $ "`  
Sample utputs:

```
[Sat Jun 02 14:24:12 vivek@server:~ ] $
```

### How do I add colors to my prompt?

You can change the [color of your shell prompt](https://www.cyberciti.biz/faq/bash-shell-change-the-color-of-my-shell-prompt-under-linux-or-unix/) to impress your friend or to make your own life quite easy while working at command prompt.

### Putting it all together

Let us say when you login as root/superuser, you want to get visual confirmation using red color prompt. To distinguish between superuser and normal user you use last character in the prompt, if it changes from $ to #, you have superuser privileges. So let us set your prompt color to RED when you login as root, otherwise display normal prompt.

Open /etc/bashrc (Redhat and friends) / or /etc/bash.bashrc (Debian/Ubuntu) or /etc/bash.bashrc.local (Suse and others) file and append following code:  
`# vi /etc/bashrc`  
or  
`$ sudo gedit /etc/bashrc`  
Append the code as follows

```
<span># If id command returns zero, you got root access.</span>
<span>if</span> <span>[</span> $<span>(</span><span>id</span> -u<span>)</span> <span>-eq</span> <span>0</span> <span>]</span>;
<span>then</span> <span># you are root, set red colour prompt</span>
  <span>PS1</span>=<span>"\\[<span>$(tput setaf 1)</span>\\]\\u@\\h:\\w #\\[<span>$(tput sgr0)</span>\\]"</span>
<span>else</span> <span># normal</span>
  <span>PS1</span>=<span>"[\\u@\\h:\\w] $"</span>
<span>fi</span>
```

Close and save the file.

### My firepower prompt

Check this out:  
[![How to  Change / Set up bash custom prompt (PS1) in Linux - Firepower shell prompt using bashish](https://www.cyberciti.biz/media/new/tips/2007/06/firepower-prompt.jpg)](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/firepower-prompt.jpg)  
(click to enlarge)

You can also create complex themes for your bash shell using bashish. [Bashish](http://bashish.sourceforge.net/) is a theme enviroment for text terminals. It can change colors, font, transparency and background image on a per-application basis. Additionally Bashish supports prompt changing on common shells such as bash, zsh and tcsh. Install bashish using rpm or apt-get command:  
`# rpm -ivh bashish*`  
OR  
`# dpkg -i bashish*`  
Now start bashish for installing user configuration files:  
`$ bashish`  
Next you must restart your shell by typing the following command:  
`$ exec bash`  
To configure the Bashish theme engine, run  
`$ bashishtheme`

basish in action (screenshots from official site):  
[![How to  Change / Set up bash custom prompt (PS1) in Linux - flower.png](https://www.cyberciti.biz/media/new/tips/2007/06/flower.thumbnail.png)](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/flower.png "flower.png")

[![urbandawn - based on an artwork by grevenlx](https://www.cyberciti.biz/media/new/tips/2007/06/urbandawn.thumbnail.png)](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/urbandawn.png "urbandawn - based on an artwork by grevenlx")  
Finally, you can always use [aterm](http://www.afterstep.org/aterm.php) or other terminal program such as rxvt. It supports nice visual effect , like transparency, tinting and much more by visiting profile menu. Select your terminal > click on Edit menu bar > Profiles > Select Profile > Click on Edit button > Select Effects tab > Select transparent background > Close

[![Linux desktop nice visual effect , like transparency, tinting etc](https://www.cyberciti.biz/media/new/tips/2007/06/mydesktop-thumb-desltop-transparency.png)](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/mydesktop.png)  
(click to enlarge)

## How to celebrate the holidays at your bash prompt

Most modern Linux terminal supports Unicode. So you can add the following emojis:

| Emoji | Description |
| --- | --- |
| 🎄 | Christmas Tree |
| 🦌 | Deer |
| 🤶 | Mrs. Claus |
| 🎅 | Santa Claus |
| 🧣 | Scarf |
| ❄ | Snowflake |
| ☃ | Snowman |
| 🎁 | Wrapped Gift |

Let us add colors and Christmas tree/Santa to our PS1:  
`$ export PS1="🎅\[\e[33;41m\][\[\e[m\]\[\e[32m\]\u\[\e[m\]\[\e[36m\]@\[\e[m\]\[\e[34m\]\h\[\e[m\]\[\e[33;41m\]]\[\e[m\]🎄 "`  
[![How to celebrate the holidays at your bash prompt](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/How-to-celebrate-the-holidays-at-your-bash-prompt.png)](https://www.cyberciti.biz/tips/wp-content/uploads/2007/06/How-to-celebrate-the-holidays-at-your-bash-prompt.png)

## Conclusion

And that is how to change or set up bash custom prompt ([PS1](https://bash.cyberciti.biz/guide/PS1)) in Linux. See the following urls for more info:

-   [Coloring your prompt with tput and shell escape code](https://www.cyberciti.biz/faq/bash-shell-change-the-color-of-my-shell-prompt-under-linux-or-unix/)
-   [How-to reference guide to customizing the BASH command-line prompt.](http://tldp.org/HOWTO/Bash-Prompt-HOWTO/)
-   Download [Bashish](http://bashish.sourceforge.net/) theme environment for text terminals.

See the following man pages using the [man command](https://bash.cyberciti.biz/guide/Man_command "man command in Linux with Examples - Linux Shell Scripting Tutorial") or [help command](https://bash.cyberciti.biz/guide/Help_command "Help command - Linux Bash Shell Scripting Tutorial Wiki"):  
`$ man bash`

### Did you notice? 🧐

nixCraft is ad-free to protect your privacy and security. We rely on reader support to keep the site running. Please consider subscribing to us on Patreon or supporting us with a one-time support through PayPal or purchase official merchandise. Your support will help us cover the costs of hosting, CDN, DNS, and tutorial creation.
