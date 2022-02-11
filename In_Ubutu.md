# Email_Configuration
# In AWS
🔴 2 Instance (1 Server and 1 client);

🔴 2 Elastic IPs (for each instance).

# In Termius

## Server

◻️ `sudo hostnamectl set-hostname example.example.com` ;

◻️ `sudo su -`;

### Update and Upgrade your Server

◻️ `apt update && apt upgrade` ;

### Installing Postfix and Dovecot

◻️ `apt -y install postfix postfix-doc dovecot-imapd dovecot-pop3d sasl2-bin` ;

◻️ `dpkg-reconfigure postfix` ;

Configuration of `dpkg-reconfigure postfix` :

```
2
example.com
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

◻️ `login user` ;

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
