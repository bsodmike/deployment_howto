### Part 1: Deploying Rack Apps to Production - Ubuntu LTS, RVM, and Ruby

**This beginner's guide is based on the fantastic post titled '[Ubuntu, Ruby, RVM, Rails, and You](http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/)' by [Ryan Bigg](http://ryanbigg.com/) ([@ryanbigg](http://twitter.com/#!/ryanbigg)).**

**At this time, I would also like to thank [Wayne E. Seguin](https://github.com/wayneeseguin) ([@wayneeseguin](http://twitter.com/#!/wayneeseguin)) for his fantastic work on RVM and [Deryl (@deryldoucette)](https://twitter.com/#!/deryldoucette) for his advice!**

#### Ubuntu 'Lucid' 10.04 LTS Housekeeping

First, run `sudo apt-get update` to fetch the latest sources followed by `sudo apt-get upgrade` thereby upgrading your install of Ubuntu.

You are *strongly advised* to edit `/etc/ssh/sshd_config` and [secure your server](http://library.linode.com/securing-your-server#sph_disabling-ssh-password-authentication-and-root-login), especially its OpenSSH daemon's configuration.  Details on setting up public and private SSH keys are [detailed here](https://help.ubuntu.com/community/SSH/OpenSSH/Keys).

Next, let's install [Git, a distributed version control system (DVCS)](http://git-scm.org/), `curl`, as well as the `build-essential` package &mdash; these are prerequisites for installing RVM, amongst others.  Proceed with the following command

```bash
$ sudo apt-get install build-essential git-core curl
```

#### Install RVM

Let's perform a system wide install of RVM

```bash
$ curl -L get.rvm.io | sudo bash -s stable
```

You should be left with the following details and instructions post-install.

```bash
Installing RVM to /usr/local/rvm/
    Creating RVM system user group 'rvm'

# RVM:  Shell scripts enabling management of multiple ruby environments.
# RTFM: https://rvm.io/
# HELP: http://webchat.freenode.net/?channels=rvm (#rvm on irc.freenode.net)
# Cheatsheet: http://cheat.errtheblog.com/s/rvm/
# Screencast: http://screencasts.org/episodes/how-to-use-rvm

# In case of any issues read output of 'rvm requirements' and/or 'rvm notes'

Installation of RVM in /usr/local/rvm/ is almost complete:

  * First you need to add all users that will be using rvm to 'rvm' group,
    and logout - login again, anyone using rvm will be operating with `umask g+w`.

  * To start using RVM you need to run `source /etc/profile.d/rvm.sh`
    in all your open shell windows, in rare cases you need to reopen all shell windows.

# mike,
#
#   Thank you for using RVM!
#   I sincerely hope that RVM helps to make your life easier and more enjoyable!!!
#
# ~Wayne


rvm 1.12.3 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.c
om> [https://rvm.io/]
```

As per the above let's add the current user to the `rvm` group

```bash
$ usermod -a -G rvm mike
```
Ensure you logout and back in to your account for changes to take effect.  This will always automatically perform `source /etc/profile.d/rvm.sh` thereby loading `rvm` into your current session.

We need to add the following line to `.bashrc` to ensure RVM will be setup in our bash session during future logins 

```bash
$ echo '[[ -s "/usr/local/rvm/scripts/rvm" ]] && source "/usr/local/rvm/scripts/rvm"' >> ~/.bashrc
```

You can either logout and back, or for convenience sake simply reload the file via `source .bashrc`, thereby ensuring the above changes take effect.  

Before we set about installing Ruby, let's install some packages to ensure our Ruby lives are as pain free as possible &mdash; you can get the following list by performing `rvm requirements`.

```bash
$ sudo apt-get install build-essential openssl libreadline6 libreadline6-dev curl
git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev
sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool
bison subversion
```

You are also urged to look at `rvm notes` as well for OS specific notes.  Great! We are now ready to install Ruby via RVM.

#### Install Ruby

Proceed to install Ruby 1.9.3, or any other version as you require, via `rvm install 1.9.3`.  Notice that I have not specified a particular patch-level and therefore RVM will fetch the latest patch level for ruby 1.9.3.

Let's confirm the install went well

```bash
$ rvm list

rvm rubies

   ruby-1.9.3-p125 [ x86_64 ]

# Default ruby not set. Try 'rvm alias create default <ruby>'.

# => - current
# =* - current && default
#  * - default
```

As seen above, `ruby-1.9.3-p125` has been compiled as is available for use via `rvm`.  Let's make this our _default_ version of Ruby

```bash
$ rvm use 1.9.3 --default && rvm list
Using /usr/local/rvm/gems/ruby-1.9.3-p125
rvm rubies

=* ruby-1.9.3-p125 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
```

Performing `ruby -v` also confirms this with its response of `ruby 1.9.3p125 (2012-02-16 revision 34643) [x86_64-darwin11.3.0]`

Fantastic! We've configured and successfully setup `ruby-1.9.3-p125` as the default Ruby via RVM.