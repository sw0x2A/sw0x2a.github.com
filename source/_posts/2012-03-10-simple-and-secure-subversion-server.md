---
title: Simple and secure Subversion server
author: sw
layout: post
permalink: /2012/03/simple-and-secure-subversion-server/
categories:
  - Uncategorized
tags:
  - ssh
  - subversion
  - svn
---
# 

In most environments, people want to access their Subversion repositories over a HTTPS connection. This requires a webserver like Apache, which provides WebDAV and SVN modules. I just switched over to nginx and do not want to install Apache just for SVN only. So I started to search for an alternative method which should be easy and secure.

I found `svn ssh`. I knew SVN over SSH already but I remembered that there were some problems with file permissions and I did not really liked the idea of having Unix accounts for each SVN user on the system. What I realized then was that there is a pretty nice workaround perfect solution.

First of all, let us create a new SVN user. Only this user will access the repositories.

    adduser --home /var/svn --shell /bin/bash --disabled-password svn
    

Next step is to create a new repository and import the basic directory structure.

    for dir in trunk branches tags; do mkdir -p /tmp/project1/$dir; done
    mkdir /var/svn/repos
    svnadmin create /var/svn/repos/project1
    svn import /tmp/project1 file:///var/svn/repos/project1 -m "Initial import"
    

All the magic is done in the SVN user’s SSH `authorized_keys` file.

    command="/usr/bin/svnserve -t -r /var/svn/repos --tunnel-user=user1",no-port-forwarding,no-pty,no-agent-forwarding,no-X11-forwarding ssh-rsa AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA== user1@example.com
    command="/usr/bin/svnserve -t -r /var/svn/repos --tunnel-user=user2",no-port-forwarding,no-pty,no-agent-forwarding,no-X11-forwarding ssh-rsa AAAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB== user2@example.com
    

You can limit user access per SSH public key. In this case we force the user to execute `svnserve`, we can limit to repository and we can set a username with `--tunnel-user` which is used for commits. Additionally, we can disallow all non-SVN stuff to prevent login to shell or port-forwarding etc.

That’s it. Check out your repository and start working with it.

    svn co svn ssh://svn@example.com/project1/trunk project1