# Email_Configuration
# In AWS
🔴 In "central.inova.pt" after having done RAID-5 and NIS and NFS;

# In Termius

## "central.inova.pt"

◻️ `sudo su -`;

### Update and Upgrade your Server

◻️ `apt update && apt upgrade` ;

### Install Postfix and Dovecot

◻️ `apt -y install postfix postfix-doc dovecot-imapd dovecot-pop3d sasl2-bin` ;

◻️ `dpkg-reconfigure postfix` ;

Configuration of `dpkg-reconfigure postfix` :

```
2
inova.pt
ubuntu
do not modify
no
erase everything
0
+
Ipv4
```
◻️ Add in `nano /etc/postfix/sasl/smtpd.conf` :
```
pwcheck_method: saslauthd
mech_list: PLAIN LOGIN
```

◻️ Modify in `nano /etc/default/saslauthd` :
```
START=yes
OPTIONS="-c -m /var/spool/postfix/var/run/saslauthd"
```
◻️ `postconf -e 'home_mailbox = Maildir/'` ;

◻️ In `nano /etc/dovecot/conf.d/10-auth.conf` Uncomment and change from "yes" to "no":

```
disable_plaintext_auth = no
```
◻️ `nano /etc/dovecot/conf.d/10-mail.conf`

Uncomment:
```
mail_location = maildir:~/Maildir
```

Comment:
```
#mail_location = mbox:~/mail:INBOX=/var/mail/%u
```
__________________________________________________________
### When a user is created, the maildir folder is automatically created:

◻️ `cd /etc/skel/` ;

◻️ `maildirmake.dovecot Maildir` .

### When the user is already created:

◻️ `login sales` / `login marketing`;

◻️ `maildirmake.dovecot Maildir` ;

__________________________________________________________

◻️ `adduser postfix sasl` ;

◻️ `adduser dovecot sasl` ;

◻️ `nano /etc/postfix/master.cf` ;

Uncomment 1st group:
```
submission inet n       -       y       -       -       smtpd
-o syslog_name=postfix/submission
-o smtpd_tls_security_level=encrypt
-o smtpd_sasl_auth_enable=yes
```
Add under "-o smtpd_sasl_auth_enable=yes":
```
-o smtpd_client_restrictions=permit_sasl_authenticated,reject
```
Uncomment 2nd group:
```
smtps     inet  n       -       y       -       -       smtpd
-o syslog_name=postfix/smtps
-o smtpd_tls_wrappermode=yes
-o smtpd_sasl_auth_enable=yes
```
Add under "-o smtpd_sasl_auth_enable=yes":
```
-o smtpd_client_restrictions=permit_sasl_authenticated,reject
```
◻️ `nano /etc/dovecot/conf.d/10-master.conf` ;

To add:
```
# Postfix smtp-auth
unix_listener /var/spool/postfix/private/auth {
mode = 0666
user = postfix
group = postfix
}
```
◻️ `systemctl restart saslauthd.service postfix dovecot`

__________________________________________________________
### To see if all ports are on:

◻️ `ss -tulnp | grep master` ;
- 25

- 465

- 587

◻️ `ss -tulnp | grep dovecot` .
- 110

- 143

- 993

- 995
__________________________________________________________

If you don't want to change the certificates that already come, leave them as they are. If you want to change the default certificates, you will have to go to "control.inova.pt" or "easy-rsa" and you will have to create them, for example:

◻️ `./easyrsa build-server-full example.com nopass` ;

◻️ `cp /etc/easy-rsa/pki/ca.crt /etc/ssl/certs/` ;

◻️ `cp /etc/easy-rsa/pki/issued/example.com.crt /etc/ssl/certs/` ;

◻️ `cp /etc/easy-rsa/pki/private/example.com.key /etc/ssl/private/` ;

◻️ `cd /etc/ssl/private/` ;

◻️ `chown --reference=ssl-cert-snakeoil.key example.com.key` .
__________________________________________________________
After creating the certificates:

◻️ `nano /etc/dovecot/conf.d/10-ssl.conf` ;

Change from "no" to "yes":
```
ssl = yes
```
And uncomment and modify:
```
ssl_cert = </etc/ssl/certs/example.com.crt
ssl_key = </etc/ssl/private/example.com.key
```
◻️ `nano /etc/postfix/main.cf` ;

Change:
```
smtpd_tls_cert_file=/etc/ssl/certs/example.com.crt
smtpd_tls_key_file=/etc/ssl/private/example.com.key
```
◻️ `systemctl restart dovecot` .
__________________________________________________________
## Client

### Installation of the graphical interface with thunderbird:

Access the following link to install the graphical environment [Graphical_Interface](https://github.com/JoseCarvalho1026/Graphical_Interface) and pay attention to the following point.

◻️ Add in the command `sudo apt install -y xrdp chromium-browser` `thunderbird` .

### Add the users:

◻️ `adduser user` ;

◻️ `cp /home/ubuntu/.xsession /home/user/.xsession` ;

◻️ `chown user:user /home/user/.xsession` .
