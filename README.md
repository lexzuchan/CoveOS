# Cove Desktop

Cove is a macOS-inspired desktop for Windows 11 x64. It opens as a workspace at sign-in on a Cove installation. Windows Explorer, the Windows taskbar, and Windows Settings remain available. Use the Cove menu to minimize the workspace or quit.

- Floating dock, top menu bar, Start menu shortcut search, and open-window switching.
- Dark and light appearances, four accents, three wallpapers, and adjustable dock size and shortcuts.
- Cove Browser uses Chromium through Qt WebEngine. It supports tabs, bookmarks, downloads, and a dedicated browser profile.

## Try it on Windows

Install x64 Python 3.13 and run these commands in PowerShell from the repository root.

~~~powershell
python -m pip install -r requirements.txt
python main.py
~~~

Use **Ctrl+Space** to search apps, **Ctrl+Shift+Space** to switch windows, and **F11** for full screen while Cove is focused. Use **Appearance** to customize the workspace. In Cove Browser, **Ctrl+L** selects the address bar, **Ctrl+T** opens a tab, and **Ctrl+D** saves a bookmark.

## Build a VM ISO

On a Windows x64 build machine, install the **Deployment Tools** component of the [Windows ADK](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install). Download an unmodified [Windows 11 x64 ISO](https://www.microsoft.com/software-download/windows11) from Microsoft. Allow at least 25 GB of free space for the staged media, the Cove bundle, and the finished ISO.

~~~powershell
python -m pip install -r requirements.txt
.\scripts\Build-App.ps1
.\scripts\Build-Iso.ps1 -BaseIso 'C:\ISO\Windows11-x64.iso'
~~~

The default output is **dist/Cove-Windows11-x64.iso**. The script prints its SHA-256 checksum. It keeps standard Windows Setup and adds Cove through Setup's OEM file layout and specialize pass. Choose the Windows edition and target virtual disk in Setup as usual. Windows licensing and activation use your normal Windows license.

Create a Windows 11 x64 VM with UEFI, Secure Boot and a virtual TPM 2.0. In Hyper-V choose **Generation 2** and the **Microsoft Windows** Secure Boot template. Allocate at least 2 vCPUs, 4 GB RAM and a 64 GB virtual disk. Attach the Cove ISO to the virtual DVD drive, boot it and install to the VM's virtual disk. After setup and sign-in, Cove should open automatically. The Start menu entry is **Cove Desktop**. The startup shortcut can be disabled from Windows Startup Apps or removed via the common Startup folder.

## Build from GitHub Actions

1. Push this project to a **private** GitHub repository.
2. Register a trusted, dedicated Windows x64 self-hosted runner with the extra label **cove-iso**. Install PowerShell 7 and the ADK Deployment Tools there.
3. Put the official Windows ISO on the runner and set the repository variable **COVE_BASE_ISO** to its absolute path. The runner account needs permission to read and mount the ISO.
4. In **Actions → Build Cove ISO**, choose **Run workflow**. The job summary gives the finished ISO path on the runner. The workflow keeps the image on the runner so Windows installation media stays out of GitHub artifacts.

## Verify in the guest

- Complete standard Windows Setup and check that Cove opens at sign-in.
- Search for a Start menu app and open it. Switch back to it via **Windows**.
- Change the theme, accent and wallpaper, then close and reopen Cove to check persistence.
- Open Cove Browser, load an HTTPS site, open a second tab, and save a bookmark.

Cove is a first build. Browser engine updates are picked up by updating the Qt WebEngine dependencies and rebuilding.
