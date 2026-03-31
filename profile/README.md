# LZ & Partner – Engineering Onboarding

This document provides a **short onboarding guide** for engineers working with the
LZ & Partner GitHub organization.

The goal is to get new developers productive quickly.
Detailed rules (naming, versioning, repository structure) are defined separately.

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

Access to repositories is done via **SSH keys**.

### Recommended resources
- GitHub SSH overview:  
  https://docs.github.com/en/authentication/connecting-to-github-with-ssh
- Generating a new SSH key:  
  https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
- Adding an SSH key to GitHub:  
  https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account


### Simplest way to setup SSH-Key works on Windows & Linux

Open your terminal/comandline enter the following command
```
ssh-keygen
```

Get your ssh key from the following path: ```C:\Users\**YOURUSERNAME**\.ssh\id_ed25519.pub``` (Windows)

Get your ssh key from the following path: ```/homw/**YOURUSERNAME**/.shh/id_ed25519.pub``` (Linux)


### Optional: multiple SSH keys
If you use multiple Git platforms or keys, configure `~/.ssh/config` per key like this:

```text
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_key
```

Test the connection:

`ssh -T git@github.com`

---

## Git in Powershell Aufsetzen
Falls du ein Youtube-Tutorial bevorzugst, schau dir bitte [dieses Tutorial](https://www.youtube.com/watch?v=TwKyqOf5mJ4) an. 

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
    Import-Module posh-git> Install-Module posh-git -Scope CurrentUser -Force
    Import-Module posh-git
    Add-PoshGitToProfile -AllHosts
    ```
    Nun solltest du nützliche Extensions, die dir signalisieren, was commited und pushed werden kann und was noch untracked ist:
    ```powershell
    C:\Users\username\path\to\git\directory [main ≡ +1 ~1 -0 !]>
    ```    
4. Als Test kannst du die LoccoZ-Organisation Repository (gitlab-profile) klonen:
    ```powershell
    > cd C:\Users\$USERNAME$\path\to\desired\destination
    > git clone git@gitlab.com:loccoz-system-ag/loccoz-organization.git
    ```

---

## Setup STM32 for developing

We setup VSCode for STM32 development.

1. Install VSCode using the following link [VSCode download](https://code.visualstudio.com/download)
2. Install the STM32 as well as Serial Monitor extension from the official Microsoft Marketplace

Besides your IDE you need additional external dependencies to start developing. We recommend creating an ST account to download the required software.

- STMicroelectronics.stm32-vscode-extension [STM-VSCode extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension)
- ms-vscode.vscode-serial-monitor [SerialMonitor extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-serial-monitor)

- [CubeClt](https://www.st.com/en/development-tools/stm32cubeclt.html) is a bundle of toolchain components. During installation use default settings and **do not change the installation location**.
- [CubeMX](https://www.st.com/en/development-tools/stm32cubeclt.html) is a graphical project configurator. As well use default settings and **do not change the installation location**.
- [MCUFinder](https://www.st.com/en/development-tools/st-mcu-finder-pc.html) is a graphical tool to select an ST device. Again use default settings and **do not change the installation location**.


After you've done everything it should look like that

<img src="images/stmExtensionPanel.png" alt="Extension Tab" width="25%">

Check if the most important tabs work by clicking through the extension panel, if everything launchs you're fine. Following Key Actions should run:

- STM32CubeMX
- STMCUFinder
- Create empty project

Check if **ST-LINK firwmare upgrades are required**: This is the case if the debugger on the development board is to old. You'll find it out if following error messages pup up

```console
Could not find the task 'Build'
```

or

```console
Unable to start debugging
```

To upgrade the firmware do the following:
- Plug in your board
- In VSCode, open the STM32 extension tab
- Select "Upgrade ST-Link firmware"
- Wait for updates to complete

If it still wont work enter the following command

```console
stlinkupgrade
```

### Bugs and nice to know

- **Build fails because cmd.exe was not found** The windows commandline could not be found by the build tools add it to the system path environment variable [Tutorial](https://www.thewindowsclub.com/how-to-add-edit-a-path-variable-in-windows) add the following path into the system environment variables ```C:\Windows\System32```
- **Bad CMake executable**. Check to make sure it is intalled if not install CMake and Ninja
  1. Install [CMake](https://cmake.org/download/) installer and run it. Make sure to, when prompted, add CMake to the path of the current user. **This is not the default option**.
  2. Install the [Ninja](https://github.com/ninja-build/ninja/releases) build tool executable.
  3. Place the downloaded ```ninja.exe``` into the folder ```C:\Program Files\ninja```
  4. Add the ```C:\Program Files\ninja``` to your system path.
  5. Close and re-open VSCode

---

## LaTeX setup in VSCode

To setup LaTeX for windows one needs to have TeX Live on Windows to install that use the following link and select a method to install it [TexLive Download](https://tug.org/texlive/windows.html). The installation may take some time depending on your laptop up tho 3h.

In VSCode i recommend following extension from the extension manager with the following Extension-id:
- James-Yu.latex-workshop [To the Extension](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)

After you've setup all this you should see a build or compile symbol in the bottom left corner of your active VSCode session. To have a nice view on your latex project please open the report file and place it on the right hand side of your monitor.

---

## Commit Regeln und Konventionen 
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


