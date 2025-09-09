```bash
#!/bin/bash

# X11: 
DISPLAY=:0 xrandr --output HDMI-1 --off

#om wayland: 
#export WAYLAND_DISPLAY=wayland-0 #1 
#export XDG_RUNTIME_DIR=/run/user/$(id -u) #behövdes då crontab-reboot körs innan denna sätts av systemet
#wlr-randr --output HDMI-A-1 --off

pkill -f node
pkill -f firefox

```