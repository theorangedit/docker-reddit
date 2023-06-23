## Docker Reddit

This repository contains the docker files needed to run [open source reddit](https://github.com/theorangedit/open-source-reddit) code.

## Docker installation

The instructions assume that you are using Ubuntu 22.04 LTS as your Docker host. Run the following commands as a regular user with sudo privileges. 

### Prepare host OS

    $ sudo apt update && sudo apt -y upgrade
    $ sudo apt install nginx docker docker-compose git
    $ sudo usermod -a -G docker $USER
    $ sudo systemctl enable docker && sudo systemctl restart docker
    # or logout and login again to reload your group permissions
    $ exec su -l $USER

Optionally fix any DNS timeout issues, for virtual machines (feel free to substitute your own nameservers here)

    $ sudo apt install resolvconf
    $ sudo sed -i "1i nameserver 8.8.4.4" /etc/resolv.conf && sudo sed -i "1i nameserver 8.8.8.8" /etc/resolv.conf
    $ sudo systemctl enable resolvconf.service && sudo systemctl restart resolvconf.service

### Install reddit open source

    $ cd ~
    $ git clone https://github.com/theorangedit/docker-reddit.git
    $ mv saidit/docker-compose.yml . && cp -r saidit/docker .
    $ rm -rf saidit
    $ docker-compose up -d

### Configure host OS's nginx

    $ sudo cp docker/host/nginx/reddit-ssl /etc/nginx/sites-available/reddit-ssl
    $ sudo ln -s /etc/nginx/sites-available/reddit-ssl /etc/nginx/sites-enabled/reddit-ssl
    $ sudo openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout /etc/ssl/private/key.pem -out /etc/ssl/certs/cert.pem
    $ sudo openssl dhparam -out /etc/ssl/dhparam.pem 2048
    $ sudo nginx -t
    $ sudo service nginx restart