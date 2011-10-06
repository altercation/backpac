backpac
=======

A package state snapshot and restore tool for Arch Linux with config file 
save/restore support. Allows system state to be easily committed to version 
control such as git.

Quickstart
----------

* Install from AUR: https://aur.archlinux.org/packages.php?ID=52957
  (see install below).
* Automatically generate first config with: `backpac -Uf`
* View a report: `backpac -Q` or `backpac -Qf` to skip prompts

Why?
----

There are a lot of 'big-iron' solutions to maintaining, backing up and
restoring system state. Setting these up for a single system or a handful of
personal systems has always seemed like overkill.

There are also some existing pacman list making utilities around, but most of
them seem to list either all packages or don't separate the official and aur
packages the way I wanted. Some detect group install state, some don't. I 
wanted all these features in backpac.

Finally, whatever tool I use, I'd like it to be simple (c.f. the Arch Way).
Lists that are produced should be human readable, human maintainable and not
different from what I'm using in non-automated form. Backpac fulfills these
requirements.

Regarding files, I wanted to be able to backup arbitrary system files to a 
git repository. Tools like etckeeper are interesting but non /etc files in 
that case aren't backed up (without some link trickery) and there isn't any 
automatic integration with pacman, so there is no current advantage to using 
a tool like that. I also like making an explicit list of files to snapshot.

Bash was selected as backpac needed to have very minimal requirements and 
work on fresh systems.

Details
-------

It's a common method of setting up a single system: take some notes about what 
packages you've installed, what files you've modified.

Backpac creates those notes for you, specifically:

 * a list of installed groups (based on 80% of group packages being installed)
 * a list of packages (including official and aur packages, listed separately)
 * a list of files (manually created) that is automatically archived

Backpac can also use these lists to install packages and files. Essentially, 
then, backpac takes a snapshot of your system and can recreate that state from 
the files and lists it archives.

Backpac is a very, very lightweight way of saving and restoring system state.  
It's not intended for rolling out and maintaining multiple similar systems, 
it's designed to assist individual users in the  maintainance of their own Arch 
Linux box.

Packages
--------

It's trivial to dump out a list of installed packages using pacman, so what 
value does backpac provide?

 * It saves a list of *only* packages that have been explicitly installed (not 
   dependencies), so the list is representative of the way you've set up your 
   system.

 * It keeps lists of both official repository packages and AUR packages, but 
   keeps them separate

 * It's easy to create a list of packages that backpac can use if you want to 
   make one manually. A simple text file with package names. Like many scripts, 
   lines starting with # are ignored as comments. Order is unimportants, 
   official repository packages and AUR can be interspersed.

 * It scans for installed groups such as 'base' and 'base-devel' and disregards 
   those (it does list them in the backpac report output for information 
   purposes). Additionally it will let you know if there are packages that have 
   been added or removed from those groups in order to keep your system 
   current.

Here's a sample packages file created by backpac:

    # PACKAGES added by backpac on 2011-10-04
    # -------------------------------------------------------------------------
    acpi acpid acpitool aif alsa-utils augeas cowsay cpufrequtils curl dialog
    firefox gamin git ifplugd iw mesa mesa-demos netcfg openssh rfkill rsync
    rxvt-unicode sudo terminus-font vim wpa_actiond wpa_supplicant_gui xmobar
    xorg-server-utils xorg-twm xorg-utils xorg-xclock xorg-xinit xterm yacpi
    yajl youtube-dl zsh

    # AUR PACKAGES added by backpac on 2011-10-04
    # -------------------------------------------------------------------------
    flashplugin-beta freetype2-git-infinality git-annex haskell-json
    package-query-git packer wpa_auto xmonad-contrib-darcs xmonad-darcs

This list could be created by hand as well and pacman would add to it as you 
add packages to your system:

    # my editing apps
    vim gimp

    # x stuff
    xorg xmonad xmonad-contrib

    # audio
    alsa-utils

    # AUR PACKAGES added by backpac on 2011-10-04
    # -------------------------------------------------------------------------
    flashplugin-beta freetype2-git-infinality git-annex haskell-json

    # AUR PACKAGES REMOVED by backpac on 2011-10-04
    # -------------------------------------------------------------------------
    git-annex

(this is just an example as you can tell by the fictitous installation of the 
*entire* xorg package. don't do this. also, git-annex is neat and I wouldn't 
remove it)

Here's a sample groups list created by backpac:

    # GROUPS added by backpac on 2011-10-04
    # -------------------------------------------------------------------------
    base base-devel

Files
-----

Create a list of files names "files" in your backpac config directory and 
backpac will copy those files from your system to an archived file "snapshot" 
directory. This can be, for example, committed to a git repository and easily 
reused or rolled back.

(run backpac without any command line arguments for information and path to the 
default config directory).

Here's a sample initial `files` list created by hand:

    /etc/acpi/handler.sh
    /boot/grub/menu.lst
    /etc/rc.conf
    /etc/rc.local

And here's what happens after updating it with backpac (`backpac -U`). Backpac 
first prompts you for a decision with each file (you could skip this with -f 
for automatic updating). In future runs of backpac, files that exist already 
will be backed up prior to the new file being written unless you use the -b (no 
backups) option.

    COPY MISSING FILES?
    --------------------------------------------------------------------------------
    The following files haven't been saved to your backpac config yet. Please
    choose whether to copy each item to the config files directory (backups will
    be made):

    /etc/acpi/handler.sh
    Copy file? (y/n==this) (Y/N==all) y
    (copied; attributes set from system file)

    /boot/grub/menu.lst
    Copy file? (y/n==this) (Y/N==all) Y
    (copied; attributes set from system file)
    (copied; attributes set from system file)
    (copied; attributes set from system file)

The `files` contents now look like this:

    /boot/grub/menu.lst 644 root root
    /etc/acpi/handler.sh 755 root root
    /etc/rc.conf 644 root root
    /etc/rc.local 755 root root

Usage
-----

Run `backpac` on the command line with no options to display basic usage 
information.

### BACKPAC OPTIONS SUMMARY:

    -a     Skip pAckages; Groups and packages will not be processed.
    -b     Turn off backups when copying new files to an existing path.
    -F     DANGER: Force mode with automatic execution, NO-INITIAL PROMPT.
    -f     Force all write operations; no prompts will be given.
    -g     Suppress group currency check; Skip checking current group packages.
    -h     Display option and usage summary.
    -i     Skip fIles; Files will not be processed.
    -p     Specify full path to a custom backpac config directory.
    -Q     SAFELY queries & reports system/backpac config state; no changes.
    -R     DANGER: Auto-Remove; Remove/Uninstall actions default to YES.
    -S     Update LIVE system files/packages from backpac config.
    -U     Update files in backpac config directory.

### USAGE SUMMARY:

    backpac -Q     Show a report; no changes made to system (try this first!)
    backpac -U     Create a new or update existing backpac config
    backpac -S     Update live system to match current backpac config

    Adding -f will skip normal prompts prior to write operations.
    Adding -F will skip the initial prompt as well as all subsequent prompts.

### FIRST RUN / AUTOMATIC UPDATE:

    backpac -Uf     Automatically create a backpac config from system state.

Config
------

The backpac config is a directory containing the packages, groups, and files 
lists as well as the files archive directory (files.d). By default it is 
located at:

    /home/USERNAME/.config/backpac/HOSTNAME/

The specific location is dependent on the XDG config directory location on your 
system, but it is by default ~/.config.

This structure allows you to keep your entire backpac config directory under 
version control and have backpac configs for each of your machines under 
version control:

    /home/USERNAME/.config/backpac/
                                |
                                +-my-desktop
                                |
                                +-my-laptop
                                |
                                +-test-machine
                                |
                                +-a-server

The default location of the backpac config directory can be overridden by 
exporting an environment variable named `BACKPAC_CONFIG` or can be set on the 
command line using the -p <pathname> option.

Installation
------------

AUR Package: https://aur.archlinux.org/packages.php?ID=52957

Force
-----

Using the `-f` option skips interactive prompts, which are otherwise shown 
prior to any 'write' operation.

Using the `-F` option will additionally skip the initial prompt, useful for
scripts or cron.

AUTOMATED Examples
------------------

### No-changes, just a query report

    backpac -Qf

Both of the following are the "extreme" cases of fully automated backpac config 
update and system state updates.

### Full backpac snapshot of the system

    backpac -UFRb

A full snapshot of system state to your default backpac config directory. This 
will create a "perfect" snapshot including removing the names of packages that 
aren't installed currently (but which were previously listed in your backpac 
config). Files will also be updated from the system state (no backups made of 
current snapshot files due to the -b flag, but in this case I am going to 
commit to a git repository so I don't want them). Option details:

    -U  Update backpac config
    -F  Skip initial prompt and take default action for all changes
    -R  Sets removes (package names in this case) to default to YES
    -b  No backups (we'll use git instead)

### Full overwrite of system to conform to backpac state

    backpac -SFRb

A full restore of your backpac state to the live system. This includes the 
*uninstallation* of packages that aren't listed in the backpac lists. It also 
overwrites system files with the backpac versions *with no backup*.

    -S  System state update from backpac
    -F  Skip initial prompt and take default action for all changes
    -R  Sets removes (uninstalls in this case) to default to YES
    -b  No backups (danger! don't use this if you want to backup current files)

Sample Output
-------------

    $ backpac -Qf

    backpac
    ----------------------------------------------------------------------------
    (-a)    Skip pAckages OFF; Groups and packages will be processed normally.
    (-b)    Backups ON; Files will be saved in place with backup suffix.
     -f     Force mode ON; No prompts presented (CAUTION).
    (-F)    Full Force mode OFF; Prompt displayed before script runs.
    (-g)    Suppress group check OFF; Groups will be checked for currency.
    (-h)    Display option and usage summary.
    (-i)    Skip fIles OFF; Files will be processed normally.
    (-p)    Default backpac: /home/es/.config/backpac/tau.
     -Q     Simple Query ON; Report shown; no changes made to system.
    (-R)    Auto-Remove OFF; Remove/Uninstall action default to NO.
    (-S)    System update OFF; No system files will be updated.
    (-U)    backpac config update OFF; backpac files will not be updated.

    Sourcing from backpac config directory: /home/es/.config/backpac/tau

    Initializing.................Done


    GROUPS
    ============================================================================
    /home/es/.config/backpac/tau/groups

    GROUPS UP TO DATE: group listed in backpac and >80% local install:
    ----------------------------------------------------------------------------
    base base-devel xfce4 xorg xorg-apps xorg-drivers xorg-fonts

    GROUP PACKAGES; MISSING?: group member packages not installed:
    ----------------------------------------------------------------------------
    (base: nano)
    (xfce4: thunar xfdesktop)


    PACKAGES
    ============================================================================
    /home/es/.config/backpac/tau/packages

    PACKAGES UP TO DATE: packages listed in backpac also installed on system:
    ----------------------------------------------------------------------------
    acpi acpid acpitool aif alsa-utils augeas cowsay cpufrequtils curl dialog
    firefox gamin git ifplugd iw mesa mesa-demos mutt netcfg openssh rfkill
    rsync rxvt-unicode sudo terminus-font vim wpa_actiond wpa_supplicant_gui
    xmobar xorg-server-utils xorg-twm xorg-utils xorg-xclock xorg-xinit xterm
    yacpi yajl youtube-dl zsh

    AUR UP TO DATE: aur packages listed in backpac also installed on system:
    ----------------------------------------------------------------------------
    flashplugin-beta freetype2-git-infinality git-annex haskell-json
    package-query-git packer wpa_auto xmonad-contrib-darcs xmonad-darcs

    AUR NOT IN backpac: installed aur packages not listed in backpac config:
    ----------------------------------------------------------------------------
    yaourt-git


    FILES
    ============================================================================
    /home/es/.config/backpac/tau/files

    MATCHES ON SYSTEM/CONFIG:
    ----------------------------------------------------------------------------
    /boot/grub/menu.lst
    /etc/acpi/handler.sh
    /etc/rc.conf
    /etc/rc.local

