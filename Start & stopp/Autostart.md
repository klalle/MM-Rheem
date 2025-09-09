När rpi startar om eller startar upp för första gången: 
```bash
sudo nano /etc/rc.local

#lägg till (innan exit 0)
sudo -u pi /home/pi/startMagicMirror.sh live & 
```
