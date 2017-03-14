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

Then add the following lines to `/etc/postfix/main.cf`

```
# STARTTLS options
smtp_tls_CAfile = /etc/postfix/cacert.pem

# SASL Settings
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```
Add username and password to the SASL file `/etc/postfix/sasl_passwd`
```
[smtp.gmail.com]:587    username@gmail.com:PASSWORD
```
```
postmap /etc/postfix/sasl_passwd
chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
postfix reload && tail -fn50 /var/log/mail.log
```
##Test that it works
Once setup, you should test to ensure that all works as intended
```
echo "Test mail from postfix" | mail -s "Test Postfix" you@example.com
```


