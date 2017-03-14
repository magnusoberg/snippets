#Postfix
##Configuration files
###/etc/postfix/generic
This does rewrites on _outbound_ messages.
```
/etc/postfix/generic:
    root@localhost.local    monitor@mydomain.com

postmap /etc/postfix/generic
```

