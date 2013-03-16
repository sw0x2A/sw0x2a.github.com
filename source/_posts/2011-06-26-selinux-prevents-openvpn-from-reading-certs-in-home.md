---
title: SELinux prevents OpenVPN from reading certs in home
author: sw
layout: post
permalink: /2011/06/selinux-prevents-openvpn-from-reading-certs-in-home/
categories:
  - Uncategorized
tags:
  - fedora
  - linux
  - networkmanager
  - openvpn
  - security
  - selinux
---
# 

I saved my OpenVPN certificates in `~/.openvpn`, imported the configuration in NetworkManager and expected it to working properly. But in Fedora SELinux prevents OpenVPN from reading the certificates and private keys from that directory. The policy expects the files in directories labeled as `home_cert_t`, which are by default `~/.cert/` or `~/.pki/`. Ok, I copied the files accordingly and executed `restorecon` to make it work:

    restorecon -R -v ~/.pki ~/.cert
    

Another way would be to label `~/.openvpn` as `home_cert_t`:

    semanage fcontext -a -t home_cert_t "/home/user/.openvpn(/.*)?'
    restorecon -R -v /home/user/.openvpn