####Installazione e aggiornamento dell'OS

 Attivare un nuovo droplet con immagine Ubuntu 12.04LTS (o 14.04LTS) MEAN Stack.

Fare il login via ssh con user root nel droplet appena creato.

	ssh root@<ip-address>

Aggiornare il droplet:

	apt-get update && apt-get dist-upgrade

Riavviare il droplet:

	reboot

###Come utente `root`

####Creazione dell'utente admin

Creare un utente admin:

	useradd -m -s /bin/bash admin

Aggiungere l'utente al gruppo sudo:

	gpasswd -a admin sudo

Cambiare la password dell'utente admin:

	passwd admin

Creare la cartella `.ssh` nella home dell'utente admin:

	mkdir /home/admin/.ssh

All'interno di questa cartella, creare il file `authorized_hosts` ed incollare al suo interno la propria public key ssh.

Impostare i permessi appropriati per la cartella:

	chown -R admin:admin /home/admin/.ssh

####Creazione dell'utente meteor

Creare un utente meteor:

	useradd -m -s /bin/bash meteor

Aggiungere l'utente al gruppo sudo:

	gpasswd -a meteor sudo

Cambiare la password dell'utente meteor:

	passwd meteor

Creare la cartella `.ssh` nella home dell'utente meteor:

	mkdir /home/meteor/.ssh

All'interno di questa cartella, creare il file `authorized_hosts` ed incollare al suo interno la propria public key ssh.

Impostare i permessi appropriati per la cartella:

	chown -R meteor:meteor /home/meteor/.ssh

####Configurazione dell'ssh daemon

Scaricare il file `sshd_config` da questa repository, e sostituire con esso il file `/etc/ssh/sshd_config`:

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/sshd_config -O /etc/ssh/sshd_config

Riavviare il servizio ssh:

	service ssh restart

####Configurazione nginx

Scaricare il file `nginx.conf` da questa repository, e sostituire con esso il file `/etc/nginx/nginx.conf`:

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/nginx.conf -O /etc/nginx/nginx.conf

Scaricare il file `app.conf` da questa repository, e metterlo nella cartella `/etc/nginx/sites-enabled/`:

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/app.conf -O /etc/nginx/sites-enabled/app.conf

Modificare il `/etc/nginx/sites-enabled/app.conf` inserendo i corretti parametri
per la propria applicazione (indicazioni su cosa modificare sono presenti all'interno dei file).

Se viene usato SSL, sostituire nella cartella `/etc/nginx/cert` i certificati self-signed con un certificato valido.

####Configurazione iptables

Scaricare il file `iptables.rules` da questa repository, e spostarlo nella cartella `/etc/`

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/iptables.rules -O /etc/iptables.rules

Scaricare il file `load_iptables_rules.sh` da questa repository, e spostarlo nella cartella `/etc/network/if-pre-up.d`

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/load_iptables_rules -O /etc/network/if-pre-up.d/load_iptables_rules

Marcare il file come eseguibile:

	chmod +x /etc/network/if-pre-up.d/load_iptables_rules
