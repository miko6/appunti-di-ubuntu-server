## My UBUNTU SERVER installation ![alt text](https://github.com/miko6/appunti-di-ubuntu-server/blob/main/immagini/ubuntu-server-logo-300x125.png "ubuntu")  

1. **Abilitare il firewall ufw** 
`sudo ufw enable`  

2. **Installare ssh**  

`sudo apt install openssh-server`  

- verificare lo stato e attivare Ubuntu Server SSH  

`sudo systemctl status ssh`  

- Qualora il servizio SSH sia ancora inattivo e non si avvii automaticamente neanche in seguito a un reboot, potrete modificare lo stato digitando queste due righe di comando:  

`sudo systemctl enable ssh`  

`sudo systemctl start ssh`  

- aprire la porta SSH  
 
`sudo ufw allow ssh`