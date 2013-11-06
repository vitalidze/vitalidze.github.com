---
layout: post
tags : [git, bash]
---
{% include JB/setup %}

I have moved recently to Mac OS X (10.8.2) and I am still using git-svn to work with our corporate repository with git. First of all I wanted to add git bash completion:

     curl https://raw.github.com/git/git/master/contrib/completion/git-completion.bash -o ~/.git-completion.bash
     echo "source ~/.git-completion.bash" >> ~/.bash_profile"

Then I wanted to beautify command prompt and put this line to `~/.profile`:

     export PS1="\[\033[36m\]\u\[\033[m\]@\[\033[32m\]\h:\[\033[33;1m\]\w\[\033[m\] \$ "
     
The next step was to add git branch name to the command prompt.

     curl https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh -o ~/.git-prompt.bash

Then put following line to the beginning of ~/.profile

     source ~/.git-prompt.bash
     
Next we need to modify the PS1 variable.

     export PS1="\[\033[36m\]\u\[\033[m\]@\[\033[32m\]\h:\[\033[33;1m\]\w\[\033[m\]\e[0;32m$(__git_ps1 " (%s)")\e[m \$ "
     
This will work, but only once! It evaluates expression for only one time. After googling around I have found that one of possible solutions is to escapte dollar sign ('$') at the beginning of `__git_ps1` invocation. However, this doesn't solve problem and breaks PS1 at all. The solution is to use single quote characters to define string for `PS1` variable. This tells bash to evaluate expression each time. 

The working example:

     export PS1='\[\033[36m\]\u\[\033[m\]@\[\033[32m\]\h:\[\033[33;1m\]\w\[\033[m\]\e[0;32m$(__git_ps1 " (%s)")\e[m \$ '

I haven't found it in google, hope someone finds it helpful.
