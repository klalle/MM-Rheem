skönt att slippa koppla in tangentbord i rpi för att göra ändringar, logga in från mac/win istället
Enable: 
```bash
#se till att ssh är enableat i raspi-config:
sudo raspi-config
- interface options
  - SSH -> enable
    
```
### ssh-keygen
Skapa ett nyckel-par där du har den privata nyckeln lokalt (din mac/linux/win), och lägger den publika (.pub) på de servrar (rpi) du vill logga in på utan lösenord.
```shell
#på din mac/win:
ssh-keygen #svara default på allt (skippa lösen)
# nu kan du se att nyckelparet ligger under ~/.ssh

#kopiera den publika nyckeln till din rpi: (antar att användarnamn är "pi" å att nyckeln heter id_rsa.pub
ssh-copy-id -i ~/.ssh/id_rsa pi@http://raspberrypi.local #alt ip-adressen till din rpi
```

## robustifiera ssh
lägg till detta så att den verkligen starar om
```
sudo systemctl edit ssh.service

### Editing /etc/systemd/system/ssh.service.d/override.conf
### Anything between here and the comment below will become the new contents of the file

[Unit]
# Se till att sshd startar först när nätverket är redo
After=network-online.target
Wants=network-online.target

[Service]
# Försök alltid starta om sshd om det misslyckas
Restart=always
RestartSec=5

### Lines below this comment will be discarded