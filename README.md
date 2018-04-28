# Arch on WSL
These are the basic instructions to build Arch on top of the Legacy Ubuntu WSL.

1. Install the `Windows Subsystem for Linux`.

2. Open Windows Command Prompt
   `cmd`

3. Uninstall any existing installs

   `lxrun /uninstall /full /y`

4. Install Ubuntu base

   `lxrun /install /y`

5. Enter root bash

   `bash ~`

6. Get the Arch Linux bootstrap (latest version at time of writing)

   `wget https://mirrors.kernel.org/archlinux/iso/latest/archlinux-bootstrap-2018.04.01-x86_64.tar.gz`

7. Extract image

   `tar -zxvf archlinux-bootstrap-2018.04.01-x86_64.tar.gz`

8. Uncomment pacman mirrorlist

   `vim ~/root.x86_64/etc/pacman.d/mirrorlist`

9. Update name servers (swap IP's for preferred DNS)

   `echo "nameserver 8.8.8.8" ~/root.x86_64/etc/resolv.conf`
   `echo "nameserver 8.8.4.4" ~/root.x86_64/etc/resolv.conf`

10. Exit bash

   `exit`

11. Go here

   `%LOCALAPPDATA%\lxss\rootfs`

12. Delete these directories

   `bin` `etc` `lib` `lib64` `sbin` `usr` `var`

13. Now, go here

   `%LOCALAPPDATA%\lxss\root\root.x86_64`

14. Move (cut), dont copy these folders/files

   `bin` `etc` `lib` `lib64` `sbin` `usr` `var`

15. Enter bash

   `bash`

16. Initialize Arch keyring

   `pacman-key --init`
   `pacman-key --populate archlinux`

17. Install base

   `pacman -Syyu base base-devel vim wget reflector`

18. Enable multilib (if you want)

   `linenumber=$(grep -nr "\\#\\[multilib\\]" /etc/pacman.conf | gawk '{print $1}' FS=":")`
   `sed -i "${linenumber}s:.*:[multilib]:" /etc/pacman.conf`
   `linenumber=$((linenumber+1))`
   `sed -i "${linenumber}s:.*:Include = /etc/pacman.d/mirrorlist:" /etc/pacman.conf`

19. Sync package databases

   `pacman -Syy`

20. Update mirror list (replace United States with preferred repo mirror country)

   `reflector --country "United States" --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist`

21. Set `root` user password

   `password`

22. Create new user

   `useradd -m -G wheel username`

23. Set password on user

   `passwd username`

24. Configure wheel group

   `vim /etc/sudoers`

   (uncomment the following line)

   `%wheel ALL=(ALL) ALL`

25. Exit bash

   `exit`

26. In Command Prompt, set default user for linux subsystem

   `lxrun /setdefaultuser username`

27. On next enter, you should login as your newly created user

   `bash`
