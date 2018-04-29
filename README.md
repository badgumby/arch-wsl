# Arch on WSL
#### These are the basic instructions to build Arch on WSL.
These instructions will not allow you to install from `PKGBUILD` files. `makepkg` will not build packages on Arch WSL without a custom compiled `fakeroot`.
The kernel in WSL does not support sysv, so you would need to build a copy of `fakeroot-tcp` from source, then install it.
Basic instructions are included in the file `fakeroot.md`.

***

### Installing Arch WSL from bootstrap

1. Install the `Windows Subsystem for Linux`.

2. Open Windows Command Prompt.

   `cmd`

3. Uninstall any existing installs.

   `lxrun /uninstall /full /y`

4. Install Ubuntu base.

   `lxrun /install /y`

5. Enter root bash.

   `bash ~`

6. Get the Arch Linux bootstrap (latest version at time of writing).

   `wget https://mirrors.kernel.org/archlinux/iso/latest/archlinux-bootstrap-2018.04.01-x86_64.tar.gz`

7. Extract the image.

   `tar -zxvf archlinux-bootstrap-2018.04.01-x86_64.tar.gz`

8. Uncomment some servers in the pacman mirrorlist.

   `vim ~/root.x86_64/etc/pacman.d/mirrorlist`

9. Update name servers (swap IP's for preferred DNS).

   `echo "nameserver 8.8.8.8" >> ~/root.x86_64/etc/resolv.conf`

   `echo "nameserver 8.8.4.4" >> ~/root.x86_64/etc/resolv.conf`

10. Exit bash.

      `exit`

11. Go to `lxss` directory and rename `rootfs` directory.

      `%LOCALAPPDATA%\lxss\` and rename directory `rootfs` to `rootfs_old`

12. Now, go here and and rename extracted Arch `root.x86_64`.

      `%LOCALAPPDATA%\lxss\root\` and rename directory `root.x86_64` to `rootfs`

13. Move (cut), do not copy directory (if you copy, it will break symlinks).

      `%LOCALAPPDATA%\lxss\root\rootfs` to `%LOCALAPPDATA%\lxss\rootfs`

14. Enter bash.

      `bash ~`

15. Initialize Arch keyring.

      `pacman-key --init`

      `pacman-key --populate archlinux`

16. Install base.

      `pacman -Syyu base base-devel git vim wget reflector`

17. Enable `multilib` (if you want).

      `linenumber=$(grep -nr "\\#\\[multilib\\]" /etc/pacman.conf | gawk '{print $1}' FS=":")`

      `sed -i "${linenumber}s:.*:[multilib]:" /etc/pacman.conf`

      `linenumber=$((linenumber+1))`

      `sed -i "${linenumber}s:.*:Include = /etc/pacman.d/mirrorlist:" /etc/pacman.conf`

18. Sync package databases.

      `pacman -Syy`

19. Update mirror list (replace United States with preferred repo mirror country).

      `reflector --country "United States" --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist`

20. Set `root` user password.

      `passwd`

21. Create new user.

      `useradd -m -G wheel username`

22. Set password on user.

      `passwd username`

23. Enable `wheel` group.

       `sed -i '/%wheel ALL=(ALL) ALL/c\%wheel ALL=(ALL) ALL'  /etc/sudoers`

24. Edit Arch locale and regenerate.

      `sed -i 's:#en_US.UTF-8 UTF-8:en_US.UTF-8 UTF-8:g' /etc/locale.gen`

      `locale-gen`

      `echo LANG=en_US.UTF-8 >> /etc/locale.conf`

      `echo LANGUAGE=en_US.UTF-8 >> /etc/locale.conf`

      `echo LC_ALL=en_US.UTF-8 >> /etc/locale.conf`

25. Exit bash.

      `exit`

26. In Command Prompt, set default user for linux subsystem.

      `lxrun /setdefaultuser username`

28. On next enter, you should login as your newly created user.

      `bash ~`

29. Replace `bash.ico` in `%LOCALAPPDATA%\lxss\` with preferred Arch icon. Be sure to name `bash.ico`.

***

### Install Aura AUR helper

1. You will first need to rebuild `fakeroot` following the instructions [here](../master/fakeroot.md)

2. Create a directory for the aura PKGBUILD files and enter it.

   `mkdir ~/aura-bin`
   `cd ~/aura-bin`

3. Download aura PKGBUILD from AUR.  

   `wget --no-check-certificate https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD\?h\=aura-bin --output-document=./PKGBUILD`

4. Run `makepkg` to build and install aura.

   `makepkg -si`

***

### Install wsl-terminal and z shell

1. Download [wsl-terminal](https://github.com/goreliu/wsl-terminal/releases).
2. Extract folder and launch `wsl-terminal`.
3. Enter home directory.

   `cd ~`

3. Right-click the title bar, select `Options` then select your theme from the `Looks` section.

4. Install `zsh`.

   `sudo pacman -S zsh`

5. Install `oh-my-zsh`.

   `sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"`

   Note: This will install `oh-my-zsh` and set `zsh` to be the logged in users default shell, but since WSL launches bash, you will need to `exec zsh` after login each time.

6. Configure your `zsh` theme.

   `vim ~/.zshrc`

   `ZSH_THEME="agnoster"` (This is my preferred theme)

7. Install fonts for Powerline. [Download here.](https://github.com/powerline/fonts/tree/master/DejaVuSansMono)
8. Right-click the title bar, select `Options`, then select your installed font from the `Text` section.
