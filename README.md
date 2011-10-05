backpac
=======

A package state snapshot and restore tool for Arch Linux with config file 
save/restore support. Allows system state to be easily committed to version 
control such as git.

Quickstart
----------

* Install from AUR (see install below).
* Automatically generate first config with: `backpac -Uf`
* View a report: `backpac -R` or `backpac -Rf` to skip prompts

Why?
----

I wanted something simple to set up my Arch Linux machines, save state and 
restore it. Lists, pacman, file backup, git. Backpac just glues some of these 
pieces together.

It needed to have very minimal requirements and work on fresh systems. Bash was 
the obvious choice.

Details
-------

It's a common method of setting up a single system: take some notes about what 
packages you've installed, what files you've modified.

Backpac creates those notes for you, specifically:

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

Usage
-----

Run `backpac` on the command line with no options to display basic usage 
information.

### BACKPAC OPTIONS SUMMARY:

    -b     Turn off backups when copying new files to an existing path.
    -F     DANGER: Force mode with automatic execution, NO-INITIAL PROMPT.
    -f     Force all write operations; no prompts will be given.
    -g     Suppress group currency check; Skip checking current group packages.
    -h     Display option and usage summary.
    -p     Specify full path to a custom backpac config directory.
    -R     SAFELY Reports system and backpac config state; no changes made.
    -S     Update LIVE system files/packages from backpac config.
    -U     Update files in backpac config directory.

### USAGE SUMMARY:

    backpac -R     Show a report; no changes made to system (try this first!)
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

AUR

Force
-----

Using the `-f` option skips interactive prompts, which are otherwise shown 
prior to any 'write' operation.

Using the `-F` option will additionally skip the initial prompt, useful for
scripts or cron.

AUTOMATED Examples
------------------

Both of these are the "extreme" cases of fully automated backpac config update 
and system state updates.

### TAKE NO PRISONERS: Full backpac snapshot of the system

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


### WE ARE SPARTA: Full overwrite of system to conform to backpac state

    backpac -SFRb

A full restore of your backpac state to the live system. This includes the 
*uninstallation* of packages that aren't listed in the backpac lists. It also 
overwrites system files with the backpac versions *with no backup*.

    -S  System state update from backpac
    -F  Skip initial prompt and take default action for all changes
    -R  Sets removes (uninstalls in this case) to default to YES
    -b  No backups (danger! don't use this if you want to backup current files)
