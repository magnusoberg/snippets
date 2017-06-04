 Setup for IRC using WeeChat

```
/set irc.server_default.username {nick}
/set irc.server_default.nicks {nick1,nick2,nick3}

/nick {nick}
/msg nickserv register {password}
```

alternatively, if you need a email as well, as you do for freenode:
`/msg nickserv register {password} {email}`

follow instructions in email. Should be something like below:
`/msg NickServ VERIFY REGISTER {nick} <challengestring>`

Now, add alternate nick for when you're away. Note the trailing underscore.
Simply select a new nick, identify it to your main (using same password) and
then group it once identified.
```
/nick {nick}
/msg nickserv identify {main} {password}
/msg nickserv group
```

Once connecting, you are not identified and may risk losing your nick if
somebody else identifies using that same nick handle. This should of course be
much less of a risk if you've actually registered your nick as there will be a
timeout period and they will have to.

Identify yourself to the server:
/msg nickserv identify {yourpassword}

Now, you could do this everytime you connected. Nobody else could use your nick
after you've registered it anyway, but this would mean that you would have to
enter your password every time.

# Authenticate using a self-signed TLS certificate
```
mkdir ~/.weechat/cert
cd ~/.weechat/cert
openssl req -x509 -new -newkey rsa:2048 -sha256 -days 1000 -nodes -out freenode.pem -keyout freenode.pem

# if interested, view the certificate sha1sum fingerprint (two different ways)
# first the manual way:
openssl x509 -in freenode.pem -outform DER |shasum -b

# secondly, the official way:
openssl x509 -in freenode.pem -noout -fingerprint
```

Then setup weechat to use it:
```
/set irc.server.freenode.addresses chat.freenode.net/6697
/set irc.server.freenode.ssl on
/set irc.server.freenode.ssl_verify on
/set irc.server.freenode.ssl_cert %h/cert/freenode.pem
/set irc.server.freenode.sasl_mechanism external
```

Then reconnect to send your certificate to the server. Authenticate/identify
manually to ensure you are you, and check to see if your fingerprint is now
visible in the /whois command. It should be. Once it is there, add it to your
nick for future reference.
```
/reconnect
/msg nickserv identify ****
/whois {nick}
/msg nickserv cert add
```
You should get a response that the certificate fingerprint has been added to
your account. From now, you should not need to authenticate using a password
anymore.

# Use Elliptic Curve key intead for authentication
First, let's generate an EC key.
```
openssl ecparam -genkey -name prime256v1 > ~/.weechat/cert/ecdsa.pem
```

And, then let's take the compressed form of the public key and copy it into your
clipboard
```
openssl ec -in ~/.weechat/ecdsa.pem -text -noout -conv_form compressed 2>/dev/null|
sed '/pub:/,/ASN1 OID:/!d;//d'|
xxd -r -p|base64
```
Connect to freenode and identify yourself manually at first. Once authenticated,
go ahead and add your public key to your nick.
```
/msg nickserv set pubkey {base64 public key}
```
Then let's configure Weechat to use the new key in the SASL settings and then
reconnect to the server to verify it is working.
```
/set irc.server.freenode.sasl_mechanism ecdsa-nist256p-challenge
/set irc.server.freenode.sasl_username {nick}
/set irc.server.freenode.sasl_key "%h/cert/ecdsa.pem"
/reconnect freenode
```
