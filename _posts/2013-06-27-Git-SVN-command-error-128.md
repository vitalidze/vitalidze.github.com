---
layout: post
tags : [git, git-svn, subversion]
---
{% include JB/setup %}

On my current work we're using SVN as the code repository for our main project and all sub projects. About a month ago I started working with this repo via git-svn. So I have my local git repository, commit there changes and then push them to the SVN repository. I will not describe how to do that, maybe as a separate post. 

My openSUSE comes with pretty old 1.7.3.4 version of git. However, it was good until today. I got following error message after trying to fetch changes:

    > git svn fetch --fetch-all 
      Index mismatch: 1bac9c8667e2b3772d71108562404c95c8f7d9b0 != 97bb5a8fb29d0cb3a9b38456bf73d3c126d7c6fd
      rereading 0bf8f076838b6078e7a8bf68b0f2a234f509cf46
        M	src/com/company/project/package/UpdatedClass.java
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and acd2afc03bb5b2dc10126905cfd0cb2774aa4b2b. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and 99a066db650463dd8c3c926129bf462edeb2ed14. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and 5aa2ecdf0f8fd56480f5d71e67c2d190d1ebcbc0. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and dd5cd8aaf1d01ce8de212c29faa10891e8b45578. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and 2537435f7970943e4cb8f15a1864c641bd09a231. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and bb13cc7c762e2589966a459c8f7a6a332d7939cb. Ignoring merge info.
      W: Cannot find common ancestor between 0bf8f076838b6078e7a8bf68b0f2a234f509cf46 and 0ca1856db48e52c300f6a0a04beb54e9f5edc747. Ignoring merge info.
      fatal: ambiguous argument '618f50e5893d13f274652b0dd87b29b17cd32072^..c53f7450af7b937ca9e6d9659d9c7e7467b780ca': unknown revision or path not in the working tree.
      Use '--' to separate paths from revisions
      rev-list 618f50e5893d13f274652b0dd87b29b17cd32072^..c53f7450af7b937ca9e6d9659d9c7e7467b780ca: command returned error: 128
    
The only possible solution I've found is to remove git-svn caches:

    rm .git/svn/.caches/check_cherry_pick.db
    rm .git/svn/.caches/lookup_svn_merge.db
    
Then do the same fetch. However, it looks like my version of git is so old that even this doesn't help.

To solve this I managed to update git to the latest version by cloning official repo at git clone https://github.com/git/git.git . Then I've uninstalled currentl git package, compiled sources, cleared SVN cache again and voila! Everything works again.

I've thought that maybe someone would find this information useful.
