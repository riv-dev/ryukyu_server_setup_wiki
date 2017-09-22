# Ryukyu Social Server Setup

This tutorial assumes you have a Linux server.

### Basic server setup

The first thing you need to do is set bare minimum settings for server security.  Here I will summarize quickly.  The original tutorial is here: [https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)

1. Remotely log into the server with username "root" and root password.  To do this, type the following on the command line (use Terminal program on your Macbook).  You should get prompts to enter the password.
```
ssh root@server_ip_address
```

1a. If you forgot your root password, you can reset it by logging into your Digital Ocean account, selecting your droplet, select "access", and reset root password.
 
 <img src="root_pw_reset.png" />
