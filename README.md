Image Scan! with 16 bits scanning capability for Ubuntu 20.04 and 22.04
=======================================================================

The ImageScan! backend for Linux does not support 16 bits scanning.
Henrik Andersson fixed that a while ago in his `iscan` fork:

https://github.com/hean01/iscan

However, the Linux environment evolved since then and it does
not compile on my current Ubuntu 20.04 environment.

Thanks to

- https://bbs.archlinux.org/viewtopic.php?id=244017
- http://www.slackware.com/~alien/slackbuilds/iscan/build/

I was able to fix a few of the issues. I used the debugger to fix
the other ones I found.

As the title says, these instructions are for Ubuntu, more precisely
Ubuntu 20.04 and 22.04. They may work as is or with modifications in other
environments.

Please also read the original [README](README) file from Epson.

Installing
==========

The installation method that worked for me was to install this package
over the Epson drivers. So first, go on the
[Epson website](https://download.ebz.epson.net/dsc/search/01/search/searchModule)
and download the drivers for your scanner. This fork is based on
iscan 2.30.4-2 so you would need to install iscan 2.30.4-2 from Epson.

Test your installation. Make sure you are able to scan an 8 bit image
with `iscan` or `xsane`. Check that the software displays the scan
options that you would expect from your scanner (transparency unit, etc.)
If the expected options are not there, your scanner was not recognized.
In this case, seek help on the Internet.
[This page](https://nzeid.net/linux-sane-epson-perfection-v600-photo-scanner-setup)
may help you for the V600.

Once you can scan with the Epson drivers, you need to compile this package
and install it over Epson `iscan`.

Compiling
=========

Make a directory called `iscan-16` and `cd` into it. Then clone this repository:

```
mkdir iscan-16
cd iscan-16
git clone https://github.com/patrickfournier/iscan.git
```

Then compile the package:

```
cd iscan
git checkout master
ln -s libesmod-x86_64.c2.so non-free/libesmod-x86_64.so
dpkg-buildpackage -rfakeroot --no-sign
```

If there are any missing dependency reported by `dpkg-buildpackage`, install them
and run `dpkg-buildpackage` again.

Installing
==========

At this point, you should have a file named `iscan_2.30.4-2_amd64.deb` in the `iscan-16`
directory. Simply install it over the existing `iscan` package (do not remove Epson
package before installing this one, as it may contain a plugin specific to your scanner):

`sudo dpkg --install iscan_2.30.4-2_amd64.deb`

Extra notes
===========

These are notes for myself.

IScan sources
-------------

http://support.epson.net/linux/src/scanner/iscan/

Dependencies
------------

cdbs
debhelper (>= 9)
libsane-dev
libltdl7-dev | libltdl3-dev
libgtk2.0-dev
libpng-dev
libjpeg-dev
libgimp2.0-dev
libtiff5-dev
libxml2-dev
libusb-1.0-0-dev

Compiling manually
-----------------

```
make distclean

LDFLAGS="-ldl -lpng" \
./configure \
  --prefix=/usr \
  --sbindir=/usr/bin \
  --libdir=/usr/lib${LIBDIRSUFFIX} \
  --mandir=/usr/man \
  --docdir=/usr/doc/$PRGNAM-$VERSION \
  --localstatedir=/var \
  --sysconfdir=/etc \
  --enable-dependency-reduction \
  --enable-frontend \
  --enable-jpeg \
  --enable-tiff \
  --enable-png \
  --enable-gimp \
  --program-prefix= \
  --program-suffix=

make
```

Manual install
--------------

- Install epsonscan2
- Install iscan
- `make DESTDIR=<some-dir-abs-path> install-strip`

```
cd /usr/bin
sudo cp <some-dir-abs-path>/usr/bin/iscan .

cd /usr/lib/sane
sudo cp <some-dir-abs-path>/usr/lib/sane/libsane-epkowa.la .
sudo cp <some-dir-abs-path>/usr/lib/sane/libsane-epkowa.so.1.0.15 .

cd /usr/sbin
sudo cp <some-dir-abs-path>/usr/sbin/iscan-registry .
```

Debugging Epkowa
----------------

`export SANE_DEBUG_EPKOWA=<VALUE>`

with <VALUE> one of

```
FATAL
MAJOR
MINOR
INFO
CALL
DATA
CMD
HEX
IMG
```
