backpac
=======

A package state snapshot and restore tool for Arch Linux with config file 
save/restore support. Allows system state to be easily committed to version 
control such as git.

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

### Usage

Run `backpac` on the command line with no options to display basic usage 
information.

### BACKPAC OPTIONS SUMMARY:

`-b`    Backups ON; Files will be saved in place with backup suffix.
`-f`    Force mode OFF; Prompt before file write operations.
`-F`    Full Force mode OFF; Prompt displayed before script runs.
`-g`    Suppress group check OFF; Groups will be checked for currency.
`-h`    Display option and usage summary.
`-p`    Default backpac: /home/es/.config/backpac/tau.
`-R`    Simple Report OFF; Run with this ON to report with no system changes.
`-S`    System update OFF; No system files will be updated.
`-U`    backpac config update OFF; backpac files will not be updated.

### USAGE SUMMARY:
`backpac -R`     Show a report; no changes made to system (try this first!)
`backpac -U`     Create a new or update existing backpac config
`backpac -S`     Update live system to match current backpac config

Adding `-f` will skip normal prompts prior to write operations.
Adding `-F` will skip the initial prompt as well as all subsequent prompts.

### FIRST RUN / AUTOMATIC UPDATE:
`backpac -Uf`     Automatically create a backpac config from system state.

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


