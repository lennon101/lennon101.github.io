+++
title = 'Securing Your Nas Against Ransomware'
date = 2024-05-14T10:39:36+10:00
draft = false
categories = ['IT']
tags = ['how-to']
+++

## Threat
I'm on a [synology sub-reddit](https://www.reddit.com/r/synology/) page and I see posts like this every couple of months and it terrifies me:

{{< figure src="/attack.png" width="1000" >}}

This is an example of Ransomware. 
Ransomware is a type of malicious software designed to block access to a computer system or files until a sum of money, or "ransom," is paid. It typically works by encrypting the victim's files or locking them behind a password, rendering them inaccessible. Once the ransomware is activated, the victim is presented with instructions on how to pay the ransom, often in cryptocurrency, in exchange for a decryption key or password. Ransomware attacks can be devastating for individuals and organizations, causing data loss, financial harm, and disrupting normal operations. Cybersecurity experts recommend regularly backing up data, maintaining updated software, and exercising caution when clicking on links or downloading files to prevent falling victim to ransomware attacks.

## Mitigation 
So, in case you havn't already, here's a list I've put together that I **highly** recommend you follow ASAP: 

1. Secure your network:
    - Enable and make use of the firewall on your router.
    - Port-forward the bare minimum on your router. Make use of reverse proxies through port 443 etc if needed.
2. Disable/deactivate the default admin account.
    - Create a second admin account but don't use a username that references it's admin status eg `admin-user` or `admin-acct` 
3. Make sure you use a User account when accessing your nas. 
    - Do not use your new admin account for any other use except admin tasks.
4. Enable 2FA.
5. Use very secure passwords:
    - Never use the same password twice! 
    - Use tools like [1Password](https://1password.com/) or [Keeper](https://www.keepersecurity.com/) to generate unique passwords and store them safely 
6. Instead of [Synology QuickConnect](https://quickconnect.to/) or QNAP's [EasyConnect](https://www.qnap.com/en-au/software/myqnapcloud) use a VPN or [Tailscale](https://tailscale.com/).
7. Enable the NAS firewall.
8. Force 2FA for all other NAS users.
9. Check every password requirement.
10. Set login attempt timeouts to something like 2-3 failed logins = suspend for 4 hours or something.
11. Don’t reuse passwords.
12. Consider using AWS Glacier backups (or review which cold storage solution is best for you). It’s painfully slow but really cheap.