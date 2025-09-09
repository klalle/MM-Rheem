```bash
#skapa ett startscript: 
startMagicMirror.sh
#klistra in nedan, gör sedan exekverbar: 
sudo chmod +x ./startMagicMirror.sh

#!/bin/bash

# sätt på och rotera skärmen:
# X11
DISPLAY=:0 xrandr --output HDMI-1 --auto --rotate left

# if using Wayland:
#export WAYLAND_DISPLAY=wayland-0 #1
#export XDG_RUNTIME_DIR=/run/user/$(id -u)
#export G_SLICE=always-malloc
#wlr-randr --output HDMI-A-1 --on  --transform 90

# Kolla om argumentet "live" finns
if [[ "$1" == "live" ]]; then
    DRYRUN=""
    echo "Live sync påbörjas – filer kommer skrivas och raderas."
elsenan
    DRYRUN="--dry-run"
    echo "Dry-run – inga filer kommer att ändras."
fi

# Var noga med hur du sätter upp rclone sync då det finns risk att man deletar filer remote!!! börja med dryrun för att se vad som händer! 
rclone sync dropbox:Bilder /home/kalle/Pictures/ \
  --include "/{2017,2018,2019}/{album,almanacka}/*.jpg" \
  --include "/{2017,2018,2019}/{album,almanacka}/excludeImages.txt" \
  --include "/202[0-9]/*.jpg" \
  --include "/202[0-9]/excludeImages.txt" \
  --filter "- *" \
  --ignore-case \
  --delete-excluded \
  --ignore-checksum \
  --size-only \
  -P \
  $DRYRUN \
  || true

#jag kör igång servern headless då default browser (chromium/electron) inte alls klarade zoom lika smooth: 
cd /home/pi/MagicMirror && npm run server >/dev/null 2>&1 > /home/pi/Scripts/node_mm_$(date +%Y-%m-%d_%H-%M-%S).log 2>&1 &
SERVER_PID=$! # Spara process-ID för servern

echo -n "Väntar på att node-servern ska starta"
while ! curl -s -f http://localhost:8080 > /dev/null
do
    echo -n "."
    # Kontrollera om serverprocessen har dött
    if ! kill -0 $SERVER_PID > /dev/null 2>&1; then
        echo "MagicMirror-servern verkar ha kraschat vid start."
        exit 1
    fi
    sleep 1
done
echo ""
echo "Servern är uppe! Startar Firefox..."

export DISPLAY=:0 #tells the remote computer to send its graphical output to your local machine display if run from ssh

MOZ_DISABLE_RDD_SANDBOX=1 firefox --kiosk --private-window http://localhost:8080 &

```
