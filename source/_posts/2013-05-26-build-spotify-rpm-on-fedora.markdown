---
layout: post
title: "Build Spotify RPM on Fedora"
date: 2013-05-26 10:32
comments: true
categories: 
---

[Fedora people][1] provide everything to build a RPM package of the latest Spotify client.

[1]: http://leamas.fedorapeople.org/spotify/

	sudo yum install rpm-build rpmdevtools yum-utils
	rpmdev-setuptree
	cd $HOME/rpmbuild/SPECS
	wget http://leamas.fedorapeople.org/spotify/0.9.0/spotify-client.spec
	cd $HOME/rpmbuild/SOURCES
	spectool -g ../SPECS/spotify-client.spec
	cd -
	sudo yum-builddep spotify-client.spec
	QA_RPATHS=$[2|8] rpmbuild -bb spotify-client.spec
	rpm -Uvh $HOME/rpmbuild/RPMS/x86_64/spotify-client-0.9.0.133.gd18ed58.259-2.fc18.x86_64.rpm
