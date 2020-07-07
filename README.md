# Arch on WSL 2
#### These are the basic instructions to build Arch on WSL 2.
For instructions on bootstrapping WSL 1, please [go here.](../master/WSL_1)

***

### Installing Archlinux for WSL 2 from bootstrap

1. Setup, upgrade to the `Windows Subsystem for Linux 2`. [Go here.](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index)

2. Install another distro from the `Windows Store`.

      `You could also do these next few steps on a separate linux machine`

3. Get the Arch Linux bootstrap (latest version at time of writing).

      `wget https://mirrors.edge.kernel.org/archlinux/iso/latest/archlinux-bootstrap-2020.07.01-x86_64.tar.gz`

4. Extract the image.

      `sudo tar -zxvf archlinux-bootstrap-2020.07.01-x86_64.tar.gz`

5. Enter the extraced directory `root.x86_64`

      `cd root.x86_64`

6. Uncomment some servers in the pacman mirrorlist.

      `vim etc/pacman.d/mirrorlist`

7. Recompress files in `root.x86_64` directory.

      `sudo tar -czvf root.tar.gz *`

8. Move the `root.tar.gz` file to an accessible Windows directory.

      `sudo mv root.tar.gz /mnt/c/Users/USERNAME/root.tar.gz`

9. Open a PowerShell prompt as Admin, and issue the following command:

      `wsl --import Archlinux PATH_WHERE_VHD_SHOULD_BE_CREATED C:\Users\USERNAME\root.tar.gz`

10. Launch Archlinux from WSL.

      `wsl -d Archlinux`

11. Initialize Arch keyring.

      `pacman-key --init`
      `pacman-key --populate archlinux`

12. Install base.

      `pacman -Syyu base base-devel git vim wget reflector fish`

13. Enable `multilib` (if you want).

      `linenumber=$(grep -nr "\\#\\[multilib\\]" /etc/pacman.conf | gawk '{print $1}' FS=":")`
      `sed -i "${linenumber}s:.*:[multilib]:" /etc/pacman.conf`
      `linenumber=$((linenumber+1))`
      `sed -i "${linenumber}s:.*:Include = /etc/pacman.d/mirrorlist:" /etc/pacman.conf`

14. Sync package databases.

      `pacman -Syy`

15. Update mirror list (replace United States with preferred repo mirror country).

     `reflector --country "United States" --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist`

16. Set `root` user password.

      `passwd`

17. Create new user.

      `useradd -m -G wheel -s /bin/fish -d /home/username username`

18. Set password on user.

      `passwd username`

19. Enable `wheel` group.

      `sed -i '/%wheel ALL=(ALL) ALL/c\%wheel ALL=(ALL) ALL'  /etc/sudoers`

20. Edit Arch locale and regenerate.

      `sed -i 's:#en_US.UTF-8 UTF-8:en_US.UTF-8 UTF-8:g' /etc/locale.gen`
      `locale-gen`
      `echo LANG=en_US.UTF-8 >> /etc/locale.conf`
      `echo LANGUAGE=en_US.UTF-8 >> /etc/locale.conf`
      `echo LC_ALL=en_US.UTF-8 >> /etc/locale.conf`

***

### Install and Configure Windows Terminal

1. From the Windows Store, install [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab).

2. Copy any `.png` or `.ico` that you would like to use as distro icons to the following folder:

   `%LOCALAPPDATA%\packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState\`

3. Edit the `settings.json` file for `Windows Terminal` and add the following line to the list item:

   `"icon" : "ms-appdata:///roaming/filename.ico",`

***

### Install Yay AUR Helper and Pacman Wrapper (https://github.com/Jguer/yay)

1. Create a directory for the yay PKGBUILD files and enter it.

   `mkdir ~/yay`
   `cd ~/yay`

2. Download yay PKGBUILD from AUR.  

   `wget --no-check-certificate https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=yay --output-document=./PKGBUILD`

3. Run `makepkg` to build and install yay.

   `makepkg -si`

***

### Powerline Fonts

Download and install fonts for Powerline. [Download here.](https://github.com/powerline/fonts/)

***

### Launch X11 apps from the shell to Windows display

1. Download, install and then launch [VcXsrv](https://sourceforge.net/projects/vcxsrv/).

   + `Select display settings` - Take default
   + `Select how to start clients` - Take default
   + `Extra settings` - Be sure `Disable access control` is checked.
   + `Configuration complete` - Click `Finish`

2. Create a firewall rule in Windows to allow communication from WSL 2 to host OS.

   `New-NetFirewallRule -DisplayName "X Server - WSL 2" -Direction Inbound -Program "C:\Program Files\VcXsrv\vcxsrv.exe" -Action Allow`

3. Get the IP of your local computer of the `vEthernet (WSL)` interface from CMD or PowerShell:

   `ipconfig`

4. Export output to display using IP address collected in step 3.

   For `bash` and `zsh`:
   `export DISPLAY=192.168.1.100:0`
   For `fish`:
   `set -x DISPLAY 192.168.1.100:0`

   Note: Add to `~/.bashrc`, `~/.zshrc`, or `~/.config/fish/fish.config` and you won't need to type it again on the next WSL launch.

5. Install an xorg app for testing (We will use the Xorg Calculator).

   `sudo pacman -S xorg-xcalc`

6. Launch `xcalc` to test.

   `xcalc`

7. If Xming is working properly, Xorg Calculator should popup as a new window.
