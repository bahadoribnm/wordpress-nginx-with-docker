# wordpress-nginx-with-docker
run wordpress-nginx with dicker containers

Create directories on host

Directories are created on the host to persist data for the containers to volume mount from the host.

    mysql: The database files for MariaDB
    wordpress: The WordPress media files
    logs/nginx: The Nginx log files (error.log, access.log)
    certs: SSL certificate files (LetsEncrypt)
    certs-data: SSL challenge/response area (LetsEncrypt)
    
    attention: mysql directory must be empty for create databasess!! if this directory have any data your databasess not created.
From the top level of the cloned repository, create the directories that will be used for managing the data on the host.

$ cd wordpress-nginx-docker/
# mkdir -p certs/ certs-data/ nginx/ logs/nginx/ mysql/ wordpress/

HTTP

If you plan to run your WordPress site over http on port 80, then do the following.

    Change the name of nginx/wordpress.conf.example to nginx/wordpress.conf
    Update the DOMAIN_NAME in nginx/wordpress.conf to be that of your domain
    Run $ docker-compose up -d
    Navigate to http://DOMAIN_NAME in a browser where DOMAIN_NAME is the name of your site
    we replace DOMAIN_NAME with localhost

HTTPS with SSL Certificates

If you plan to run your WordPress site over https on port 443, then do the following.

Choose a method for SSL certificates

    Let's Encrypt

    If you plan on using SSL certificates from Let's Encrypt it is important that your public domain is already DNS registered and publically reachable.

    Run: ./letsencrypt/letsencrypt-init.sh DOMAIN_NAME, where DOMAIN_NAME is the publicly registered domain name of your host to generate your initial certificate. (Information about updating your Let's Encrypt certificate can be found further down in this document)

$ ./letsencrypt-init.sh example.com
mysql uses an image, skipping
wordpress uses an image, skipping
nginx uses an image, skipping
Creating mysql ...
Creating mysql ... done
Creating wordpress ...
Creating wordpress ... done
Creating nginx ...
Creating nginx ... done
Reloading nginx: nginx.
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): mjstealey@gmail.com

-------------------------------------------------------------------------------
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
in order to register with the ACME server at
https://acme-v01.api.letsencrypt.org/directory
-------------------------------------------------------------------------------
(A)gree/(C)ancel: a

-------------------------------------------------------------------------------
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about EFF and
our work to encrypt the web, protect its users and defend digital rights.
-------------------------------------------------------------------------------
(Y)es/(N)o: y
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for example.com
http-01 challenge for www.example.com
Using the webroot path /data/letsencrypt for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
    ssl                       on;
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/example.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/example.com/privkey.pem
   Your cert will expire on 2018-02-06. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

Stopping nginx     ... done
Stopping wordpress ... done
Stopping mysql     ... done
Going to remove nginx, wordpress, mysql
Removing nginx     ... done
Removing wordpress ... done
Removing mysql     ... done
INFO: update the nginx/wordpress_ssl.conf file
-  4:   server_name example.com;
- 19:   server_name               example.com www.example.com;
- 46:   ssl_certificate           /etc/letsencrypt/live/example.com/fullchain.pem;
- 47:   ssl_certificate_key       /etc/letsencrypt/live/example.com/privkey.pem;
- 48:   ssl_trusted_certificate   /etc/letsencrypt/live/example.com/chain.pem;

    Self signed

    If you plan on using self signed SSL certificates, run: ./letsencrypt/self-signed-init.sh DOMAIN_NAME, where DOMAIN_NAME is the CN you want to assign to the host (commonly localhost).

$ cd letsencrypt/
$ ./self-signed-init.sh localhost
INFO: making certs directory
Generating a 4096 bit RSA private key
................................................................................................................................................................................................................................................++
....................................................++
writing new private key to 'key.pem'
-----
INFO: update the nginx/wordpress_ssl.conf file
-  4:   server_name localhost;
- 19:   server_name               localhost www.localhost;
- 46:   ssl_certificate           /etc/letsencrypt/live/localhost/cert.pem;
- 47:   ssl_certificate_key       /etc/letsencrypt/live/localhost/privkey.pem;
- 48:   #ssl_trusted_certificate   /etc/letsencrypt/live/DOMAIN_NAME/chain.pem; <-- COMMENT OUT OR REMOVE

    Bring your own

    If you plan to use pre-existing certificates you will need to update the nginx/wordpress_ssl.conf file with the appropriate settings to the kind of certificates you have.

Finally

    Change the name of nginx/wordpress_ssl.conf.example to nginx/wordpress_ssl.conf
    Update the DOMAIN_NAME in nginx/wordpress_ssl.conf to be that of your domain
    Run $ docker-compose up -d
    Navigate to https://DOMAIN_NAME in a browser where DOMAIN_NAME is the name of your site
    we replace DOMAIN_NAME with localhost
