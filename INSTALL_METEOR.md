##Come creare un bundle meteor ed installarlo

###Nel server di sviluppo

Su un computer linux, installare meteor con

	curl https://install.meteor.com/ | sh

Il computer può essere anhe una macchina virtuale. Sconsiglierei
però di usare la stessa macchina che viene usata in produzione, onde
evitare inconvenienti di alcun tipo.

Installare il tool meteorite:

	sudo npm install -g meteorite

Clonare la repository github dell'applicazione (in caso di
repository private, possono essere necessarie le credenziali
di github, oppure una chiave ssh valida aggiunta su github):

	git clone https://github.com/<nome-utente>/<nome-repository>

Spostarsi nella cartella della repo ed installare i
pacchetti usati dall'applicazione:

	cd <nome-repository>
	mrt install

Verificare che tutto funzioni con:

	meteor

Dovrebbe avviarsi il server locale di sviluppo, accessibile
alla pagina http://localhost:3000 . Verificato il
funzionamento, arrestare il server con `Crtl+C` e creare il
bundle:

	meteor bundle bundle.tgz

Copiare il bundle nella cartella home dell'utente `meteor`
nel server di produzione:

	scp bundle.tgz meteor@<indirizzo-server-di-produzione>:

###Nel server di produzione

####Come utente `meteor`

Come utente `meteor` nel server di produzione, decomprimere
il bundle:

	tar -xvzf bundle.tgz

L'applicazione verrà così estratta nella cartella
`~/bundle/`.

E' ora necessario reinstallare il pacchetto npm `fibers`,
poiché esso necessita di essere ricompilato (operazione
fatta in automatico con l'installazione):

	cd ~/bundle/programs/server/
	npm install fibers
	cd

Copiare all'interno della cartella `~/bundle/` il file
`env_vars` di questa repository, che definisce alcune
variabili d'ambiente necessarie a meteor:

	wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/env_vars ~/bundle/env_vars

Apportare le modifiche necessarie al file (all'interno del
file ci sono indicazioni su cosa cambiare).

Marcare il file come eseguibile con:

	chmod +x ~/bundle/env_vars

####Come utente `root`

Le seguenti istruzioni vanno eseguite dall' utente `admin`.
Esse infatti richiedono che vengano eseguiti i comandi come
utente `root` mediante l'uso del comando `sudo`. L'utente
`meteor` non ha questa possibilità, poiché in questo modo
si riduce al minimo la "superficie di attacco" del server.

Copiare nella cartella `/etc/init` il file `meteor.conf` di
questa repository, che definisce il servizio upstart
"meteor", il che permette di avviare e arrestare il server
meteor con i comandi `service meteor start` e `service
meteor stop`, oltre che fare avviare il server meteor dopo
il boot.

	sudo wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/meteor.conf /etc/init/meteor.conf

Copiare nella cartella `/usr/bin/` il file
`meteor_server.sh` (uno script usato dal servizio "meteor"
di upstart), e marcarlo come eseguibile:

	sudo wget https://raw.githubusercontent.com/pscanf/meteor-deploy-howto/master/meteor_server.sh /usr/bin/meteor_server.sh
	sudo chmod +x /usr/bin/meteor_server.sh

A questo punto è possibile avviare il server con:

	service meteor start
