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
### Test remote log in with "riv" 
1. In case this step does not work, do not exit terminal that is logged in with "root".  
2. Start a new terminal and type the following:
```
ssh riv@server_ip_address
```
3. If everything is right, you should now be logged into your server as user "riv".  You may now exit the terminal that was logged in as "root".

### Set up basic Firewall

1.  We will want to start a firewall to block all ports, and open only the ports that we need.  We can list the current ports that the fire wall allows by typing the following:
```
sudo ufw app list
```
2.  If it is not already in the list, we want to open "SSH" port so that we can log into our server with ssh as before.  Type the following:
```
sudo ufw allow OpenSSH
```
3. Now enable the firewall:
```
sudo ufw enable
```
4. View the status of the firewall by typing the following:
```
sudo ufw status
```

## Firewall Setup for Ryukyu Social
Below is a quick summary of firewall settings for our server.  For detailed information on how to setup the firewall visit [https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

1. Allow **http** and **https** connections to our server.
```
sudo ufw allow http
```
```
sudo ufw allow https
```
2. View the firewall status. That is it, we do not wish to allow any other kinds of connections to our Ryukyu Social server.
```
sudo ufw status
```

## Setup Swap Memory
Ryukyu Social uses more than 512mb of ram.  This could be a problem if our server only has 512mb of ram, the app will crash.  In order to get around this, we can add "virtual" ram, AKA swap memory.  What we are doing is setting up our server to use our hard-disk memory (as virtual ram) when it runs out of actual ram memory.  Virtual RAM is a lot slower than actual RAM (because we are using the hard-disk), however the advantage is our application will not crash if it runs out of actual RAM.  The original tutorial can be found here: [https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04)

1. Check if your server already has swap memory setup. If there is no output, it means there is no swap memory.
```
sudo swapon --show
```
2. Verify there is no active swap with:
```
free -h
```
```
Output
              total        used        free      shared  buff/cache   available
Mem:           488M         36M        104M        652K        348M        426M
Swap:            0B          0B          0B
```
3. Check how much space you currently have on your hard-disk:
```
df -h
```
```
Output
Filesystem      Size  Used Avail Use% Mounted on
udev            238M     0  238M   0% /dev
tmpfs            49M  624K   49M   2% /run
**/dev/vda1        20G  1.1G   18G   6% /**
tmpfs           245M     0  245M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           245M     0  245M   0% /sys/fs/cgroup
tmpfs            49M     0   49M   0% /run/user/1001
```


