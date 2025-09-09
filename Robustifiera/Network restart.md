nätverksrestart vid strömavbrott
```bash
sudo nano /usr/local/bin/network-check.sh



#!/bin/bash

# En pålitlig extern adress att pinga. 8.8.8.8 är Googles DNS.
TARGET=8.8.8.8

# Skicka ett (1) ping-paket, med en timeout på 2 sekunder.
# Skicka all output till /dev/null eftersom vi bara bryr oss om returkoden.
ping -c 1 -W 2 $TARGET > /dev/null 2>&1

# Kontrollera returkoden från ping-kommandot. 0 = lyckades, allt annat = misslyckades.
if [ $? -ne 0 ]; then
    # Om ping misslyckades, logga ett meddelande och starta om nätverkstjänsten.
    # logger-kommandot skriver till systemloggen (journalctl), vilket är bra för felsökning.
    logger -t network-check "Nätverksfel upptäckt. Startar om nätverket..."
    
    # *** VIKTIGT: Välj ETT av kommandona nedan ***
    # Avkommentera den rad som gäller för ditt system.
    #ta reda på vilken: 
    # systemctl is-active dhcpcd.service
    # systemctl is-active NetworkManager.service
    
    # För de flesta Raspberry Pi OS-installationer som använder dhcpcd:
    #systemctl restart dhcpcd.service
    
    # Om du använder NetworkManager (vanligare på andra Linux-distar):
    systemctl restart NetworkManager.service
    
else
    # Valfritt: Logga att allt är OK. Kan vara bra för att se att skriptet körs.
    # Du kan ta bort detta om du vill undvika "spam" i loggarna.
    logger -t network-check "Nätverksanslutning är OK."
fi



sudo chmod +x /usr/local/bin/network-check.sh
```

skapa systemdtjänst:
```bash
sudo nano /etc/systemd/system/network-check.service


[Unit]
Description=Kontrollerar nätverksanslutning och startar om vid behov
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/network-check.sh

[Install]
WantedBy=multi-user.target
```

skapa systemd-timer
```bash
sudo nano /etc/systemd/system/network-check.timer

[Unit]
Description=Kör network-check.service var 5:e minut

[Timer]
# Kör 2 minuter efter uppstart för att ge routern en chans att starta först
OnBootSec=2min
# Kör därefter var 5:e minut
OnUnitActiveSec=5min
Unit=network-check.service

[Install]
WantedBy=timers.target
```

Starta om demonen
```bash
sudo systemctl daemon-reload
sudo systemctl enable network-check.timer
sudo systemctl start network-check.timer

#titta på listan med tajmrar + loggen
systemctl list-timers
journalctl -u network-check.service -f
```