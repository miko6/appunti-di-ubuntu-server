## My UBUNTU SERVER installation ![alt text](https://github.com/miko6/appunti-di-ubuntu-server/blob/main/immagini/ubuntu-server-logo-300x125.png "ubuntu")  

1. ***Abilitare il firewall ufw***  

`sudo ufw enable`  

---  

2. ***Installare ssh***  

`sudo apt install openssh-server`  

- verificare lo stato e attivare Ubuntu Server SSH  

`sudo systemctl status ssh`  

- Qualora il servizio SSH sia ancora inattivo e non si avvii automaticamente neanche in seguito a un reboot, potrete modificare lo stato digitando queste due righe di comando:  

`sudo systemctl enable ssh`  

`sudo systemctl start ssh`  

- aprire la porta SSH  

`sudo ufw allow ssh`  

---  

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

`sudo ufw allow 'Samba'`  

---  

4. ***Installare duf (Disk Usage/Free utility)***  

`sudo apt install duf`  

---  

5. ***Installare Docker engine & compose***  

[Guida](https://docs.docker.com/engine/install/ubuntu/)  

- Installare Docker Compose  

`sudo apt-get install docker-compose-plugin`

---  

6. ***Pihole container***  

https://github.com/pi-hole/docker-pi-hole?tab=readme-ov-file  

- Creare una cartella nel sistema, spostatici dentro e salva il seguente file con nome docker-compose.yml  
More info at https://github.com/pi-hole/docker-pi-hole/ and https://docs.pi-hole.net/  

```
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      # DNS Ports
      - "53:53/tcp"
      - "53:53/udp"
      # Default HTTP Port
      - "80:80/tcp"
      # Default HTTPs Port. FTL will generate a self-signed certificate
      - "443:443/tcp"
      # Uncomment the line below if you are using Pi-hole as your DHCP server
      #- "67:67/udp"
      # Uncomment the line below if you are using Pi-hole as your NTP server
      #- "123:123/udp"
    environment:
      # Set the appropriate timezone for your location (https://en.wikipedia.org/wiki/List_of_tz_database_time_zones), e.g:
      TZ: 'Europe/Rome'
      # Set a password to access the web interface. Not setting one will result in a random password being assigned
      FTLCONF_webserver_api_password: 'serverspam'
      # If using Docker's default `bridge` network setting the dns listening mode should be set to 'ALL'
      FTLCONF_dns_listeningMode: 'ALL'
    # Volumes store your data between container upgrades
    volumes:
      # For persisting Pi-hole's databases and common configuration file
      - './etc-pihole:/etc/pihole'
      # Uncomment the below if you have custom dnsmasq config files that you want to persist. Not needed for most starting fresh with Pi-hole v6. If you're upgrading from v5 you and have used this directory before, you should keep it enabled for the first v6 container start to allow for a complete migration. It can be removed afterwards. Needs environment variable FTLCONF_misc_etc_dnsmasq_d: 'true'
      #- './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      # See https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
      # Required if you are using Pi-hole as your DHCP server, else not needed
      - NET_ADMIN
      # Required if you are using Pi-hole as your NTP client to be able to set the host's system time
      - SYS_TIME
      # Optional, if Pi-hole should get some more processing time
      - SYS_NICE
    restart: unless-stopped  
```  

- lanciare il comando seguente per compilare lo script  

`docker compose up -d`  

---  

7. ***BentoPDF container***  

```
services:
  bentopdf:
    # simple mode - bentopdf/bentopdf-simple:latest
    # default mode - bentopdf/bentopdf:latest
    image: bentopdf/bentopdf-simple:latest
    container_name: bentopdf
    restart: unless-stopped
    ports:
      - '8080:8080'
```  

- lanciare il comando seguente per compilare lo script  

`docker compose up -d`  

---  

8. ***Installare sensori temperatura***  

- prima aggiungere il repository  

`sudo add-apt-repository ppa:malcscott/ppa`  

`sudo apt-get update && sudo apt-get install lm-sensors hddtemp`

- comando `sensors` per leggere temperature  

- `sudo hddtemp /dev/sda2` per leggere temp hard disk  