# Postfix
## Configuration files
The `/etc/postfix/generic` file rewrites the *From* headers on _outbound_ messages so that it can
be replied to if necessary. It also makes it appear more legitimate, and may
even be used to correctly identify which host is sending the message.

The `/etc/postfix/aliases` file defines aliases that are resolved so that mail
that would usually go only to root will now actually be forwarded on to an
actual email address.

```
# Ensure mail uses a legitimate 'From' address.
/etc/postfix/generic:
root@localhost.local    mywebserver@mydomain.com

# Ensure mail to root is seen by someone.
/etc/postfix/aliases:
root:   myself@example.com

postmap /etc/postfix/generic
postmap /etc/postfix/aliases
```

## Installing Postfix with SASL support
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
After this, ensure to hash the `sasl_passwd` file and secure it so that only
root can read it's contents.
```
postmap /etc/postfix/sasl_passwd
chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
postfix reload && tail -fn50 /var/log/mail.log
```

## Test that it works

Once setup, you should test to ensure that all works as intended
```
echo "Test mail from postfix" | mail -s "Test Postfix" you@example.com
```
You should now receive an authenticated (authorized) email from the username
defined in `sasl_passwd` file. Check the logs in `/var/log/mail.log` for more
details.
