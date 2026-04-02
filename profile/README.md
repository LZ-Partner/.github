# LZ & Partner – Engineering Onboarding

This document provides a **short onboarding guide** for engineers working with the
LZ & Partner GitHub organization.

The goal is to get new developers productive quickly.
Detailed rules (naming, versioning, repository structure) are defined separately.

## Table of Contents

- [Organization and Rules](#organization-and-rules)
  - [Organizational Structure](#organizational-structure)
- [SSH-Key](#ssh-key)
- [Git in Powershell aufsetzen](#setup-git-in-powershell)
- [STM32 development environment setup](#stm32-development-environment-setup)
  - [Windows Setup](#windows-stm32-setup)
  - [Linux Setup](#linux-stm32-setup)
  - [Build STM32 Project](#build-stm32-project)
- [Latex setup in VSCode](#latex-setup-in-vscode)
- [Kicad setup](#kicad-setup)
  - [Windows setup](#windows-kicad-setup)
  - [Linux setup](#linux-kicad-setup)
- [Commit Regeln und konventionen](#commit-regeln-und-konventionen)
- [Troubleshooting](#troubleshooting)

---

## Organization and Rules
All hardware and software development is managed through this GitHub organization.

### Organisational structure
The final structure is still under definition. For now, the hierarchy follows this principle:

- GitHub Organization
  - Teams
    - Product Teams
    - Platform / Shared Components
    - Tooling & Infrastructure
  - Repositories
    - `product-*` (product-specific hardware, firmware, software)
    - `platform-*` (shared libraries and reusable components)
    - `tools-*` (internal tooling)
    - `infra-*` (CI/CD, automation)
    - `.github` (organization-wide defaults)


Access to repositories is managed via **GitHub Teams**.
Direct commits to protected branches are not allowed.

---

## SSH-Key

SSH-Keys are used to securely authenticate with GitHub without using a password. This guide should work for Windows and Linux

### 1. Generate the Key

To generate a new SSH-key open your terminal/cmd and use the following command to generate it. This is the default option with no additional metadata, for more information read [this](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).
```
ssh-keygen
```
Now you have to select
-  File to store it in (default works unless you want multiple setups)
-  Passphrase either leave it empty or set a passpharse the [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) provides additional information

### 2. Find and copy the key
Default locations:
- Windows:

    `C:\Users\YOUR_USERNAME\.ssh\id_ed25519.pub`

- Linux:

    `~/.ssh/id_ed25519.pub`

To print the key in your terminal

cat `~/.ssh/id_ed25519.pub`

Copy the entire output

### 3. Add SSH Key to GitHub 
  1. In your browser visit [GitHub](https://github.com) 
  2. Click on your Avatar in the top right corner
  3. Navigate to:
      - Settings
      - Access -> SSH and GPG keys
  4. Click "New SSH Key"
  5. Paste your key and save


### 4. Test the connection:

```
ssh -T git@github.com
```

The output should be something like this ```Hey YOURUSERNAME! You've successfully authenticated ....```

### Optional: multiple SSH keys
If you use multiple Git platforms or keys, configure `~/.ssh/config` per key like this:

```text
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_key
```

---

## Setup Git in Powershell
If you prefer a Youtube-Tutorial please watch following [Tutorial](https://www.youtube.com/watch?v=TwKyqOf5mJ4) after that for a better experience jump to point 4. 

1. Install Git
   - Download Git from: https://git-scm.com/downloads
   - Use the default installation options
   - Ensure OpenSSH is enabled

2. Verify Installation
    ```powershell
    git --version
    ```

3. Configure Git Identity
Configure your global Git identity (required for commits):
    ```powershell    
    git config --global user.name "<your-github-username>"
    git config --global user.email "<your-company-email>"
    ```
    Verify configuration: `git config --list`
4. For a prettier Git in the Powershell terminal:)
    ```powershell
    Install-Module posh-git -Scope CurrentUser -Force
    Import-Module posh-git
    Add-PoshGitToProfile -AllHosts
    ```

    Now we add helpful extensions which indicate what is commited and what is ready to get pushed as well additionally it shows the number of untracked files.
    ```powershell
    C:\Users\username\path\to\git\directory [main ≡ +1 ~1 -0 !]>
    ```    
5. To test your setup you should clone some LoccoZ-Organisation Repository (gitlab-profile):
    ```powershell
    > cd C:\Users\$USERNAME$\path\to\desired\destination
    > git clone git@gitlab.com:loccoz-system-ag/loccoz-organization.git
    ```

---

## STM32 development environment setup

We use [VSCode](https://code.visualstudio.com/) for STM32 development. This chapter is very demanding and the setup process isn't trivial. First we guide you through the Windows installation and setup process afterwards follows, linux follows. All tools except VSCode (used for documentation) were uninstalled to ensure a celan setup for this guide. 

### Windows STM32 Setup

### 1. Install VSCode IDE Windows
Install VSCode using [this](https://code.visualstudio.com/) link
- Select Windows Installation and download it
- The Setup Wizard window will open use default settings and accept license agreements continue until installation starts
- Launch VSCode

Here a [Video Tutorial](https://www.youtube.com/watch?v=f8_uF_IDV50) about basic functionalities of VSCode

### 2. Install VSCode Plugins
 
Following VSCode extensions are required, here [this](https://marketplace.visualstudio.com/VSCode) link redirects you to the market place where you can find extensions

- STMicroelectronics.stm32-vscode-extension
- ms-vscode.vscode-serial-monitor


Are the important extensions to setup
1. Visit the two links [STM-VSCode extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension), [SerialMonitor extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-serial-monitor)
2. Press the install button
3. A pop up will spawn, open in VSCode accept
4. Now VSCode should open, install the corresponding plugin

Plugins should be installed.

### 3. Install the STM32 Applications

The following three applications are important

- [CubeClt](https://www.st.com/en/development-tools/stm32cubeclt.html) is a bundle of toolchain components. During installation use default settings and **do not change the installation location**.
- [CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html#) is a graphical project configurator. Again, use the default settings and **do not change the installation location**.
- [MCUFinder](https://www.st.com/en/development-tools/st-mcu-finder-pc.html) is a graphical tool to select an ST device. Again, use default settings and **do not change the installation location**.

For all three repeat the process described below

1. Visit the link CubeClt link from above
2. Press the button get Software you'll be redirected to a page with download options
3. Since we are on Windows we use the latest Windows installer
4. You have to accept the licens and create an account to install it download will start
5. After download finished open the zip folder and start the `st-stm32cubectl-versionnumber.exe` by double clicking
6. Setup wizzard wil pop up, you have to agree the license agreement
7. Default settings are good you don't need to change something press continue or next the whole time until installation process starts finally finish the process

### 4. Install Cmake
CMake is an open-source build system generator for software projects. If you want more information about CMake visit [this](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Why%20CMake.html) Link. The Section will guide you through the installation process.

1. Visit [this](https://cmake.org/download/) link to download
2. Press on the green download button in the top right 
3. Select the `cmake-versionnumber-x86_64.msi` file
4. Install it by double clicking the `.msi` file in the Download folder
5. Select the default selections, most important is `Add CMake to the PATH environment variable`



### 5. System environment variables (Optional but recommended)
This is optional but to be safe sometimes `Build fails because cmd.exe was not found`. You have to add the location of the executable to the path usually (`C:\Windows\System32`)

1. Search for environment variables
2. Click Environment Variables
3. Select the "Path" system variable and click edit:
4. Click "New" and add the `C:\Windows\System32` path and click OK

### 6. Install Ninja (Optional but recommended)
Ninja is a small build system with focus on speed. Ninja aims to be an assembler and builds projects fast but read more if interested [here](https://ninja-build.org/)

1. Download ninja from [this](https://github.com/ninja-build/ninja/releases) link here, select ninja-win.zip
2. Unzip the folder `ninja.exe` should appear
3. Place the downloaded `ninja.exe` into the folder `C:\Program Files\ninja` you have to create the ninja folder and place the `.exe` in it
4. Add the `C:\Program Files\ninja` folder to your system path this is a [tutorial](https://windowsloop.com/how-to-add-to-windows-path/).

### 7. Install STLink USB drivers

Finally you've setup all the required software the only step is to install ST-Link drivers

1. Open the STM32 extension in the left bar
2. Plug in your STM32Board
3. Click on the Install STLink USB drivers
4. If there is a driver update the update wizzard should pop up press continue and wait until it finishes

If it won't work open the VSCode Console and enter
```code
stlinkupgrade
```
It should open a window if it's the case go through step 4. and execute it

You should now be ready to proceed ;). It's recommended to directly jump [here](#build-stm32-project) to start a project.

---

### Linux STM32 Setup

Install VSCode similar to the Windows installation
> **Important**: using your operating systems default package manager, snap/flatpack vversions may break.

### 1. Install VSCode IDE Linux
Open the terminal and enter following commands
```
sudo apt update
sudo apt install code
```
Now VSCode should be installed

### 2. Install VSCode Plugins
 
Following VSCode extensions are required, here [this](https://marketplace.visualstudio.com/VSCode) link redirects you to the market place where you can find extensions

- STMicroelectronics.stm32-vscode-extension
- ms-vscode.vscode-serial-monitor


Are the important extensions to setup
1. Visit the two links [STM-VSCode extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension), [SerialMonitor extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-serial-monitor)
2. Press the install button
3. A pop up will spawn, open in VSCode accept
4. Now VSCode should open, install the according plugin

Plugins should be installed.

### 3. Install the STM32 Applications

Following three STM32 Applications have to be installed. Here are the default paths for each STM32 application

 - [CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html): ```/opt/st/```
 - [CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html): ```/usr/local/STM...```
 - [MCUFinder](https://www.st.com/en/development-tools/st-mcu-finder-pc.html): ```/usr/local/STM...```

For all three applications repeat the process the process is similar

1. Visit the Webpage of [CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html)
2. Navigate to download section and select if possible your Linux distro else select generic
3. After Download is completed open terminal and `cd Downloads`
4. Follow the steps below

```bash
unzip DOWNLOADED_FOLDER.zip -d cubeclt #-d flag will create a new folder where the files from unzip land
cd cubeclt #you change directory into the new created where the files are located
sudo ./installCubeClt #you will start the installation using their shellscript
```
5. A window will pop up default values should be ok please pay attention that the file paths are similar to the above file paths
6. Finally Install ncurses via apt
```bash 
sudo apt update
sudo apt-get install libncurses5
```

The latest versions of ubuntu do not contain libncurses5 in their repositories anymore To install it manually, see [here](https://gist.github.com/schilkp/8fcab720fffc11cb0034010c1dc05404)

### 4. Check the installation
Finally you've setup all the required software the only step is to install ST-Link drivers

1. Open VSCode navigate to the left side bar
2. Select the butterfly aka STM32 extension
3. Click through the STM32CUBE KEY ACTIONS, STM32CubeMX, STMCUFinder and create empty project should work
4. Connect the STM32 Developmentboard via USB with your device
5. Below the KEY Actions are the STM32CUBE RESOURCES important is Install STLink USB drivers sometimes also called udev rules click on it
6. A window will pop up in the bottom right corner install it

Now you should be able to develop STM32 applications visit this link [here](#build-stm32-project)

---

### Build STM32 Project

### 1. Generate Project

1. Open STMCubeMX deskto application
2. Click on start my project from ST Board
3. Select the corresponding ST Board
4. A new panel launches where you will see the Pinout Configuration
5. Navigate to Project Manager and give the project a name
6. Define the Toolchain/IDE `CMake`
7. Select the default Compiler/Linker `GCC`

### 2. Flash the chip

1. In the bottom left corner of VSCode there should exist a build button. Press it the project should build
2. Now open the debugger extension and run the file if a pop-up appears select `STM32Cube: Launch ST-Link GDB Server`
3. Perhaps you have to build a json run file
4. Run the project from the left bar, it will stop at `HAL_Init()`, press `F5` or continue button this is the blue arrow


---

## LaTeX setup in VSCode

You should have installed VSCode for your operating system if this is not the case please visit [Windows Setup](#1-install-vscode-ide-windows), [Linux](#1-install-vscode-ide-linux)

### Windows LaTeX setup
1. Visit following [page](https://tug.org/texlive/windows.html)
2. Select the easy installer and download the `.exe`
3. Launch the `.exe` default options should be fine click through it
4. Wait installation may take up to 3 hours and more depending on your device

The basics are done jump to the following section [VSCode setup](#vscode-setup)

### Linux LaTex setup

You only need to install TexLive! Open the terminal and enter following commands 

```bash
sudo apt update
sudo apt install texlive-full
```

The section below will explain VSCode installation

### VSCode setup

To setup LaTex in VSCode follow these points

1. Follow [this](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) Link
2. Press the green install button a redirection window will pop up select accept VSCode will open
3. Install the LaTeX Workshop extension

<span style="color:red">Masiar or Jonathan how to change the out dir for aux files</span>

To test your LaTeX setup please clone a LaTeX and look if it works below there are some useful links if you never written latex

- [LaTeX Tutorial](https://www.overleaf.com/learn)
- [TeX Documentation](https://tug.org/begin.html)
---

## KiCad Setup

KiCad is a free and open source software which is widely used to design electrical circuits besides PCBs

### Windows KiCad Setup

To install KiCad on Windows simply navigate to [this](https://www.kicad.org/download/) page and select your operating system in our case Windows. You will get redirected to a page where Windows Downloads are available. Choose the latest stable release and your region i.e. Europe/Cern-Switzerland. Run the exe by clicking on it in the Explorer by double clicking on the ```Kicad-VERSION-Number-x86.exe``` it will open a window. Press continue or weiter. You now have to select following points in your installer (we recommend default settings).

- Local user or system wide installation
- Select components
- Select systempath

Installation should be finished

---

### Linux KiCad Setup

For Linux there exist two options: the default option using the installer from the [webpage](#webpage-setup) or the [console](#console-setup) installation.

#### Webpage setup

To install KiCad on Linux you have to visit [this](https://www.kicad.org/download/) web page.
- Select Linux and choose the latest stable release as well as your region Europe/Cern-Switzerland. It starts downloading a ```.tar``` file.
- Unpack the ```.tar``` file and run unpacked ```.AppImage``` file by clicking on it
- A window will Welcome... press next
- The next windows use default options (already selected by the setup wizard) and press next

KiCad should now be installed <span style="color:red">not 100% sure how to make installation persistent </span>

#### Console setup

Using the console it is straight forward

```
sudo apt update
sudo apt install kicad
```

Now it asks:
- Password (enter it)
- If you are sure to install it, either press y or j depending on your local language settings

Installations should be finished

---

## Commit Rules and Conventions
https://www.conventionalcommits.org/en/v1.0.0/
Another recommendation for commits is to record commit messages at shorter intervals and after minor changes, rather than recording one commit message after many changes. You can think of this in the same way as progressing in a game:
In certain difficult games, if this option is available, you try to save your progress at short intervals. This gives you as the player security in case your avatar dies during the game, your city is reduced to rubble, or even worse, your machine suddenly breaks down.

---

## Troubleshooting
__Please list any problems you encounter during installation here.__ 
1. Creation of SSH keys for Miguel and Timo: 
   1. SSH key generation with ID_ED25519 protocol
   2. Key was renamed to __gitlab_key__ via command line attribute “-f” (instead of id_ed25519.pub -> gitlab_key.pub)
   3. Key was successfully generated and connected to the official Gitlab server.
   4. __Problem__: When attempting to clone a repository from the Loccoz-System-AG Group, the following error occurred:
        ```
        Could not access remote. Permission denied (publickey)
        fatal: could not reach repository
        ```

2. Setting up and building STM32 Project:
    1. Check if all STM32 tools are installed
    2. Don't build it on **OneDrive**
    3. cd directly into the project i.e. if you named your project Test cd directly into the project and after you're in the folder open VSCode to compile
    4. Create a launch file