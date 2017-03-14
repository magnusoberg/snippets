#Postfix
##Configuration files
###/etc/postfix/generic
This does rewrites of the *From* headers on _outbound_ messages so that it can
be replied to if necessary (it also makes it appear more legitimate, and may
even be used to correctly identify which host is sending the message).
```
/etc/postfix/generic:
    root@localhost.local    mywebserver@mydomain.com

postmap /etc/postfix/generic
```
##Installing Postfix with SASL support
```
sudo apt-get install postfix mailutils libsasl2-2 ca-certificates libsasl2-modules
```
If Postfix is installed for the first time, select _Internet Site_ when asked.
`vim /etc/postfix/main.cf`
```
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_CAfile = /etc/postfix/cacert.pem
smtp_use_tls = yes
```

