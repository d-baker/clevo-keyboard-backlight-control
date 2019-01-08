What is this?
=============

A command-line frontend for [tuxedo_keyboard](https://github.com/tuxedocomputers/tuxedo-keyboard), the kernel module for controlling the backlight on Clevo laptops. In simpler terms, it lets you switch the backlight on and off, and set the colors on RGB keyboards. I created this on an Ubuntu-based Linux distro and can't guarantee it will work on other distros.

Dependencies
============
- `tuxedo-keyboard`
[Download it here](https://github.com/tuxedocomputers/tuxedo-keyboard) and follow installation instructions

Installation
=====
1. Download and unzip, or clone, this repo, then add the script to your path by adding the following line to `~/.bashrc`:
`export PATH=$PATH:<dir>` where `<dir>` is the full path to the repo. For example if you downloaded it to your desktop, it would look like `export PATH=$PATH:~/Desktop/clevo-keyboard-backlight-control`.
2. `cd` into the clevo-keyboard-backlight-control directory and make the scripts executable:
```
chmod +x kbtoggle
chmod +x kbsetcolor
chmod +x batterymon
```
3. In order to run the `batterymon` script through cron, you need to have it in your `root` path since it has to be added to `root`s cron table. So, edit `/etc/environment` as root and append the path to the script.


## Turning off the password check
This utility needs to unload and reload the keyboard kernel module in order to update its configuration without a reboot. Therefore, it requires root privileges, so when you run it from the command line, it will ask for your password.
This gets annoying, but you can easily disable it:
1. `sudo visudo`, this will open the `/etc/sudoers` file in your terminal
2. Add the following lines to the end of the file, where `<pathtoscript>` is the full path to this utility on your system:
```
username ALL=(ALL) NOPASSWD: <pathtoscript>/kbtoggle,<pathtoscript>/kbsetcolor,<pathtoscript>/batterymon
```
3. Save and close the file

Usage
=====
- To toggle the backlight on and off from the command-line: `kbtoggle`
- To toggle the backlight on and off with a keyboard shortcut
	1. Create a custom command shortcut in your system settings (exact steps vary by distro - please google).
	2. Specify the command as `gksudo <pathtoscript>/kbtoggle`. Replace `gksudo` with `kdesudo` if using KDE. 
	3. Using `gksudo` or `kdesudo` will pop up a graphical interface to ask for your password when you hit your chosen keyboard shortcut. I haven't found a way to make these respect the sudoers file yet, so you'll have to give you password every time.
- To change the colorscheme of the keyboard, from a terminal run `kbsetcolor <colorscheme>` where `<colorscheme>` is the name of one the files in `kb-templates` without the `.txt` file extension. Running `kbsetcolor` without any arguments will give you a list of all the colorscheme arguments you can use.
- To create a new colorscheme, run `kbsetcolor -n` and follow the prompts.
- You can set a default colorscheme to use if there is no colorscheme already set: `kbsetcolor -d <colorscheme>`
- To make the keyboard change color when the battery is low (20%) or critical (10%), run `sudo crontab -e`, add the following line (where `<pathtoscript>` is the full path to this utility on your system) and save:
```
*/2 * * * * cd <pathtoscript> && ./batterymon >> batterymon.log
```
This will check your battery level every 2 minutes, and run a script to change the color of the keyboard if it's low. Any errors from attempting to run the script will be output to `batterymon.log`.
Note: you can also run `batterymon` manually from the terminal to check your current battery level on a once-off basis.


Advanced usage
==============
The keyboard has 3 sections which can be independantly colored: `color_left`, `color_center`, and `color_right`. You can use any hex values formatted `0x123456` here, but be warned that the actual colors your keyboard is capable of may be limited and you might get unexpected results. The following work reliably on my machine:
```
Red: 0xFF0000
Green: 0x00FF00
Blue: 0x0000FF
Cyan: 0x00FFFF
Magenta: 0xFF00FF
Yellow: 0xFFFF00
Orange: 0xFF6600
White: 0xFFFFFF
Black (backlight off): 0x000000
```

Most of the files in `kb-templates` are named after the initials of the 3 colors in their colorscheme. For example, `yrb` stands for `yellow, red, blue` in that order, from left to right, across the keyboard. A few schemes are solid colors (white, cyan) and some are gradients of 2 closely related colors.

You can create your own colorschemes using the `kbsetcolor -n` command. Alternatively, you can create a colorscheme manually by duplicating one of the existing schemes and replace the specified colors with your choice of colors from the list above. Save it with a name you'll remember so you can easily call it from the command line (you may want to rename your favorite existing schemes for the same reason!)
