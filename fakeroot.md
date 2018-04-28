# Building fakeroot-tcp on Arch WSL
Instructions from user `some_random_guy_5345` on Reddit
[Full thread here](https://www.reddit.com/r/archlinux/comments/7rycmu/cannot_build_fakeroottcp_without_fakeroot/)

#### Instructions
Open up the PKGBUILD for `fakeroot-tcp`
Install the dependencies
   Example: `depends=('glibc' 'filesystem' 'sed' 'util-linux' 'sh')``
   `makedepends=('po4a')``

Perform commands in PKGBUILD
   wget http://ftp.debian.org/debian/pool/main/f/fakeroot/fakeroot_1.22.orig.tar.bz2
   tar xvf fakeroot_1.22.orig.tar.bz2
   cd fakeroot-1.22/
   ./bootstrap
   ./configure --prefix=/opt/fakeroot \
       --libdir=/opt/fakeroot/libs \
       --disable-static \
       --with-ipc=tcp
   make
   sudo make install
   /opt/fakeroot/bin/fakeroot
   exit
   exit

If you receive any errors after running `/opt/fakeroot/bin/fakeroot`, ignore them.

This will install `fakeroot` in a different directory.

Then prefix $PATH with /opt/fakeroot/bin/, install fakeroot-tcp from makepkg, and remove /opt/fakeroot.
