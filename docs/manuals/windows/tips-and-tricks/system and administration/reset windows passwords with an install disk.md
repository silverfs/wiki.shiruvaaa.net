
# Reset Windows Passwords with an Install Disk

There are numerous ways to reset a password. Whatever your reason, you do you. This guide will explain in steps how to get a working command prompt at the login screen to change settings (user management, as an example). Following this guide requires an install disk of Windows (I will use Windows 10 in this guide) so if you don't have one, just go to Microsoft's website and download the official Windows 10 ISO file (No product key required for download). The Media Creation tool allows you to download & make a Windows 10 install CD/USB derived from your Windows PC.

<br>

## Step 1: Boot from Windows 10 Install Disk
First, insert the Windows 10 installation disk into your PC and reboot from it. Remember to change the boot order and disable UEFI secure boot temporarily in BIOS/UEFI firmware so you can boot from CD or USB.


![[press-key-boot-from-cd.png]]

When the screen displays "Press any key to boot from CD or DVD", you guessed it: we need to press a key ;). If you don't, the PC will automatically boot from your Windows Hard Disk.

## Step 2: Replace Sethc.exe with Cmd.exe
When you come to the Windows Setup screen, just press ++shift+f10++ key combinations to launch Command Prompt.

![[windows-10-setup.png]]

Type following commands in Command Prompt window and press Enter key each time you enter a command. Replace d:\ with the drive letter of your Windows installation.

```cmd
copy C:\windows\system32\sethc.exe C:\
copy /y C:\windows\system32\cmd.exe d:\windows\system32\sethc.exe
```

(This will essentially replace the sticky keys with the command prompt).

![[replace-sethc-with-cmd.png]]

Now you can close the Command Prompt, cancel Windows Setup, restart your PC and remove Windows install disk.

![[exit-windows-setup.png]]

## Step 3: Reset Forgotten Windows 10 Password
After your PC restarts. At Windows 10 logon screen, press Shift Key 5 times consecutively and it will launch Command Prompt with administrator privileges.


Now, to change the password use the following command. Do not forget to replace 'username' with actual local account's username and 'newpassword' with the password that you want to set.

```cmd
net user username newpassword
```

![[reset-windows-password-at-logon.jpg]]


Close the Command Prompt. You can now log into Windows 10 with the new password.