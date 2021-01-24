---
layout: post
title:  "Installing XMonad on Arch Linux"
tags:   xmonad arch linux tiling window manager haskell cabal
---

# Atr
## [goto HOMEPAGE;](https://arthurbacci64.github.io/)

-

## How to install XMonad in Arch Linux

Hello, in this tutorial I will demonstrate how you can install XMonad in Arch Linux or most of the Arch-based distributions, including, but not limited to: Parabola GNU/Linux, Arch Linux, Manjaro and Artix.

<!--more-->

1. Install ghcup:

	```			
	pacman -S curl g++ gcc gmp make ncurses realpath xz-utils
	curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
	```

	Press `ENTER`,  `ENTER`,  `NO` and  `YES`

2. Update Cabal and install xmonad:

	```
	cabal update
	cabal install xmonad
	cabal install --lib xmonad
	```

3. (Optional) Install `xmonad-contrib` and `xmonad-extras`

	`xmonad-contrib` includes some non-official packages made by the community.
	
	`xmonad-extras` includes some non-official packages made by the community that are not in `xmonad-contrib` because it has a lot of "heavy" dependencies. Usually you will not need this, but nothing bad will happen if you install.

4. There are two ways

	If you want to use a display manager, then jump the fiveth step, else if you want to login from terminal and start the graphical session with `startx`, then jump the sixth step.
	
5. Adding an xsession for XMonad

	Open **as root**, the file `/usr/share/xsessions/xmonad.desktop`(It probally will not exist, create it).
	
	Write the following in it:
	
	```
	[Desktop Entry]
	Name=XMonad
	Comment=Log in using XMonad
	Exec=/home/you/.cabal/bin/xmonad
	TryExec=/home/you/.cabal/bin/xmonad
	Type=Application
	```
	
	Remember to change `/home/you/` to the path of your home, if you do not know it, do: `echo $HOME`.
	
	
6. Installing `xinit` and making a `~/xinitrc`

	Install the `xorg-xinit` package:

	```
	pacman -S xorg-xinit
	```
	
	Now create a file in your home called `xinitrc`
	
	```
	touch ~/.xinitrc
	```
	
	And put this content on it:
	
	```
	echo "exec xmonad" > ~/.xinitrc
	```
	
	Now, you will need to disable your display manager.
	
	If you are using systemd:
	
	```
	systemctl disable displaymanagername
	```
	
	Replace `displaymanagername` to the name of your display manager, it can be `sddm`, `lightdm`, `gdm`...
	
	If you are not using systemd, you probally is a advanced user and knows how to do the things. Does not you?
	
7. It is done!

	Reboot your computer.
	
	If you are using a display manager, it probally will have a place where you can change the selected Window Manager or Desktop Enviroment, select `xmonad` and login.
	
	If you are using `xinit` / `startx` (my favourite way) then, login and type `startx` in the command line that will appear.
	
	
If you had any problem with the installation, please contact me, my e-mail is `arthurbacci@protonmail.com`.
	
