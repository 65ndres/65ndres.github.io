---
title: Deploy your Docker Rails 7.1 app to AWS
date: 2024-05-02 12:00:00 -500
categories: [programming]
tags: [tutorial,docker,rails,aws,nginx]
---

Rails 7.1 came out recently and it now comes with its own Dockerfile, Ill be going over how easy it is to use it by deploying to AWS.

## Game Plan

* Launch EC2 instance from AWS
* Get our codebase into the new EC2 instance
* Build app image
* Configure NginX

### Launch EC2 instance from AWS

First we are gonna launch a EC2 instance from AWS, for this we will go to the AWS dashboard
and search for "EC2", click on the first result and look for the option to launch a new one.
We are gonna choose the `Ubuntu` image for its size we will go with their free tier option `t2.micro`. We are gonna create a new key pair choosing `RSA` and save it a a `.pem` file (I will save it as `twitter-bot.pem`) and finally we are going to allow SSH, HTTP and HTTPS connections from the internet.

#### Connect to instance via ssh

Once our instance state shows as `Running` inside our EC2 dashboard we are going to connect to it via SSH using the `twitter-bot.pem` file we downloaded earlier.

Before using the `.pem` file we need to limit it permissions. When first download it, the file will have permission code `0644` which are too broad so are gonna change it to `400` since we are not going to be editing the file, just reading.

```bash
sudo chmod 400 twitter-bot.pem
```
Now we can connect to our EC2 running:

```bash
ssh ubuntu@<your-instance-public-ip> -i twitter-bot.pem
```

#### Update and install Docker

Once inside our instace we will run `sudo apt update` to update any outdated software and install `Docker` using the Docker official documentation:

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```
and finally install its supporting packages.

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify that the Docker Engine installation is successful by running the hello-world image.

``` bash
sudo docker run hello-world
```

### Get our codebase into the new EC2 instance

To get our code to the instance we need to use Github, so given the app is already in Github we are gonna set the configuration to download our codebase into our instance.

Inside our instance generate a ssh key using:

``` bash
ssh-keygen
```
Dont set any passphrase, just click enter a couple of times.
Now we need to copy the generated key into our Github's SSH and GPG setting.
copy using something like: `cat /home/ubuntu/.ssh/id_rsa.pub`

Once thats completed we should be abel to clone using:

``` bash
git clone git@github.com:<URUSERNAME>/<PROJECTNAME>
```
### Build app image

Now that our code has been downloaded we are gonna build the image and then run it.
We build using
```bash
sudo docker build -t twitter-bot .
```
create a volume
```bash
sudo docker volume create twitter-bot-storage
```
and run using:
```bash
docker run --rm -it -v twitter-bot-storage:/rails/storage -p 3000:3000 --env RAILS_MASTER_KEY="URMASTERKEY" twitter-bot
```
To verify our app is running as it should we need to modify the inbound rules of our security group in AWS. From the instance details find the security group and click on it, click on "Edit inbound rules" and add a new rule of type "Custom TCP", port `3000`, source `0.0.0.0/0` and save.

Now we should be able to visit our site at `<your-instance-public-ip>:3000`.

### Configure Nginx

Having to especify the port number everytime we want to visit our site is a lot of work and unconventional, to fix this we are gonna add Nginx and configure it so any trafic going to our default web port (80) get redirected to where our Rails app is running.

First we are gonna install NginX doing

```bash
sudo apt intall nginx
```

Next we create the configuration file

```bash
sudo nano /etc/nginx/sites-available/twitter-bot
```
and paste

```bash
server {
    listen 80;
    server_name _;
    location / {
        proxy_pass https://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

    } 
}
```
we are gonna create the symlink we need

```bash
sudo ln -s /etc/nginx/sites-available/twitter-bot /etc/nginx/sites-enabled/
```
and finally we are gonna restart Nginx so our changes take effect

```bash
sudo service nginx restart
```

Done.