![ArchJourneyHeader.png](ArchJourneyHeader.png)
# About This ArchLinux Installation

Let me preface this with I have used Windows as my daily driver personally for many years
and macOS professionally for almost as many years. I'm comfortable with the terminal, 
and have no issues navigating or executing commands within it. However, that being said
I wanted to have a system that I could have customized to my explicit needs with as little
bloat on it that was possible. Another goal for this was it would have to look nice and 
flow together really well. I've experimented with distros such as Ubuntu, Fedora, Debian,
PopOS, Linux Mint, etc. And while they all did the things well I always found something
missing from them. Gnome just was too bloated, and KDE was too rough. 

So after dealing with the headache that is WSL2 within Windows and trying to develop I 
finally turned a corner and said... screw it. So I began this adventure down the path of 
Arch Linux and taking all the responsibility for my system.

## Installing Arch

Things got a little chaotic here, all in all I only installed Arch three times before I
got it "right". The first time to see if I could do it at all, on a virtual machine in
Windows 11. 
I followed the [Arch Installation Guide](https://wiki.archlinux.org/title/Installation_guide)
basically to the letter on this one. Once this was completed I decided to fully take the
plunge and backed up all the necessary files (or at least those I cared about) from
my documents on Windows and wiped the hard drives completely.

Now many would think this was the point of no return however it really was not. I could
easily re-install Windows 11 and be back to where I was within a day. So I took some time
and decided to browse YouTube and some of my favorite Tech YouTubers channels (S.O. to 
ThePrimeagen, Theo - t3.gg, and DreamsOfAutonomy). And while perusing the YouTube I came 
across a guide on installing Arch that made sense and had some of the features that I
wanted. So for the second installation I used DreamsOfAutonomy's 
[Arch From Scratch](https://github.com/dreamsofautonomy/arch-from-scratch)
Guide, being as I had several drives in the system and also wanted encryption this worked
out great.

This worked out, however it had one big caveat that I did not like. It used Gnome and GDM
as the desktop environment. So while I got comfortable with Arch for a few days, I started
researching other environments and came across the hot new environment;
[Hyprland](https://hyprland.org/). I had used PopOS years ago on an old laptop and loved
the idea of having a tiling window manager again. So using the Arch From Scratch guide as
a base, I derived my [Installation Steps](Installation%20Steps.md) and started going down 
the absolute rabbit hole that was. Completing the third installation I was left with the
blinking cursor of the login screen of ArchLinux, ready to start a new adventure.

## Hyprland & Customizing My System

Now before I did ***ANYTHING*** to this system I had one very important piece of software
to install, mainly to save my own sanity; Timeshift. This is a utility that can be used
to make a backup of the essential system at a point in time, and restore it from there
should anything go wrong. Knowing the chaos that can be Arch, I wanted a way to revert
any and all changes should I need to. So I made my first backup and went on my merry way.




