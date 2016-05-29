Dev Env Setup on Mac OS X
=========================

* Apache HTTP Server & PHP

First off, we need to have a running web server to serve contents. Out of the box the machine already has php and httpd installed. We just need to enable PHP for the web server. So we need to uncomment the following line in /etc/apache2/httpd.conf

```
#LoadModule php5_module libexec/apache2/libphp5.so
```

Then you can start the server.

```
sudo apachectl restart
```

* Git

Then download and install Git. I also set up my user name and email.

```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Then I generate a ssh key

```
ssh-keygen -t rsa -C "your_email@youremail.com"
```

And add the public key in /Users/you/.ssh/id_rsa.pub to my github account.

* Macports

I then installed MacPorts and install the following

```
sudo port install pcre
sudo port install automake
sudo port install autoconf
sudo port install libtool
sudo port install pkgconfig
```

* X code Command Line Tools

You also need X code and its command line tools.

* Apache Traffic Server

Finally I simply go to my forked version of ATS and clone it. I also setup my upstream

```
git clone https://github.com/shukitchan/trafficserver.git
git remote add upstream https://github.com/apache/trafficserver.git
```

To build and install

```
autoreconf -f -i
./configure --enable-experimental-plugins=yes
make
sudo make install
```

Then edit /usr/local/etc/trafficserver/remap.config to define how ATS will do the proxying and restart it.

```
sudo /usr/local/bin/trafficserver restart
```
