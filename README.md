# Ryukyu Social Server Setup

This tutorial assumes you have a Linux server.

## Basic server setup

The first thing you need to do is set bare minimum settings for server security.  Here I will summarize quickly.  The original tutorial is here: [https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)

### Log in as Root

1. **Remotely log into the server with username "root" and root password.**  To do this, type the following on the command line (use Terminal program on your Macbook).  You should get prompts to enter the password.
```
ssh root@server_ip_address
```
(optional) If you forgot your root password, you can reset it by logging into your Digital Ocean account, selecting your droplet, select "access", and reset root password.
<img src="root_pw_reset.png" />

### Create a new user

1. You should now be logged into your server.  Everything you type now is executing on your remote server.  **Create a new user for your server.**  In the future, you do not want to log in as "root", it is not good practice to use the "root" account.  Choose a username, for example "riv".  Type in a good password, and accept all defaults by pressing "Enter".
```
adduser riv
```

### Root Privileges

1. **This new user will be the server admin.**  Give your new user sudo privileges for performing administrative tasks.
```
usermod -aG sudo riv
```

### Add Public Key Authentication (Optional but recommended)

1. **We will now setup so we can log into the server with ssh-keys instead of using a password.**  It is insecure to use a password to log into your server because if anyone finds out your password, anyone can log into your server from anywhere in the world. Check if you already have a local ssh-key on your local computer (Macbook).  Start a new Terminal and type the following on the command line:
```
ls ~/.ssh
```
If you see "id_rsa" and "id_rsa.pub" it means you already have an ssh-key, you may skip step 5.  If you do not, then you don't have one yet, follow step 5.

2. **Create an ssh-key on your local computer.**  Start a new Terminal and type and execute the following on your local machine.  Accept all defaults by pressing enter.  You do not need to enter a passphrase, leave empty and hit enter.
```
ssh-keygen
```

3. **Copy your local ssh-key to your clipboard** so that you can add it to your server.  To do this first type:
```
cat ~/.ssh/id_rsa.pub
```

Then select the output and copy it to your clipboard.  For example you will see:
```
id_rsa.pub contents
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBGTO0tsVejssuaYR5R3Y/i73SppJAhme1dH7W2c47d4gOqB4izP0+fRLfvbz/tnXFz4iOP/H6eCV05hqUhF+KYRxt9Y8tVMrpDZR2l75o6+xSbUOMu6xN+uVF0T9XzKcxmzTmnV7Na5up3QM3DoSRYX/EP3utr2+zAqpJIfKPLdA74w7g56oYWI9blpnpzxkEd3edVJOivUkpZ4JoenWManvIaSdMTJXMy3MtlQhva+j9CgguyVbUkdzK9KKEuah+pFZvaugtebsU+bllPTB0nlXGIJk98Ie9ZtxuY3nCKneB+KjKiXrAvXUPCI9mWkYS/1rggpFmu3HbXBnWSUdf localuser@machine.local
```
4. **We will now add your ssh-key to the server.** Go back to the terminal where you are logged into your server. First, switch to your "riv" user account
```
su - riv
```

5.  You will now be in the home directory for "riv" user.  Make a .ssh folder and set permissions for it.
```
mkdir ~/.ssh
chmod 700 ~/.ssh
```

6.  **Create "authorized_keys" file**.  Use "vim" or "nano" to create the file.  For example with nano:
```
nano ~/.ssh/authorized_keys
```
Save and exit by typing "CTRL X", press "y" then "Enter".

7. Set permissions for the new file:
```
chmod 600 ~/.ssh/authorized_keys
```
8. Switch back to the "root" user by typing:
```
exit
```

9. Repeat steps 1-8 for other computers that you wish to allow access to the server.

### Disable Password Authentication (Optional but recommended)

1.  **We will now disable password log in** so that users can only log into the server if they have a ssh-key pair that matches with the server. (done in the previous step).  To do this first open the following file with vim or nano (make sure you are logged into your server as "root"):
```
sudo nano /etc/ssh/sshd_config
```

2. Find the following line and set it to "no"
```
PasswordAuthentication no
```

3. Save and exit the text editor.  If you are using nano, save and exit by typing "CTRL X", "y", then "Enter".

4. Restart the ssh daemon:
```
sudo systemctl reload sshd
```


