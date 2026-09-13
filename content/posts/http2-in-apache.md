+++
date = '2023-04-09T22:10:12-05:00'
draft = false 
title = 'Enabling HTTP2 in Apache'
summary = "I've had to manually enable http2 in Apache. It has always been a bit of a headache."
tags = ['linux','apache','http2']
+++

A few times, I've had to manually enable http2 in Apache. It has always been a
headache for me, so I want to make short tutorial how to set it up properly.
This tutorial will already assume that you have a functional website that is
currently using http/1.1. I am also assuming that your server is running a
Debian based distribution. These instructions will work for other
distributions, but you may have to adjust slightly.

First, if you are running the ufw firewall, you want to open the OpenSSH port.

    # ufw allow OpenSSH

Next, you will want to check what multi-processing module (mpm) Apache is
using. This is very important because http2 does not work with mpm_prefork. You
must use mpm_event or mpm_worker. We will go with mpm_event for this tutorial,
but choose what works best for your system. You can check to see what apache is
using by entering the following command:

    $ apache2 -V | grep -i mpm

To disable prefork and enable worker, enter the following commands.

    # a2dismod mpm_prefork
    # a2enmod mpm_event
    # systemctl restart apache2

If you are using php, you will have to also update what version of php you are
using. You will want to install php-fpm. I will be demonstrating with php
version 7.4. Change the version number to match yours. 

    # apt update && apt install php7.4-fpm

You will need to update your server to enable php-fpm.

    # a2enconf php7.4-fpm
    # a2enmod proxy_fcgi
    # systemctl restart apache2

In order to enable http2 on your website, you will need to open your .htaccess
file or server configuration file and add the following line:

    Protocols h2 h2c http/1.1

Finally, we will enable http2.

    # a2enmod http2
    # systemctl restart apache2

If everything ran without error, you should be all set to go. In order to check
to see if your server is using http2, you can run the following command,

    $ curl -I --http2 <https://your-domain.tdl>
