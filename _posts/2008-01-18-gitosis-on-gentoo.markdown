--- 
permalink: /posts/gitosis-on-gentoo.html
title: Gitosis on Gentoo
tags: 
- gentoo
- git
- gitosis
layout: post
---
In my [last post](/posts/git-links-for-2007-01-05), I was making [Git](http://git.or.cz/) out to be kind of a big deal. One of the links talked about setting up [gitosis](http://scie.nti.st/2007/11/14/hosting-git-repositories-the-easy-and-secure-way) for managing repositories. It's an excellent walkthrough, but it's pretty generic. This post hopes to provide a Gentooified version of it.

## The scenario

I'm making a few assumptions:

 * You have a workstation with a normal user, with hostname 'urworkstation' and user 'you'.
 * You've installed dev-util/git on urworkstation.
 * You have a server with root access, with hostname 'urserver'.
 * You will be making a repository called 'gitftw'

## Installing gitosis

[Gitosis](http://scie.nti.st/2007/11/14/hosting-git-repositories-the-easy-and-secure-way) is a fairly recent addition to portage. If you're not using ~arch for your ACCEPT\_KEYWORDS, you will need to add an entry to /etc/portage/package.keywords.

    root@urserver # echo "dev-util/gitosis" >> /etc/portage/package.keywords

Now you should be able to emerge it.

    root@urserver # emerge -atv dev-util/gitosis

_Note: -t shows a dependency tree of what will be merged, -v includes a little more verbose output, and -a shows you what will be merged, and asks you to continue._

This should (hopefully!) merge without any issues. It installs all the gitosis files you need, as well as create a 'git' user and group, and a place for your repositories to live in at /var/spool/gitosis/repositories.

You now are ready to start configuring gitosis.

## Configuring

You'll need to generate an ssh key, if you don't already have one. I also recommend using [keychain](http://www.gentoo.org/proj/en/keychain/) for managing your keys/ssh-agents.

    you@urworkstation $ ssh-keygen -t rsa

You will be prompted for a location to save the key, but I will assume you choose `~/.ssh/id_rsa`. This will create `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`. Now you want to toss `id_rsa.pub` up on urserver using scp.

    you@urworkstation $ scp ~/.ssh/id_rsa.pub root@urserver:
    
After you get `id_rsa.pub` there, the following command switches to the git user, and kicks off gitosis-init using your ssh public key.
 
    root@urserver #  sudo -H -u git gitosis-init < id_rsa.pub

_Note: -H makes sudo switch the user's home directory. -u git says to switch to the git user._

This creates the gitosis-admin repository which you will use to further setup gitosis.

Now you want to clone it:

    you@urworkstation $  git clone git@urserver:gitosis-admin.git
    
## Creating new repositories and adding users

The creating of repositories and adding of users isn't really Gentoo specific, so I refer you to the "Creating new repositories" and "Adding users" sections of the [gitosis article](http://scie.nti.st/2007/11/14/hosting-git-repositories-the-easy-and-secure-way).

I assume you'll create a repository called 'gitftw', and give yourself access to it.
 
## Making publically accessible using git-daemon

At this point, you have your 'gitftw' repository, and you're able to clone it by doing:

    you@workstation $ git clone git@urserver:gitftw.git
    
This is all well and good if you want only users you've setup to be able to access the repository. But what about if you want to provide anonymous access to the repository? One answer is to use git-daemon which comes as part of git.

The Gentoo package for dev-util/git provides an init script for you, so you just need to tweak the accompanying conf file, and add it to the default runlevel.

There are two ways you can choose to export your repositories. The first is to individually choose which repositories to export, or choose to export everything by default. I think the latter isn't the best idea though, since this includes gitosis-admin repository.

### Selectively export

Open up /etc/conf.d/git-daemon, and update it to have:

    GITDAEMON_OPTS="--syslog --base-path=/var/spool/gitosis/repositories/"

Now we touch a file 'git-daemon-export-ok' in each repository we want exported.

    root@urserver # touch /var/spool/gitosis/repositories/gitftw.git/git-daemon-export-ok


### Export everything

Open up /etc/conf.d/git-daemon, and update it to have:

    GITDAEMON_OPTS="--syslog --base-path=/var/spool/gitosis/repositories/ --export-all"
    
### Starting it

You're all set to actually start it up now.

    root@urserver # /etc/init.d/git-daemon start
    
Now we try checking it out using the git protocol:

    you@urworkstation $ git clone git://urserver:gitftw.git
   
Assuming all is well, now we can add it to the default runlevel.

    root@urserver # rc-update add git-daemon default
    
## Conclusion

Congratulations, you are now all set to manage and play with git on urserver until the cows come home.

## Update Jan 22, 2008

Robin Johnson aka robbat2, who maintains the ebuilds for gitosis, posted [some important notes about our gitosis package](http://robbat2.livejournal.com/217665.html). Mostly, there are some differences between our package and the upstream version of gitosis.

