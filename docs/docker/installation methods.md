
# Installation

I'm not here to describe you in every single detail how to install Docker on your system. If you want every possible detail there is, I'd advice you to head over to the [official documentation](https://docs.docker.com/get-docker/).  
What I want to discuss here are the most common installations, as simple as possible. So without further ado, let's dive right into it.


## Windows

I'd advice you to install your Docker instance on a Linux machine or Linux subsystem, but if you have no other choice or just really want to do it on your Windows pc without WSL, it's possible. In order for you to work with Docker without a WSL2 backend, we need to make use of a Hyper-V backend and Windows containers, but we'll come back later on that.

!!! warning
	To run Windows containers, you need Windows 10/11, with either Professional or Enterprise edition. The Windows Home or Education editions will only allow you to run Linux containers.

<br>
<br>

You can download Docker Desktop for Windows [directly](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) or, if that doesn't work, through the [website](https://docs.docker.com/desktop/install/windows-install/) and execute the program.

When executing the program, you will be prompted to choose between WSL2 and Hyper-V, You'll have to choose the Hyper-V option (if you are not able to select an option, that means your system does not support it). If you do support the WSL2 option, you can check that one to continue the installation [here](http://192.168.1.21:3003/manuals/docker/installation#wsl2)

Next, follow the instructions the wizard gives you and finalize the installation. When successful, click _close_ to complete the installation process.

Great! You have now successfully installed Docker Desktop. You'll be able to start and view your containers, search in your image-list, and more; all from a neat GUI.
<br>
### WSL2

[[https://learn.microsoft.com/en-us/windows/wsl/|WSL]] (or Windows Subsystem for Linux) is there for developers to run a GNU/Linux environment, including most command-line tools, utilities and applications directly on Windows. This requires no virtual machine or a dualboot setup and runs with your Windows installation.  
In order to work with WSL, we'll have to install it first.


Check if you've already installed WSL before with this command:
```
wsl
```

If a bash terminal inside your normal terminal doesn't appear, it means you don't have WSL yet. Install WSL with the following command:
```
wsl --install
```

It should tell you afterwards that the operation was successful. You have installed Ubuntu as your Linux Distribution. You can now continue where you left off in the windows part, by downloading the application and following the magical instructions.

<br>

## GNU/Linux

*Coming soon ™️*