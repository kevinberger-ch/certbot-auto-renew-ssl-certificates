# Certbot Auto Renew SSL Certificates with Dynv6

Auto Renew certificates using dns authentication with the DynV6 REST-API

***

## Requirements
1. You need an SSH-Connection to your linux webserver
2. You need have *certbot* installed [certbot-installation-guide](https://certbot.eff.org/)
3. You need one or more pre-configured ssl certificates created with *certbot*
   1. The certificate must be created with ```certbot certonly -manual```
   2. The certificate needs the DNS authentication method

## Setup

>#### Connect your web server (Linux) with the Dynv6 Api
>
>* On your webserver generate an *ed_25519* ssh-key with ```ssh-keygen -t ed25519```
>    * Leave all inputs empty and just press enter   
>* Copy your generated key 
>    * You can get your key with ```cat ~/.ssh/id_ed25519.pub```
>  
>* Go to [dynv6.com/keys](https://dynv6.com/keys)
>    * Click on *add SSH Public Key*
>   * Enter your copied key and click on *Add*


>#### Configure certbot on your web server
>
>* On your webserver go to ```/etc/letsencrypt/renewal-hooks/pre``` 
>  * Download the *dns-authenticator.sh* script with ```wget https://git.kvn-brg-srv.ch/kevin/certbot-auto-renew-dynv6/raw/branch/main/dns-authenticator.sh```

>#### Renew all of your certificates
>
>Enter this command ```certbot renew --manual-auth-hook /etc/letsencrypt/renewal-hooks/pre/dns-authenticator.sh --manual-public-ip-logging-ok``` 

>#### (Optional) Fully automated renew with *crontab*
>* Enter ```crontab -e```
>  * Add the following line ```0 12 * * * /usr/bin/certbot renew -manual-auth-hook /etc/letsencrypt/renewal-hooks/pre/dns-authenticator.sh --manual-public-ip-logging-ok --quiet```
> 
> From now on certbot will check every day if any of your certificates will expire in the next 30 days, if any do they will automatically be renewed

### Notice

After renewing your certificates you need to restart your webserver<br>
```systemctl restart apache2``` in case of an apache webserver<br>
```systemctl restart nginx``` in case of a nginx webserver
