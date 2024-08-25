# Software Installed On System

## Base Software for Usage
This is the absolute bare minimum to run the setup, this has no configuration and most of the features
will not work out of the box. This setup is for use with hyprland and the wayland desktop system. However,
my system is not customized to oblivion. I made this to support my development workflow. Timeshift is absolutely
essential to keep things working if Arch decides to bork itself.
1. Timeshift
2. Git 
3. Paru
4. Hyprland
5. SDDM & SDDM-kcm
6. Mako
7. Pipewire, Pipewire Pulse & Wireplumber
8. Pulse Audio Control
9. XDG-Desktop Portal & XDG Desktop Portal GTK
10. Polkit KDE Agent
11. QT5 Wayland & QT6 Wayland
12. Waybar
13. Cava
14. Hyprpaper
15. Rofi
16. Clipse 
17. CPIO, Meson, CMake 
18. Hyprlock 
19. Nautilus 
20. Zen Browser
21. KDE Plasma (This is mainly for SDDM)
22. Kitty

**Installation Command**
```zsh
# Installing Git and Paru
sudo pacman -S git;
git clone https://aur.archlinux.org/paru.git;
cd paru;
makepkg -si;

# Using Paru to Install Everything Else
paru -S timeshift hyprland sddm sddm-kcm mako pipewire pipewire-pulse wireplumber pavucontrol \
xdg-desktop-portal-hyprland xdg-desktop-portal-gtk polkit-kde-agent qt5-wayland qt6-wayland \
qt5-quickcontrols plasma-meta plasma-framework5 plasma-desktop waybar-cava hyprpaper rofi clipse \
cpio meson cmake hyprlock nautilus zen-browser kitty \;
```

## Additional System Tools
1. man
2. wget
3. wayclip
4. tree

**Installation Script**
```zsh
paru -S man wget wayclip tree;
```

# ZSH Plugins
### zsh-syntax-highlighting
```zsh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
```

### Starship
```zsh
curl -sS https://starship.rs/install.sh | sh
```

## Development Tools
### VMWare Workstation
The first six items on this list are dependencies for VMWare Workstation as noted in the Arch Wiki.
1. fuse2
2. gtkmm
3. linux-headers
4. ncurses
5. libcanberra
6. pcsclite
7. vmware-workstation

**Installation Script**
```zsh
pacman -S fuse2 gtkmm linux-headers ncurses libcanberra pcsclite;
paru -S vmware-worksation;
```

### Docker Desktop
```zsh
paru -S docker-desktop docker-compose;
```

### JetBrains Toolbox
```zsh
paru -S jetbrains-toolbox;
```
