# set-Grub-password-Centos-7


**Core concepts :**

But before we start you must know GRUB2 offers two types of password protection:<br /> 
* Password is required for modifying menu entries but not for booting existing menu entries;<br />
* Password is required for modifying menu entries and for booting one, several, or all menu entries.<br />

So there are two definitions when we talk about set GRUB2 password in Linux. Earlier this was achieved using **"grub2-mkpasswd-pbkdf2"** but starting
with RHEL 7.2 the recommended tool to set GRUB2 password is by using **"grub2-setpassword"**

You can check **"/etc/grub.d/01_users"** file which will have below content.
```
#!/bin/sh -e
cat << EOF
if [ -f \${prefix}/user.cfg ]; then
   source \${prefix}/user.cfg
   if [ -n "\${GRUB2_PASSWORD}" ]; then
      set superusers="root"
      export superusers
      password_pbkdf2 root \${GRUB2_PASSWORD}
   fi
fi
EOF
```

and the same will be available inside **"/boot/grub2/grub.cfg".**
```
### BEGIN /etc/grub.d/01_users ###
if [ -f ${prefix}/user.cfg ]; then
   source ${prefix}/user.cfg
   if [ -n "${GRUB2_PASSWORD}" ]; then
      set superusers="root"
      export superusers
      password_pbkdf2 root ${GRUB2_PASSWORD}
   fi
fi
### END /etc/grub.d/01_users ###
```
So basically here the grub checks for "/boot/grub2/user.cfg" to get the "GRUB2_PASSWORD" and if found it will set the respective password for the
provided superuser which here is "root". The same password is then assigned using "password_pbkdf2"

## Set Grub2 password
First of all create a password using "grub2-setpassword" and "root" user.
```
[root@edge1-stage ~]# grub2-setpassword
```
This command will create (if already not existing) or update the content of "/boot/grub2/user.cfg" with the hash password.
```
[root@edge1-stage ~]# cat /boot/grub2/user.cfg
GRUB2_PASSWORD=grub.pbkdf2.sha512.10000.1
D83F78C7B031E23F919AE17EBDB883F943753C7F5B9534F1420128B05B12EEE997D516FF166904AC7D42C64471E7B1BB6B5B7C20F79A65D5
409DE0A83C188D1.
CC707D56A39BC9A2E9641B0751C9D1A0BE73E8F7E9E37A75F146156A13830ADBBB8B414D1FD473CE67FC7CF70E4E0D2D3199420329A9E614
E9B4F0328CBAB1A0
```
Continue with the reboot of your Linux node to validate your changes. When you get the splash screen with the grub menu, press "e" to edit the
highlighted kernel.<br />
It will prompt you for "username" and "password". here the username is root and the password will the one you used with "grub2-setpassword" .<br />
If the entries match then you will get the grub2.cfg content for editing purpose.

## Remove Frub2 password
To remove GRUB2 password you must delete the "/boot/grub2/user.cfg" file or clear the content of this file. So when there is no "GRUB2_PASSWORD"
defined, so the kernel will not prompt for one when some attempts to edit the grub menu.

