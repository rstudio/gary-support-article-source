---
title: "Using RStudio Server on Windows"
date: "Last updated on 2020-06-17"
output:
  bookdown::html_document2:
    keep_md: true
    number_sections: false
    toc: true
toc-title: "Table of Contents"
knit: "bookdown::render_book"
---

<!--html_preserve--><script>
  addClassKlippyTo("pre.bash, pre.r, pre.markdown");
  addKlippy('left', 'top', 'auto', '1', 'Copy code', 'Copied!');
</script><!--/html_preserve-->

## Introduction {#intro}

This article explains how to set up RStudio Server on Windows platform.

As RStudio Server has primarily been designed to work with Linux distributions, Windows users require a virtualization technology, called ["Windows Subsystem for Linux" (WSL)](https://docs.microsoft.com/en-us/windows/wsl/){target="_blank"}, to run a Linux kernel inside their system.

* **Note:** We do not support running RStudio Server Pro on WSL at this time, and recommend RStudio Server on WSL only for single-user use, not as a way to deploy a multi-user server.

## Prerequisite {#prereq}

We recommend using the enhanced WSL (version 2) for setting up RStudio Server, which requires **Windows 10 (Version 2004; Build 19041 or higher)**.

1. You can check your Windows version by pressing the `Windows logo key + R`, type `winver`, and hit enter.
1. In case your system is lower than the suggested version, please [update to the latest Windows version](ms-settings:windowsupdate){target="_blank"}.

## WSL Setup {#wsl-setup}

Once you have met the [prerequisite](#prereq), you need to enable WSL features on your system.

* **Note:** Use "Copy code" button below to easily copy all the required commands to clipboard and paste it directly into your PowerShell by pressing `Ctrl + V` key. Note that you have to hit enter key to execute the pasted code chunk in the console.

1. Open "Windows PowerShell" with admin privileges.
1. Turn on the WSL feature using the following command in the PowerShell:

    
    ```bash
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```

1. Enable the "Virtual Machine Platform" feature by running the following command in the PowerShell:

    
    ```bash
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
    ```

1. Restart your system.
1. Open "Windows PowerShell" with admin privileges again.
1. Set WSL 2 as your default version using the following command in the PowerShell:

    
    ```bash
    wsl --set-default-version 2
    ```

1. Download and install [the latest WSL2 Linux kernel](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) if you see the following message:

    > WSL 2 requires an update to its kernel component. For information please visit `https://aka.ms/wsl2kernel`

## Linux Distribution Setup {#Linux-setup}

After the [WSL 2 feature](#wsl-setup) has been enabled, you have to download and install a Linux distribution from [Microsoft Store](https://aka.ms/wslstore){target="_blank"}.

While RStudio Server does support other distros, details in this article will be specific to Ubuntu 20.04.

You can download and manually install the distro by clicking the link below:

* [Download Ubuntu 20.04](https://aka.ms/wslubuntu2004).

Once the distribution has been installed and launched, you will be prompted the initial user configuration like below:

> Installing, this may take a few minutes...                                                                                                             
> 
> Please create a default UNIX user account. The username does not need to match your Windows username.                                                  
> For more information visit: `https://aka.ms/wslusers` 
> 
> Enter new UNIX username:                                                                                                                               
  
Provide a user name followed by password that will be used for both your WSL distribution and RStudio Server authentication later on.

You can now close the WSL instance by typing the following command:
   

```bash
exit
```

* **Note:** Unlike the PowerShell, you cannot use `Ctrl + V` shortcut key to paste the clipboard content into the WSL terminal. Alternatively, press mouse right-click button to emulate the paste function.

## RStudio Server Setup {#rstudio-server-setup}

Now that you have successfully installed a Linux distribution on your system via WSL, you can setup and run RStudio Server by following the procedures below:

1. Press `Windows` logo key to open start menu, then type `wsl` to launch your installed Linux distro.
1. Make sure your Linux packages are up-to-date using the following command (we will assume that you have installed a Ubuntu distribution):

    
    ```bash
    sudo apt-get update
    sudo apt-get upgrade -y
    ```

    * **Note:** Provide the password of your user account that you created for the Linux system, not the Windows password to log in for your PC.

1. Add the following apt credential to your repository list  for Getting the Latest R (v4.0.1 as of this writing):

    
    ```bash
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
    sudo add-apt-repository 'deb https://cloud.r-project.org/bin/Linux/ubuntu focal-cran40/'
    ```

    * **Note:** More detailed information on installing the latest R for Linux can be found at [the official CRAN page](https://cran.r-project.org/bin/Linux/ubuntu/){target="_blank"}.

1. Install R with some essential dependencies for the RStudio Server and devtools:

    
    ```bash
    sudo apt install -y r-base r-base-core r-recommended r-base-dev gdebi-core build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev
    ```

1. Install latest stable release of RStudio Server for Ubuntu 18 or newer:

    
    ```bash
    wget https://rstudio.org/download/latest/stable/server/bionic/rstudio-server-latest-amd64.deb
    sudo gdebi rstudio-server-latest-amd64.deb
    ```

    * **Note1:** You may receive a warning message like "Couldn't find an alternative telinit implementation to spawn." This is a known issue and can be safely ignored.

    * **Note2:** See the [RStudio Release](https://rstudio.com/products/rstudio/download-server/){target="_blank"} page for full list of supported platforms. Make sure you choose RStudio Server (not Desktop) and match with your installed Linux distribution type and version.

    * **Note3:** While we recommend installing the current stable release, you may evaluate pre-release versions by consulting their download pages at [RStudio Preview](https://rstudio.com/products/rstudio/download/preview/){target="_blank"} or [RStudio Daily](https://dailies.rstudio.com/){target="_blank"}.

1. Launch RStudio Server:

    
    ```bash
    sudo rstudio-server start
    ```

    * **Note1:** If there was no error message, you can assume that your RStudio Server has been successfully loaded.

    * **Note2:** If you terminate and restart WSL, you will need to run `sudo rstudio-server start` again to get RStudio Server up and running.
      
1. Access your server at [http://localhost:8787](http://localhost:8787){target="_blank"}.

    * **Note:** The user name and password are the same as your Linux system's.

## Removing RStudio Server {#remove-rstudio-server}

The following steps explain how to uninstall RStudio Server from WSL:

1. Press `Windows` logo key to open start menu, then type `wsl` to launch your installed Linux distro.
1. To stop RStudio Server, type the following command in the WSL terminal:

    
    ```bash
    sudo rstudio-server stop
    ```

    * **Note:** Provide the password of your user account that you created for the Linux system, not the Windows password to log in for your PC.

1. Remove your Server:

    
    ```bash
    sudo apt-get remove rstudio-server -y
    ```

## Upgrading RStudio Server {#rstudio-server-upgrade}

More information on how to upgrade RStudio Server can be found at [this article](https://support.rstudio.com/hc/en-us/articles/216079967-Upgrading-RStudio-Server){target="_blank"}.

<!--chapter:end:draft.Rmd-->

