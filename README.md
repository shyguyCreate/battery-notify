# Battery Notifier

Script and configuration for to get notifications on Xorg.

### Script file

Download the script [battery-notify](./battery-notify) to a directory in **$PATH** like `~/.local/bin` and make it executable.

### .Xenv file

Add this to your ~/.xprofile

```
cat > ~/.Xenv << EOF
export XAUTHORITY="$XAUTHORITY"
export DISPLAY="$DISPLAY"
export DBUS_SESSION_BUS_ADDRESS="$DBUS_SESSION_BUS_ADDRESS"
EOF
```

### Cron

Add [cron job](https://wiki.archlinux.org/title/cron#Running_X.org_server-based_applications) for scheduled battery notifications.

```
echo "* * * * * . $HOME/.Xenv &&  $HOME/.local/bin/battery-notify" | crontab -u $USER -
```

### Udev

Send notification when battery is [plugged/unplugged](https://wiki.archlinux.org/title/udev#Triggering_desktop_notifications_from_a_udev_rule).

```
sudo mkdir -p /etc/udev/rules.d
sudo tee /etc/udev/rules.d/99-battery-notify-$USER.rules > /dev/null << EOF
ACTION=="change", SUBSYSTEM=="power_supply", ATTR{type}=="Mains", ATTR{online}=="0", \\
RUN+="/usr/bin/su $USER -c '. $HOME/.Xenv && $HOME/.local/bin/battery-notify'"

ACTION=="change", SUBSYSTEM=="power_supply", ATTR{type}=="Mains", ATTR{online}=="1", \\
RUN+="/usr/bin/su $USER -c '. $HOME/.Xenv && $HOME/.local/bin/battery-notify'"
EOF
```
