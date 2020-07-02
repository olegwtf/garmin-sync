# garmin-sync

Automatically upload new activities from specified directory to garmin connect.

**Use case:**
1. Make your ride to create new activity file
2. Connect your device to computer via USB cable
3. New activity will be automatically uploaded to garmin connect, you will get tray notification about success or failure

The same way as it works on Windows with official client.

## Installation

Run `sudo make install-debian` if you are on Debian based distro. Otherway see `Makefile` for dependencies list and try to install them yourself.

## Configuration

To configure `garmin-sync` you should run it from command line without any parameters. This will ask you for login and password from garmin connect, path to the directory with activities and so on. All configuration are stored at `~/.config/garmin-sync`.

## Manual syncing

At this point you can connect your device to computer using USB cable, mount it and run `garmin-sync` manually to upload new activities to garmin connect. See next section if this is not very handy for you.

## Automatic syncing on device connection

1. First of all you need to make your device mount automatically when it is connected to computer. For example in KDE I can do this in systemsettings:
`Removable storage -> Removable devices -> Enable automatic mounting of removable media -> select your device`.
2. Then we need to create new systemd service which will execute `garmin-sync` when our device is mounted:
    * run `systemctl edit garmin.service --force --user` from your normal user
    * and create such unit file:
    ```
    [Unit]
    Description=Garmin Activities Uploader
    Requires=media-oleg-GARMIN.mount
    After=media-oleg-GARMIN.mount

    [Service]
    ExecStart=/usr/local/bin/garmin-sync

    [Install]
    WantedBy=media-oleg-GARMIN.mount
    ```
    Replace `media-oleg-GARMIN.mount` with name of your device, which may be found with `sudo systemctl list-units -t mount`
3. Enable systemd service: `systemctl enable garmin.service --user`

Next time you'll connect garmin device all new activities will be automatically uploaded to garmin connect.

## How detection of new activities works

First of all it will never remove activities from watched directory. To find new activities it stores modification time of newest activity from last run in `~/.config/garmin-sync/last_uploaded_mtime.json`. You can modify this time manually if you ever need.
