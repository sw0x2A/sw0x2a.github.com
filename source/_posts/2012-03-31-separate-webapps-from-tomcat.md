---
title: Separate webapps from Tomcat
author: sw
layout: post
permalink: /2012/03/separate-webapps-from-tomcat/
categories:
  - Uncategorized
tags:
  - java
  - jenkins
  - tomcat
  - webapps
---
# 

Until last week I thought it would be necessary to configure Tomcat and its webapps as a big monolithic system. What a great mistake! I have learned that there is a much easier way to run webapps in Tomcat. Actually each webapps becomes its own application server, executed with a dedicated user. I will demonstrate this way by showing how to install Jenkins. 

First install [Java JDK][1] and [Tomcat][2] into /opt. In this case, I have just unpacked the tarballs there and created symlinks to have an easy way to upgrade to newer versions.

 [1]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
 [2]: http://tomcat.apache.org/

    root@debian:/opt# ls -l
    total 8
    drwxr-xr-x 9 root root [...] apache-tomcat-6.0.35
    lrwxrwxrwx 1 root root [...] java7 -> jdk1.7.0_03
    drwxr-xr-x 8 root root [...] jdk1.7.0_03
    lrwxrwxrwx 1 root root [...] tomcat6 -> apache-tomcat-6.0.35
    

Add a new user for Jenkins and create the base skeleton of Tomcat directories in its homedir /home/jenkins. Copy the conf directory from your Tomcat installation. The other directories are empty. Download the [Jenkins WAR file][3] and save it in the webapps directory.

 [3]: http://mirrors.jenkins-ci.org/war/latest/jenkins.war

    root@debian:/home/jenkins# ls -l
    total 28
    drwxr-xr-x 2 jenkins nogroup [...] conf
    drwxr-xr-x 6 jenkins nogroup [...] data
    drwxr-xr-x 2 jenkins nogroup [...] logs
    drwxr-xr-x 3 jenkins nogroup [...] temp
    drwxr-xr-x 3 jenkins nogroup [...] webapps
    drwxr-xr-x 3 jenkins nogroup [...] work
    

Create /home/jenkins/.profile to define the application server environment variables.

    # Applicationserver enviroment
    export JENKINS_HOME=/home/jenkins/data
    export JENKINS_BASEDIR=${HOME}
    export CATALINA_HOME=/opt/tomcat6
    export CATALINA_BASE=${HOME}
    export JAVA_HOME=/opt/java7
    export CATALINA_PID=${JENKINS_BASEDIR}/jenkins.pid
    export CATALINA_OPTS="-Xms2048m -Xmx2048m -Djava.awt.headless=true"
    export PATH=${CATALINA_HOME}/bin:${JAVA_HOME}/bin:${PATH}
    

Last step is to put an init script for Jenkins into /etc/init.d/jenkins.

    #!/bin/bash
    #
    # Startup / shutdown script for the jenkins server.
    #
    ### BEGIN INIT INFO
    # Provides:             jenkins
    # Required-Start:       $network $local_fs
    # Required-Stop:
    # Should-Start:         $named
    # Should-Stop:
    # Default-Start:        2 3 4 5
    # Default-Stop:         0 1 6
    # Short-Description:    Jenkins
    # Description:          Jenkins Continuous Integration server
    ### END INIT INFO
    
    SCRIPT_USER=jenkins
    LOCKFILE=/var/lock/jenkins
    export TOMCAT_HOME=/opt/tomcat6
    [ -f $TOMCAT_HOME/bin/catalina.sh ] || exit 0
    case "$1" in
      start)
            # Start daemon.
            echo -n "Starting Jenkins: "
            su -l $SCRIPT_USER -c "$TOMCAT_HOME/bin/catalina.sh start"
            RETVAL=$?
            echo
            [ $RETVAL = 0 ] && touch $LOCKFILE
            ;;
      stop)
            # Stop daemons.
            echo -n "Shutting down Jenkins: "
            su -l $SCRIPT_USER -c "$TOMCAT_HOME/bin/catalina.sh stop"
            RETVAL=$?
            echo
            [ $RETVAL = 0 ] && rm -f $LOCKFILE
            ;;
      restart)
            $0 stop
            $0 start
            ;;
      condrestart)
           [ -e $LOCKFILE ] && $0 restart
           ;;
      status)
            status jenkins
            ;;
      *)
            echo "Usage: $0 {start|stop|restart|status}"
            exit 1
    esac
    exit 0
    

Now you can start Jenkins as a seperate service. Repeat the steps from above for each webapp that you need.

This setup makes it possible to start or stop each webapp independent from the others. It also allows you to run webapps with different Java or Tomcat versions or easily change the underlying Java and Tomcat versions (just refer to them in .profile).