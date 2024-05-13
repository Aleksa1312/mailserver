# Docker Self-Host Mailserver

This is a fully self-hostable mailserver with SSL.
As the name applies it is supposed to be run in a docker container, and it is built on an open source project **docker-mailserver**.

## Getting started

This git repo contains a template **docker-compose.yml** file, that is designed to start your mailserver with one command.
The only hussle you will have to do is obtain an SSL certificate, which can be done for free thanks to LetsEncrypt and Certbot.
Every single step of the process is going to be documented below.

## Prerequisites

To run this mailserver you are going to need a few things:

- Access to VPS or your machine that is connected to the web with a static IP address.
- Domain name and access to editing DNS records for the said domain.
- Docker and Docker Compose installed on said machine or VPS.

## References

If you are having trouble with prerequisites you can refer to these tutorials to help set it up.

1. For VPS I've had good experience with [Hostinger](https://www.hostinger.com/vps-hosting), but any other VPS service that you might like will do the job.
2. [Installing Docker on Ubuntu.](https://docs.docker.com/engine/install/ubuntu/)
3. Purchese a domain from any platform (GoDaddy, Squarespace, Hostinger, etc.).

## DNS Setup

You are going to have to add the following DNS records to your domain.

Watch out:

- **Change every example.com with your domain!**
- **Change every 123.123.123.123 with IP address of your VPS!**

> If the domain is **mail.example.com**, replace it with **mail.yourdomain.com**.

**DNS Records**:
| Host | Type | Priority | Data |
| --- | --- | --- | --- |
| mail.example.com | MX | 10 | 123.123.123.123 |
| mail | A | N/A | 123.123.123.123 |
| 123.123.123.123 | PTR | N/A | mail.example.com |

> **If the priority is set to `N/A`, you should leave it blank when creating records.**

## Installation

### 1. Connect to your VPS.

You can skip this step if you are on your local machine and just run the commands after it normally.

```sh
ssh root@123.123.123.123
```

> The next steps are going to need you to have **Git**, **Docker** and **Docker Compose** installed.
>> If you need help you can refer to [references](#references) step.

### 2. Get a SSL certificate for your domain.

This is important since we do not want third parties reading contents of our emails.
To do this we are going to install Certbot via snap.

#### Install snap package installer.

This is the easiest method to install Certbot and get SSL certificate.

```sh
sudo apt update
sudo apt install snapd
```

#### Remove conflicting versions of Certbot (if installed).

```sh
sudo apt-get remove certbot
```

#### Install Certbot using snap.

```sh
sudo snap install --classic certbot

sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

#### Acquire an SSL certificate.

```sh
sudo certbot certonly --standalone
```

### 3. Clone and setup this repo.

```sh
git clone https://github.com/Aleksa1312/mailserver.git
```

#### Change docker-compose.yml settings.

You need to change the hostname setting in your docker compose.
We are going to use nano as our text editor but you can use vim or your preferred choice.

```sh
cd mailserver

nano docker-compose.yml
```

#### Change hostname.

If you are using nano navigate with arrows to this part of the config, edit the hostname to your domain and press `ctrl+o` and `enter` to save the file and finally exit with `ctrl+x`.

```yml
services:
  mailserver:
    hostname: mail.yourdomain.com
```

### 3. Start the mailserver.

To start the mailserver we are going to use docker compose.
For testing purposes only we are running our docker compose without `-d` tag because we want to see in the logs if everything is running correctly.

Make sure you are inside the repo and run the following command to build a docker image and start the container.

```sh
docker compose up --build
```

If everything is started correctly and the mailserver has not crashed (exited with code 1), you should see this message logged into your console `Dovecot needs at least on mail account to start`.
Now stop the container by pressing `ctrl+c` and start it again with the same commmand but now add `-d` tag so it runs in the background and we have access to the console. 

```sh
docker compose up --build -d
```

### 4. Create a mail account.

This creates a mail account which can be used to send and receive messages using SMTP protocol.
**You can create as many or as few accounts as you'd like, but you need at least one to keep the mailserver running.**

```sh
docker exec -ti mailserver setup email add user@yourdomain.com 
```

After this you will be prompted to enter a password.
Use something secure as any person with the password will be able to access your emails on this mail account.

### 5. Connect to the mail account.

After successfully runnning your server and creating a mail account, you will want to connect to it using an email client.
You can use Gmail, Outlook or any other client but I recommend using **Mozilla Thunderbird**.

Connecting to your account is as easy as:

- Install thunderbird.
- Log in with credentials.

Credentials:

- Email: `user@yourdomain.com`
- Password: `the-secret-password-you-entered-when-creating-a-mail-account`

## Final Words

If you like this self-hosing mailserver guide please give it a star.
Also consider donating to the underlaying technologies powering it, as they are what make this possible.

Their links are below:

- [Docker-Mailserver](https://github.com/docker-mailserver/docker-mailserver)
- [Docker](https://www.docker.com/)
