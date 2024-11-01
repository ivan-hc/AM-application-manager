# "AM" Application Manager
### Database & solutions for all AppImages and portable apps for GNU/Linux!

<div align="center">

| <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/sandbox.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/list.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/about.gif"> |
| - | - | - |
| *sandbox AppImages* | *list available apps* | *info about the apps* |
| <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/install.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/query.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/files.gif"> |
| *install applications* | *query lists using keywords* | *show the installed apps* |
| <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/backup-overwrite.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/update.gif"> | <img src="https://raw.githubusercontent.com/ivan-hc/AM/main/sample/nolibfuse.gif"> |
| *create and restore snapshots* | *update everything* | *get rid of libfuse2* |

</div>

"AM"/"AppMan" is a set of scripts and modules for installing, updating, and managing AppImage packages and other portable formats, in the same way that APT manages DEBs packages, DNF the RPMs, and so on... using a large database of Shell scripts inspired by the Arch User Repository, each dedicated to an app or set of applications.

"AM"/"AppMan" aims to be the default package manager for all AppImage packages, giving them a home to stay.

You can consult the entire **list of managed apps** at [**portable-linux-apps.github.io/apps**](https://portable-linux-apps.github.io/apps).

------------------------------------------------------------------------
## What does it do?

"AM" easily integrates portable programs and AppImages system-wide (by default) or locally (using the `--user` flag), allows for their updates and management from the command line, integrates them into the application menu and can even assemble them on the fly as an AUR helper.

"AM" is just a tool to provide applications easily and quickly and is only responsible for integrating the AppImages into the system and installing the various programs available, respecting the following order:
1. creation of the base directories and the removal script
2. download of the package
3. creation of the version file and the update script
4. possibly, extraction of the icons and .desktop files

## Disclaim
**"AM" is NOT responsible for the malfunction of individual apps!** This is a problem of those who develop or package them upstream. You can use the command `am -a {PROGRAM}` to view the description and get the sources **to contact the program maintainers**, or `am -d {PROGRAM}` to **download and read the script on your desktop to trace and reach the source safely**.

------------------------------------------------------------------------
# Main index
------------------------------------------------------------------------

[Installation](#installation)

 - [What is AppMan?](#what-is-appman)
 - [AM installation structure](#am-installation-structure)
 - [Uninstall AM](#uninstall-am)
 - [How are apps installed](#how-are-apps-installed)
 - [How to set the path to local apps](#how-to-set-the-path-to-local-apps)
 - [What programs can be installed](#what-programs-can-be-installed)

[How to update all programs, for real!](#how-to-update-all-programs-for-real)
 - [How to update all installed apps](#how-to-update-all-installed-apps)
 - [How to update everything using Topgrade](#how-to-update-everything-using-topgrade)

[Options](#options)

[Guides and tutorials](#guides-and-tutorials)

[Troubleshooting](#troubleshooting)

[Related projects](#related-projects)

------------------------------------------------------------------------
# Installation
To install "AM" you must first install the "core" dependencies from your package manager:
- "`coreutils`" (contains "`cat`", "`chmod`", "`chown`"...);
- "`curl`", to check URLs;
- "`grep`", to check files;
- "`less`", to read the ever-longer lists;
- "`sed`", to edit/adapt installed files;
- "`wget`" to download all programs and update "AM" itself.
- "`sudo`" or "`doas`", for installing and removing programs at the system level.

<details>
  <summary>Additionally, you may need these optional dependencies, click here.</summary>

- "`binutils`", contains a series of basic commands, including "`ar`" which extracts .deb packages;
- "`unzip`", to extract .zip packages;
- "`tar`", to extract .tar* packages;
- "`torsocks`", to connect to the TOR network;
- "`zsync`", required by very few programs and AppImages (although it is mentioned in all installation scripts, it is often disabled because the managed .zsync files are often broken, especially for apps hosted on github.com).

</details>


### Quick installation
Copy/paste the following one line command to download and run the "[AM-INSTALLER](https://github.com/ivan-hc/AM/blob/main/AM-INSTALLER)" script
```
wget -q https://raw.githubusercontent.com/ivan-hc/AM/main/AM-INSTALLER && chmod a+x ./AM-INSTALLER && ./AM-INSTALLER
```
...below, the screenshot of what will appear

Type "1" to install "AM", "2" to install "[AppMan](#what-is-appman)". Any other key will abort the installation.

**Installation is complete!**

Run `am -h` or jump to "**[Usage](#usage)**" to see all the available options.

------------------------------------------------------------------------
### What is AppMan?
AppMan is a portable version of "AM", limited to installing and managing apps only locally and without root privileges.

The command name changes, from `am` to `appman`, but the script is the same.

"AM" on the contrary, provides a "fixed" installation, but can install and manage apps both locally and at the system level.

I recommend "AM" to privileged users who want to install and manage apps at multiple levels, and "AppMan" to non-privileged users who do not have large needs.

------------------------------------------------------------------------
### AM installation structure
The classic "AM" installation has the following structure:
```
/opt/am/APP-MANAGER ==> /usr/local/bin/am
/opt/am/modules
/opt/am/remove
```
Where the command `/usr/local/bin/am` is just a symbolic link to `/opt/am/APP-MANAGER`. The directory `/opt/am/modules` contains the modules "not vital" for "AM" but necessary for managing the apps. The script `/opt/am/remove` is instead necessary for removing "AM".

------------------------------------------------------------------------
### Uninstall AM
To uninstall "AM" simply run this command:
```
sudo /opt/am/remove
```
or from the CLI directly
```
am -R am
```
NOTE, it is recommended to remove apps first using the `-R` option, and then "AM", since locally installed apps may have a different path than `/opt`, see "[How are apps installed](#how-are-apps-installed)", below.

------------------------------------------------------------------------
### How are apps installed
The system-wide AppImage integration has the following structure:
```
/opt/$PROGRAM/
/opt/$PROGRAM/$PROGRAM
/opt/$PROGRAM/AM-updater
/opt/$PROGRAM/remove
/opt/$PROGRAM/icons/$ICON-NAME
/usr/local/bin/$PROGRAM
/usr/local/share/applications/$PROGRAM-AM.desktop
```
Locally installed apps can have a directory of your choice, depending on what you decided when you first started `am -i --user {PROGRAM}` or when you started `appman` (if you chose [AppMan](#what-is-appman)) or by using the `am --user` command.

In fact, the `--user` command can be used as a "flag" for application installation options, allowing you to integrate them locally and without root permissions, as AppMan does.

For example, let's say you want to create and use the `/home/USER/Applications` directory, here is the structure of a locally embedded AppImage:
```
~/Applicazioni/$PROGRAM/
~/Applicazioni/$PROGRAM/$PROGRAM
~/Applicazioni/$PROGRAM/AM-updater
~/Applicazioni/$PROGRAM/remove
~/Applicazioni/$PROGRAM/icons/$ICON-NAME
~/.local/bin/$PROGRAM
~/.local/share/applications/$PROGRAM-AM.desktop
```

------------------------------------------------------------------------
### How to set the path to local apps

The configuration file for the path to locally installed applications is located in `~/.config/appman` and contains the path you specified when you first launched an option to install applications using the `--user` flag, for example:
```
am -i --user {PROGRAM}
```
If you are an AppMan user, you can simply launch any option.

When you first launch it, you will be asked to specify a path to the applications. You can specify any directory or subdirectory you want, **even outside of $HOME**, as long as it is not privileged. Even a USB stick.

NOTE: by modifying the contents of `~/.config/appman`, you will only change the paths for any subsequent operation, while apps and modules stored in the old path will not be manageable. It is recommended to remove the apps first.

------------------------------------------------------------------------
### What programs can be installed
"AM" installs, removes, updates and manages only standalone programs, ie those programs that can be run from a single directory in which they are contained.

1. **PORTABLE PROGRAMS** from official sources (see Firefox, Thunderbird, Blender, NodeJS, Chromium Latest, Platform Tools...), extracted from official .deb/tar/zip packages.
2. **APPIMAGES**, from both official and unofficial sources (I also create unofficial AppImages), or compiled on-the-fly with [pkg2appimage](https://github.com/AppImage/pkg2appimage) and [appimagetool](https://github.com/AppImage/AppImageKit), like an AUR helper, from official archives.
3. **FIREFOX PROFILES** to run as webapps, the ones with suffix "ffwa-" in the apps list.
4. **THIRD-PARTY LIBRARIES** if they are missing in your repositories.

The database aims to be a reference point where you can download all the AppImage packages scattered around the web, otherwise unobtainable, as you would expect from any package manager, through specific installation scripts for each application, as happens with the AUR PKGBUILDs, on Arch Linux. You can see all of them [here](https://github.com/ivan-hc/AM/tree/main/programs), divided by architecture.

You can view basic information, site links and sources using the related command `am -a {PROGRAM}`, or visit [**portable-linux-apps.github.io/apps**](https://portable-linux-apps.github.io/apps).

------------------------------------------------------------------------

| [Back to "Main Index"](#main-index) |
| - |

------------------------------------------------------------------------
## How to update all programs, for real!
Most of the apps managed by "AM" have a script called `AM-updater`. It tells how updates are checked when running the `am -u` command.

In most cases, the "version comparison" is used between the installed one (file `version`) and an online source (official or not, depending on how hard or easy it is to find a download URL or just a number, using the terminal). In other cases, AppImages can rely on "`appimageupdatetool`" if they support "delta updates" (install it with the command `am -i appimageupdatetool`). However, there are some programs that update themselves (and among these the most famous is certainly Firefox, all official development builds).

### How to update all installed apps
Option `-u` or `update` updates all the installed apps and keeps "AM"/"AppMan" in sync with the latest version and all latest bug fixes.

1. To update only the programs, use `am -u --apps` / `appman -u --apps`
2. To update just one program, use `am -u $PROGRAM` / `appman -u $PROGRAM`
3. To update all the programs and "AM"/"AppMan" itself, just run the command`am -u` / `appman -u`
4. To update only "AM"/"AppMan" and the modules use the option `-s` instead, `am -s` / `appman -s`

### How to update everything using Topgrade
Keeping your system up to date usually involves invoking multiple package managers. This results in big, non-portable shell one-liners saved in your shell. To remedy this, Topgrade detects which tools you use and runs the appropriate commands to update them.

Install the "`topgrade`" package using the command
```
am -i topgrade
```
or
```
am -i --user topgrade
```
Visit [github.com/topgrade-rs/topgrade](https://github.com/topgrade-rs/topgrade) to learn more.

------------------------------------------------------------------------

| [Back to "Main Index"](#main-index) |
| - |

------------------------------------------------------------------------
# OPTIONS

<details>
  <summary>Click here to see the full list of options</summary>
  
------------------------------------------------------------------------
## USAGE:

		am {OPTION}
		am {OPTION} {PROGRAM}

------------------------------------------------------------------------
### `about`, `-a`

		am -a {PROGRAM}

**Description**:

Shows more info about one or more apps.

------------------------------------------------------------------------
### `apikey`

		am apikey {Github Token}
		am apikey delete

**Description**:

Accede to github APIs using your personal access tokens. The file named "ghapikey.txt" will be saved in ~/.local/share/AM. Use "`del`" to remove it.

------------------------------------------------------------------------
### `backup`, `-b`

		am -b {PROGRAM}

**Description**:

Create a snapshot of the current version of an installed program.

------------------------------------------------------------------------
### `clean`, `-c`

		am -c

**Description**:

Removes all the unnecessary files and folders.

------------------------------------------------------------------------
### `config`, `-C`, `--config`

		am -C {PROGRAM}

**Description**:

Set a dedicated `$XDD_CONFIG_HOME` for one or more AppImages.

------------------------------------------------------------------------
### `downgrade`, `--rollback`

		am --rollback {PROGRAM}

**Description**:

Download an older or specific app version.

------------------------------------------------------------------------
### `download`, `-d`

		am -d {PROGRAM}
		am -d --convert {PROGRAM}

**Description**:

Download one or more installation scripts to your desktop or convert them to local installers for "AppMan".

------------------------------------------------------------------------
### `extra`, `-e`

		am -e user/project {APPNAME}
		am -e user/project {APPNAME} {KEYWORD}

**Description**:

Install AppImages from github.com, outside the database. This allows you to install, update and manage them all like the others. Where "`user/project`" can be the whole URL to the github repository, give a name to the program so that it can be used from the command line. Optionally, add an "univoque" keyword if multiple AppImages are listed.

NOTE: Since this is an "install" option, you can add the "`--user`" flag (before "`user/project`") to install apps locally. See "`--user`" at the bottom to learn more.

------------------------------------------------------------------------
### `files`, `-f`

		am -f
		am -f --byname
		am -f --less

**Description**:

Shows the list of all installed programs, with sizes. By default apps are sorted by size, use "`--byname`" to sort by name. With the option "`--less`" it shows only the number of installed apps.

**Example**:

https://github.com/user-attachments/assets/31e36845-48e9-4274-8978-ca86b525d797

------------------------------------------------------------------------
### `help`, `-h`

		am -h

**Description**:

Prints this message.

------------------------------------------------------------------------
### `home`, `-H`, `--home`

		am -H {PROGRAM}

**Description**:

Set a dedicated $HOME directory for one or more AppImages.

------------------------------------------------------------------------
### `icons`, `--icons`

		am --icons {PROGRAM}
		am --icons --all

**Description**:

Allow installed apps to use system icon themes. You can specify the name of the apps to change or use the "`--all`" flag to change all of them at once. This will remove the icon path from the .desktop file and add the symbolic link of all available icons in the `~/.local/share/icons/hicolor/scalable/apps` directory.

------------------------------------------------------------------------
### `install`, `-i`

		am -i {PROGRAM}
		am -i --debug {PROGRAM}
		am -i --force-latest {PROGRAM}

**Description**:

Install one or more programs or libraries from the list. With the "`--debug`" option you can see log messages to debug the script. For more details on "`--force-latest`", see the dedicated option, below.

NOTE: Since this is an "install" option, you can add the "`--user`" flag to install apps locally. See "`--user`" at the bottom to learn more.

------------------------------------------------------------------------
### `install-appimage`, `-ia`

		am -ia {PROGRAM}
		am -ia --debug {PROGRAM}
		am -ia --force-latest {PROGRAM}

**Description**:

Same as "install" (see above) but for AppImages only.

------------------------------------------------------------------------
### `lock`

		am lock {PROGRAM}

**Description**:

Prevent an application being updated, if it has an"AM-updater" script.

------------------------------------------------------------------------
### `list`, `-l`

		am -l
		am -l --appimages

**Description**:

Shows the list of all the apps available, or just the AppImages.

------------------------------------------------------------------------
### `newrepo`, `neodb`

		am newrepo add {URL}\{PATH}
		am newrepo select
		am newrepo on\off
		am newrepo purge
		am newrepo info

**Description**:

Set a new default repo, use "`add`" to append the path to a local directory or an online URL, then use "`select`" to use it by default, a message will warn you about the usage of this repo instead of the default one. Use "`on`"/"`off`" to enable/disable it. Use "`purge`" to remove all 3rd party repos. Use "`info`" to see the source from where installation scripts and lists are taken.

------------------------------------------------------------------------
### `nolibfuse`

		am nolibfuse {PROGRAM}

**Description**:

Convert old AppImages and get rid of "libfuse2" dependence.

------------------------------------------------------------------------
### `overwrite`, `-o`

		am -o {PROGRAM}

**Description**:

Overwrite apps with snapshots saved previously (see "-b").

------------------------------------------------------------------------
### `query`, `-q`

		am -q {KEYWORD}
		am -q --appimages {KEYWORD}
		am -q --pkg {PROGRAM1} {PROGRAM2}

**Description**:

Search for keywords in the list of available applications, add the "`--appimages`" option to list only the AppImages or add "`--pkg`" to list multiple programs at once.

------------------------------------------------------------------------
### `remove`, `-r`

		am -r {PROGRAM}

**Description**:

Removes one or more apps, requires confirmation.

------------------------------------------------------------------------
### `-R`

		am -R {PROGRAM}

**Description**:

Removes one or more apps without asking.

------------------------------------------------------------------------
### `sandbox`, `--sandbox`

		am sandbox {PROGRAM}

**Description**:

Run an AppImage in a sandbox using Aisap.

------------------------------------------------------------------------
### `sync`, `-s`

		am -s

**Description**:

Updates this script to the latest version hosted.

------------------------------------------------------------------------
### `template`, `-t`

		am -t {PROGRAM}

**Description**:

Generate a custom installation script.

------------------------------------------------------------------------
### `unlock`

		am unlock {PROGRAM}

**Description**:

Unlock updates for the selected program (nulls "`lock`").

------------------------------------------------------------------------
### `update`, `-u`, `-U`

		am -u
		am -u --apps
		am -u {PROGRAM}

**Description**:

Update everything. Add "`--apps`" to update only the apps or write only the apps you want to update by adding their names.

**Example:**

https://github.com/user-attachments/assets/662d8eb2-38d7-45b8-9530-061189e6ed85

------------------------------------------------------------------------
### `version`, `-v`

		am -v

**Description**:

Shows the version.

------------------------------------------------------------------------
### `--devmode-disable`

		am --devmode-disable

**Description**:

Undo "`--devmode-enable`" (see below).

------------------------------------------------------------------------
### `--devmode-enable`

		am --devmode-enable

**Description**:

Use the development branch (at your own risk).

------------------------------------------------------------------------
### `--disable-sandbox`

		am --disable-sandbox {PROGRAM}

**Description**:

Disable the sandbox for the selected app.

------------------------------------------------------------------------
### `--force-latest`

		am --force-latest {PROGRAM}

**Description**:

Downgrades an installed app from pre-release to "latest".

------------------------------------------------------------------------
### `--launcher`

		am --launcher /path/to/${APPIMAGE}

**Description**:

Drag/drop one or more AppImages in the terminal and embed them in the apps menu and customize a command to use from the CLI.

------------------------------------------------------------------------
### `--system`

		am --system

**Description**:

Switch "AM" back to "AM" from "AppMan Mode" (see "`--user`").

------------------------------------------------------------------------
### `--user`, `appman`

		am --user

**Description**:

Made "AM" run in "AppMan Mode", locally, useful for unprivileged users. This option only works with "AM".

The "`--user`" option can also be used just as a "flag" for installation options. For example:

- Use it to install applications locally, option "`-i`" or "`install`":

		am -i --user {PROGRAM}

- Also suboptions of "`-i`" can work with this flag:

		am -i --user --debug {PROGRAM}
		am -i --user --force-latest {PROGRAM}
		am -i --user --debug --force-latest {PROGRAM}

- Same for AppImages only, option "`-ia`" or "`install-appimage`":

		am -ia --user --user {PROGRAM}
		am -ia --user --debug {PROGRAM}
		am -ia --user --force-latest {PROGRAM}
		am -ia --user --debug --force-latest {PROGRAM}

- External AppImages can be installed like this as well, option "`-e`" or "`extra`":

		am -e --user user/project {APPNAME}
		am -e --user user/project {APPNAME} {KEYWORD}

NOTE, "AM" 9 or higher is also able to, update and manage apps locally, by default, and without having to switch to "AppMan Mode".

</details>

------------------------------------------------------------------------

| [Back to "Main Index"](#main-index) |
| - |

------------------------------------------------------------------------
# Guides and tutorials
Below you can access the documentation pages related to the use of "AM", complete with tutorials and demonstration videos, depending on the topic you are interested in:

------------------------------------------------------------------------
- [Install applications](#install-applications)
- [Install only AppImages](#install-only-appimages)
- [Install AppImages not listed in this database but available in other github repos](#install-appimages-not-listed-in-this-database-but-available-in-other-github-repos)
- [List the installed applications](#list-the-installed-applications)
- [List and query all the applications available on the database](#list-and-query-all-the-applications-available-on-the-database)
- [Update all](#update-all)
- [Backup and restore installed apps using snapshots](#backup-and-restore-installed-apps-using-snapshots)
- [Remove one or more applications](#remove-one-or-more-applications)
- [Convert Type2 AppImages requiring libfuse2 to New Generation AppImages](#convert-type2-appimages-requiring-libfuse2-to-new-generation-appimages)
- [Integrate local AppImages into the menu by dragging and dropping them](#integrate-local-appimages-into-the-menu-by-dragging-and-dropping-them)
  - [How to create a launcher for a local AppImage](#how-to-create-a-launcher-for-a-local-appimage)
  - [How to remove the orphan launchers](#how-to-remove-the-orphan-launchers)
  - [AppImages from external media](#appimages-from-external-media)
- [How to use "AM" in non-privileged mode, like "AppMan"](#how-to-use-am-in-non-privileged-mode-like-appman)
- [Sandbox an AppImage](#sandbox-an-appimage)
  - [How to enable a sandbox](#how-to-enable-a-sandbox)
  - [How to disable a sandbox](#how-to-disable-a-sandbox)
  - [Sandboxing example](#sandboxing-example)
  - [About Aisap sandboxing](#about-aisap-sandboxing)
- [How to enable bash completion](#how-to-enable-bash-completion)
- [How to update or remove apps manually](#how-to-update-or-remove-apps-manually)
- [Downgrade an installed app to a previous version](#downgrade-an-installed-app-to-a-previous-version)
- [How to use multiple versions of the same application](#how-to-use-multiple-versions-of-the-same-application)
- [Create and test your own installation script](#create-and-test-your-own-installation-script)
  - [Option Zero: "AppImages"](#option-zero-appimages)
  - [Option One: "build AppImages on-the-fly"](#option-one-build-appimages-on-the-fly)
  - [Option Two: "Archives and other programs"](#option-two-archives-and-other-programs)
  - [Option Three: "Firefox profiles"](#option-three-firefox-profiles)
  - [How an installation script works](#how-an-installation-script-works)
  - [How to test an installation script](#how-to-test-an-installation-script)
- [Third-party databases for applications (NeoDB)](#third-party-databases-for-applications-neodb)

------------------------------------------------------------------------

| [Back to "Main Index"](#main-index) |
| - |

------------------------------------------------------------------------
# Troubleshooting
Below you can access documentation pages for common issues and frequently asked questions:

------------------------------------------------------------------------
- [An application does not work, is old and unsupported](#an-application-does-not-work-is-old-and-unsupported)
- [Cannot download or update an application](#cannot-download-or-update-an-application)
- [Cannot mount and run AppImages](#cannot-mount-and-run-appimages)
- [Failed to open squashfs image](#failed-to-open-squashfs-image)
- [Spyware, malware and dangerous software](#spyware-malware-and-dangerous-software)
- [Stop AppImage prompt to create its own launcher, desktop integration and doubled launchers](#stop-appimage-prompt-to-create-its-own-launcher-desktop-integration-and-doubled-launchers)
- [The script points to "releases" instead of downloading the latest stable](#the-script-points-to-releases-instead-of-downloading-the-latest-stable)
- [Ubuntu mess](#ubuntu-mess)
- [Wrong download link](#wrong-download-link)

------------------------------------------------------------------------

| [Back to "Main Index"](#main-index) |
| - |

------------------------------------------------------------------------
# Related projects
#### External tools and forks used in this project
- *[aisap](https://github.com/mgord9518/aisap), sandboxing solutions for AppImages*
- *[appimagetool](https://github.com/AppImage/appimagetool), get rid of libfuse2 from your AppImages*
- *[pkg2appimage](https://github.com/AppImage/pkg2appimage), create AppImages on the fly from existing .deb packages*
- *[repology](https://github.com/repology), the encyclopedia of all software versions*

#### My other projects
- *[AppImaGen](https://github.com/ivan-hc/AppImaGen), easily create AppImages from Ubuntu PPAs or Debian using pkg2appimage and appimagetool*
- *[ArchImage](https://github.com/ivan-hc/ArchImage), create AppImages for all distributions using Arch Linux packages. Powered by JuNest*
- *[Firefox for Linux scripts](https://github.com/ivan-hc/Firefox-for-Linux-scripts), easily install the official releases of Firefox for Linux*
- *[My AppImage packages](https://github.com/ivan-hc#my-appimage-packages) the complete list of packages managed by me and available in this database*
- *[Snap2AppImage](https://github.com/ivan-hc/Snap2AppImage), try to convert Snap packages to AppImages*

------------------------------------------------------------------------

###### *You can support me and my work on [**ko-fi.com**](https://ko-fi.com/IvanAlexHC) and [**PayPal.me**](https://paypal.me/IvanAlexHC). Thank you!*

--------

*© 2020-present Ivan Alessandro Sala aka 'Ivan-HC'* - I'm here just for fun! 

------------------------------------------------------------------------

| [**ko-fi.com**](https://ko-fi.com/IvanAlexHC) | [**PayPal.me**](https://paypal.me/IvanAlexHC) | [Install "AM"/"AppMan"](#installation) | ["Main Index"](#main-index) |
| - | - | - | - |

------------------------------------------------------------------------
