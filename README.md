What is this?
=============

A command-line frontend for [clevo-xsm-wmi](https://bitbucket.org/tuxedocomputers/clevo-xsm-wmi), the kernel module for controlling the backlight on Clevo laptops. In simpler terms, it lets you switch the backlight on and off, and set the colors on RGB keyboards. I created this on an Ubuntu-based Linux distro and can't guarantee it will work on other distros.

Dependencies
============
- `clevo-xsm-wmi`
	1. [Download it here](https://bitbucket.org/tuxedocomputers/clevo-xsm-wmi)
	2. Install dependencies: `sudo apt install gcc make linux-headers-generic`
	3. Open a terminal, enter the clevo-xsm-wmi directory and run `cd module && make && sudo make install`
	4. Just in case: 
		```
		sudo install -m644 clevo-xsm-wmi.ko /lib/modules/$(uname -r)/extra
		sudo depmod
		sudo tee /etc/modules-load.d/clevo-xsm-wmi.conf <<< clevo-xsm-wmi
		```
- `gksudo` (or `kdesudo` for KDE) if you want to use a keyboard shortcut to run the script. Install with `sudo apt install gksu` or `sudo apt install kdesudo`. This is probably already installed.
- `acpi` for the battery monitor: `sudo apt install acpi`

Installation
=====
1. Download and unzip, or clone, this repo, then add the script to your path by adding the following line to `~/.bashrc`:
`export PATH=$PATH:<dir>` where `<dir>` is the full path to the repo. For example if you downloaded it to your desktop, it would look like `export PATH=$PATH:~/Desktop/clevo-keyboard-backlight-control`.
2. `cd` into the clevo-keyboard-backlight-control directory and make the scripts executable:
```
chmod +x kbtoggle
chmod +x batterymon
```


## Turning off the password check
This utility needs to unload and reload the keyboard kernel module in order to update its configuration without a reboot. Therefore, it requires root privileges, so when you run it from the command line, it will ask for your password.
This gets annoying, but you can easily disable it:
1. `sudo visudo`, this will open the `/etc/sudoers` file in your terminal
2. Add the following lines to the end of the file, where `<pathtoscript>` is the full path to this utility on your system:
```
username ALL=(ALL) NOPASSWD: <pathtoscript>/kbtoggle,<pathtoscript>/batterymon
```
3. Save and close the file

Usage
=====
- To toggle the backlight on and off from the command-line: `kbtoggle`
- To toggle the backlight on and off with a keyboard shortcut
	1. Create a custom command shortcut in your system settings (exact steps vary by distro - please google).
	2. Specify the command as `gksudo <pathtoscript>/kbtoggle`. Replace `gksudo` with `kdesudo` if using KDE. 
	3. Using `gksudo` or `kdesudo` will pop up a graphical interface to ask for your password when you hit your chosen keyboard shortcut. I haven't found a way to make these respect the sudoers file yet, so you'll have to give you password every time.
- To change the colorscheme of the keyboard, from a terminal run `kbtoggle <colorscheme>` where `<colorscheme>` is the name of one the files in `kb-templates` without the `.txt` file extension.
- To make the keyboard change color when the battery is low (20%) or critical (10%), run `sudo crontab -e`, add the following line (where `<pathtoscript>` is the full path to this utility on your system) and save:
```
*/2 * * * * cd <pathtoscript> && ./batterymon >> batterymon.log
```
This will check your battery level every 2 minutes, and run a script to change the color of the keyboard if it's low. Any errors from attempting to run the script will be output to `batterymon.log`.


Advanced usage
==============
The keyboard has 3 sections which can be independantly colored. The kernel module supports the following predefined colors:
```
red
yellow
green
cyan
blue
magenta
white
```

Most of the files in `kb-templates` are named after the initials of the 3 colors in their colorscheme. For example, `yrb` stands for `yellow, red, blue` in that order, from left to right, across the keyboard. A few schemes are solid colors (white, cyan) and some are gradients of 2 closely related colors.

You can create your own colorschemes easily - just duplicate one of the existing schemes and replace the specified colors with your choice of colors from the list above. Save it with a name you'll remember so you can easily call it from the command line (you may want to rename your favorite existing schemes for the same reason!)

TODO
====
- Add an option to randomly generate a colorscheme when running `kbtoggle` with an appropriate parameter.
- Prevent `batterymon` from insisting on switching the backlight on if you want it off.
- Make `batterymon` actually run from cron properly :'(
- Any improvements to this hastily hacked-together code are welcomed.
