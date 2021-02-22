+++
title = "Abilitare HTTPS su Apache2"
template = "post.html"
date = 2021-02-22
draft = false

[taxonomies]
tags = ["apache2", "https", "ssl", "LAMP", "ubuntu", "linux"]
categorie = ["web server"]
+++

Nel seguito si suppone di voler abilitare *su un server [Apache2](https://httpd.apache.org/)* il protocollo <abbr title="HyperText Transfer Protocol over Secure Socket Layer">HTTPS</abbr> affinché si possano abilitare VirtualHosts in <abbr title="Secure Sockets Layer">SSL</abbr> dedicati a siti web con domini preconfigurati. 

Quindi si vuole rendere lo scambio di tati sicuro. 

Per evitare lo sniffing di dati scambiati bidirezionalmente fra server e client solitamente si procede all'abilitazione del protocollo di crittografia asimmetrica.

Ipotizzando di avere già un server LAMP preconfigurato e funzionante, è possibile procedere all'abilitazione dell'https: operazione anche questa più da sistemista che da web developer, ma sicuramente alla portata di tutti.

<!-- more -->

Su piattaforme Linux basate su *sistemi [Ubuntu[(https://ubuntu.com/)*, così come su altre distribuzioni, il **modulo SSL** è già presente nell'installazione base di Apache.
Bisogna solo attivarlo con il seguente comando che abilita l'utilizzo della porta 443 solitamente utilizzata per l'https:

`sudo a2enmod ssl`

A questo punto è necessario creare una cartella in cui generare i certificati di sicurezza utilizzati dall'SSL. A tal proposito è consigliabile creare una nuova sottocartella dentro quella di Apache:

`sudo mkdir /etc/apache2/ssl`

In altri sistemi la cartella del web server può essere diversa da `/etc/apache2`, ad esemprio in *CentOS* si avrà `/etc/httpd`.

Per ottenere la chiave privata e il certificato si utilizza openssl che solitamente troverete già installato nel sistema. È però opportuno verificare che openssl sia installato nel sistema:

`sudo apt policy openssl`

e, in caso non fosse installato, procedere alla sua installazione con:

`sudo apt install openssl`

Il seguente comando generera la key ed il certificato richiesto con crittografia RSA a 2048 bit e una validità di 365 giorni:

`sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt`

Verrà avviata una procedura di creazione dei certificati durante la quale verranno poste alcune semplici domande:

```
Country Name (2 letter code) [AU]:IT
State or Province Name (full name) [Some-State]:Pordenone
Locality Name (eg, city) []:Pordenone
Organization Name (eg, company) [Internet Widgits Pty Ltd]:lamiaorganizzazione
Organizational Unit Name (eg, section) []:Development
Common Name (e.g. server FQDN or YOUR name) []:ServerMio
Email Address []:maildiriferimento@dominio.it
```

A questo punto non resta che configurare correttametne i VirtualHost e abilitarli sulla connessione sicura che si trova sulla porta 443 accessibile con la chiave e il certificato appena creati.

`sudo vim /etc/apache2/sites-available/default-ssl.conf`

```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
 
        DocumentRoot /var/www/html
        <Directory />
                Options FollowSymLinks
                AllowOverride All
        </Directory>
        <Directory /var/www/>
                Options -Indexes +FollowSymLinks +MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>
 
        ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
        <Directory "/usr/lib/cgi-bin">
                AllowOverride None
                Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                Order allow,deny
                Allow from all
        </Directory>
 
        ErrorLog ${APACHE_LOG_DIR}/error.log
 
        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn
 
        CustomLog ${APACHE_LOG_DIR}/access.log combined
 
        Alias /doc/ "/usr/share/doc/"
        <Directory "/usr/share/doc/">
                Options MultiViews FollowSymLinks
                AllowOverride None
                Order deny,allow
                Deny from all
                Allow from 127.0.0.0/255.0.0.0 ::1/128
        </Directory>
 
</VirtualHost>
```

A questo punto la configurazione è terminata e basta abilitare il VirtualHost di interesse:

`sudo a2ensite default-ssl.conf`

e *riavviare* il server apache per rendere effettive le modifiche

`sudo systemctl restart apache2`

Ora, ammesso che il nostro firewall consenta il traffico sulla **porta 443**, visitando l'indirizzo del VirtualHost comparirà un avviso che ci avverte che il certificato non è attendibile. Questo è normale in quanto lo abbiamo autogenerato e non acquistato da un ente certificatore: basterà procedere accettando i rischi per navidare sul dominio in maniera sicura. 

Questa generazione dei certificati è consigliabile per utilizzo in localhost in fase di sviluppo o testing, ma per siti o app pubbliche è vivamente consigliato l'acquisto di un certificato verificato anche per non spaventare gli utenti con l'avviso di sicurezza che tutti i browser mostreranno. 

