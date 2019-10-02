<!-- TITLE: snapd -->
<!-- SUBTITLE: Linux package manager and runtime -->

snap apps are self contained apps having their own _squashfs_.

# Commands
`sudo snap install <app>` - installs the given snap app, `sudo snap remove <app>` uninstalls it of course.
`sudo snap list` - shows the installed snap apps
`sudo snap refresh <app>` - checks for app for updates (although snapd daemon will regularly check and apply updates.
`sudo snap refresh --list` - checks all snap app for updates
`sudo snap revert <app>` - rollbacks a recent update on an snap app