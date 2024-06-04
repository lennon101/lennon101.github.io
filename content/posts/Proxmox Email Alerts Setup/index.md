+++
title = 'Proxmox Email Alerts Setup'
date = 2024-06-04T13:09:00+10:00
draft = false
categories = ['IT']
tags = ['how-To', 'proxmox']
+++

## 1. Install mailutils 
SSH into proxmox node and become root user. Run the following commands to download extra software dependencies we'll need.

```
apt update
apt install -y libsasl2-modules mailutils
```

## 2. Enable 2FA for the gmail account.
This is the account that will be used by proxmox to send emails. Go to [security settings](https://myaccount.google.com/security)

## 3. Create app password for the account.

1. Go to [App Passwords](https://security.google.com/settings/security/apppasswords)
2. Select app: `Mail`
3. Select device: `Other`
4. Type in: `Proxmox` or whatever you want here
  
## 4. Setup gmail credentials  
Write gmail credentials to file and hash it. Again, make sure you are root.

```
echo "smtp.gmail.com youremail@gmail.com:yourpassword" > /etc/postfix/sasl_passwd

# chmod u=rw
chmod 600 /etc/postfix/sasl_passwd

# generate /etc/postfix/sasl_passwd.db
postmap hash:/etc/postfix/sasl_passwd
```


## 5. Proxmox config 
Open the Postfix configuration file with editor of your choice.

```
nano /etc/postfix/main.cf
```

## 6. Edit main.cf 
Append the following to the end of the file:

```
relayhost = smtp.gmail.com:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/Entrust_Root_Certification_Authority.pem
smtp_tls_session_cache_database = btree:/var/lib/postfix/smtp_tls_session_cache
smtp_tls_session_cache_timeout = 3600s

```

**IMPORTANT**: Comment out the existing line containing just `relayhost=` since we are using this key in our configuration we just pasted in.

{{< figure src="/relayhost.png" width="1000" >}}

## 7. Reload postfix
    ```bash
    postfix reload
    ```

## 8. Test it worked 
Test to make sure everything is working with: 

```
echo "sample message" | mail -s "sample subject" anotheremail@gmail.com
```


Credit: [tomdaley92](https://gist.github.com/tomdaley92) --> [original source](https://gist.github.com/tomdaley92/9315b9326d4589c9652ce0307c9c38a3)