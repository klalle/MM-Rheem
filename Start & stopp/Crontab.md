```bash
# starta vid 06:00
0 6 * * * /home/pi/startMagicMirror.sh live >> /home/pi/startMM.log 2>&1

#stäng av vid 23:00
0 23 * * * /home/pi/stopMagicMirror.sh >> /home/pi/stopMM.log 2>&1