
Debian
====================
This directory contains files used to package starshipshad/starshipsha-qt
for Debian-based Linux systems. If you compile starshipshad/starshipsha-qt yourself, there are some useful files here.

## starshipsha: URI support ##


starshipsha-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install starshipsha-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your starshipsha-qt binary to `/usr/bin`
and the `../../share/pixmaps/starshipsha128.png` to `/usr/share/pixmaps`

starshipsha-qt.protocol (KDE)

