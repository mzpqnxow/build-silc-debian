# build-silc-debian
Files and instructions for building a silc client and server on a modern Debian system

## The silc project is dead

The silc project is dead and has been for quite some time, but some people prefer to use it. I am one of those people, and it has been a pain to build it. In fact, it is a pain just to find the tarballs to build. This is a guide and it also contains the tarballs to use to build. This guide is intended for a single server loopback only silc configuration- a "private" chat

## Install Debian prerequisites

```
sudo apt-get install libncurses5-dev libglib2.0-dev build-essential
```

## Start in some dedicated directory

```
mkdir ~/build-silc
```


## Download the last maintained versions of the client and server

Download, build and install silc client and server:

You can use the tarballs in this repository or wget them from some archive:

```
wget http://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/network/silc/server/sources/silc-server-1.1.18.tar.gz
wget http://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/network/silc/client/sources/silc-client-1.1.8.tar.gz
```

## Build and install the server

```
$ tar -xvzf silc-server-1.1.18.tar.gz
$ cd silc-server-1.1.18
$ ./configure --prefix=/opt/silc-1.1.18 --disable-ipv6 \
$  CFLAGS='-fPIC -fPIE -D_FORTIFY_SOURCE=2 -O1 -Wformat -Wformat-security -pie -fPIE -fstack-protector' \
$  LDFLAGS='-z relro -z now'
$ make -j
$ sudo make install
```

## Build and install the client

```
$ cd ~/build-silc
$ tar -xvzf http://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/network/silc/client/sources/silc-client-1.1.8.tar.gz
$ cd silc-client-1.1.8
$ ./configure --prefix=/opt/silc-1.1.8 --disable-ipv6 \
  CFLAGS='-fPIC -fPIE -D_FORTIFY_SOURCE=2 -O1 -Wformat -Wformat-security -pie -fPIE -fstack-protector' \
  LDFLAGS='-z relro -z now'
$ make -j
$ sudo make install
```

## Configure the silc client

```
$ cd
$ mkdir -p .silc/
$ cp silc-client-conf/silc.conf ~/.silc
$ echo 'export PATH=$PATH:/opt/silc-1.1.8/bin' >> ~/.bashrc
$ source ~/.bashrc
```

Make edits to ~/.silc/silc.conf where there are 'XXXXXX'

You can also feel free to change the encryption settings, but this is meant for a loopback only configuration

## Configure the silc server

You'll find a default configuration in /opt/silc-1.1.18/etc/silcd.conf

You can modify it, if you want to have your own private silc server on loopback you can use the file in silc-server-conf/ after replacing the values that have XXXX with your own

You can also feel free to change the encryption settings, but this is meant for a loopback only configuration

## Making an init script

A very basic distribution/OS agnostic init script is in init.d

## Running for the first time

You might encounter some errors, check the logs after running silcd for the first time via:

```
$ sudo /opt/silc-1.1.18/sbin/silcd -f /opt/silc-1.1.18/etc/silcd.conf
```

Try these commands if silcd fails to start:

```
$ sudo mkdir /opt/silc-1.1.18/var/
$ sudo chown nobody:nogroup /opt/silc-1.1.18/var/
$ sudo chmod 600 /opt/silc-1.1.18/etc/silcd.prv
```

## Run the client

$ silc

You should be all set! Note it will ask you for a passphrase. This is for your client's private key, not for the server!
