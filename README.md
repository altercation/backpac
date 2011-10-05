backpac
=======

A package state snapshot and restore tool for Arch Linux with config file 
save/restore support. Allows system state to be easily committed to version 
control such as git.

Need
----

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



