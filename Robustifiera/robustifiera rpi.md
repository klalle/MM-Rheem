## Watchdog
Om rpi den fryser i 15s så startar den om.
```bash
sudo nano /etc/systemd/system.conf
#ta bort kommentaren och ange:
RuntimeWatchdogSec=15
sudo systemctl daemon-reload

```

## [[Network restart]]
min pi tappade nätverket när routern startade om.