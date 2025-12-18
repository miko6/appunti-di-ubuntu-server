## My UBUNTU SERVER installation ![alt text](https://github.com/miko6/appunti-di-ubuntu-server/blob/main/immagini/ubuntu-server-logo-300x125.png "ubuntu")  

1. ***Abilitare il firewall ufw*** 
`sudo ufw enable`  

2. ***Installare ssh***  

`sudo apt install openssh-server`  

> verificare lo stato e attivare Ubuntu Server SSH  

`sudo systemctl status ssh`  

- Qualora il servizio SSH sia ancora inattivo e non si avvii automaticamente neanche in seguito a un reboot, potrete modificare lo stato digitando queste due righe di comando:  

`sudo systemctl enable ssh`  

`sudo systemctl start ssh`  

- aprire la porta SSH  

`sudo ufw allow ssh`  

3. ***Installazione di Samba***  

`sudo apt update`  

`sudo apt install samba`  

- Creazione della cartella da condividere  

`sudo mkdir -p /srv/samba/condivisa`  

- Imposta i permessi sulla cartella  

`sudo chmod -R 0775 /srv/samba/condivisa`  

- Imposta l'utente e il gruppo proprietari della cartella  

`sudo chown -R nobody:nogroup /srv/samba/condivisa`  

- Configurazione di Samba  

`sudo nano /etc/samba/smb.conf`  

- Aggiungi una nuova sezione alla fine del file per la tua condivisione  

```
[Shared]
path = /srv/samba/condivisa
browsable = yes
read only = no
guest ok = yes
```  

- Gestione degli utenti Samba - Crea un utente Samba  

`sudo useradd sambauser`  

`sudo passwd sambauser`  

`sudo smbpasswd -a sambauser`  

- Modifica la sezione di condivisione per specificare l'utente e rimuovere guest ok = yes  

```
[Shared]
path = /srv/samba/condivisa
valid users = sambauser
read only = no
```  

- Riavvio e configurazione del firewall  

`sudo systemctl restart smbd` 

- Abilita il servizio Samba all'avvio del server  

`sudo systemctl enable smbd`  

- Se hai ufw attivo, apri la porta per Samba  

`sudo ufw allow 'Samba`  

4. ***Installare duf (Disk Usage/Free utility)***  

`sudo apt install duf`