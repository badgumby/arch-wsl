# Building fakeroot-tcp on Arch WSL
Modified original instructions from user `some_random_guy_5345` on Reddit.
[Full thread here](https://www.reddit.com/r/archlinux/comments/7rycmu/cannot_build_fakeroottcp_without_fakeroot/).

#### Instructions
1. Open up the PKGBUILD for `fakeroot-tcp` located on the AUR.

2. Install the dependencies.

   `glibc` `util-linux` `po4a`

3. Perform commands in PKGBUILD. This will install `fakeroot-tcp` in a different directory.

```
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

```
   Note: If you receive any errors (like acl_get_fd, acl_get_file, etc) after running `/opt/fakeroot/bin/fakeroot`, ignore them. These are a bug in this build of `fakeroot` caused by mismatch in `glibc`.

4. Backup base `faked` and `fakeroot` from `/sbin` and `/bin` and replace with newly built files

```
   sudo mv /bin/faked faked-bak
   sudo mv /bin/fakeroot fakeroot-bak
   sudo mv /sbin/faked faked-bak
   sudo mv /sbin/fakeroot fakeroot-bak

   sudo cp /opt/fakeroot/bin/faked /bin/
   sudo cp /opt/fakeroot/bin/faked /sbin/
   sudo cp /opt/fakeroot/bin/fakeroot /bin/
   sudo cp /opt/fakeroot/bin/fakeroot /sbin/

```

5. You can now install PKGBUILD's using `makepkg -si`
