# configuring_nginx_on_linux
A collection of instructions to configure nginx from LinkedIn course "[learning-nginx](https://www.linkedin.com/learning/learning-nginx)"

## Setup vagrant virtualbox for Ubuntu18.04
1. Create a directory `lerning_nginx`.
2. cd into this directory.
3. Create a Vagrantfile.
```
touch Vagrantfile
```
4. Add the following content to the Vagrantfile.
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Make sure this ip address does
# not conflict with you LAN.
guest_ip  = "192.168.2.3"

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.network "private_network", ip: guest_ip
  config.vm.synced_folder ".", "/vagrant"
  # Forward http traffic on guest os to host os on port 8000
  config.vm.network "forwarded_port", guest: 80, host: 8000, host_ip: "127.0.0.1"
end

puts "-------------------------------------------------"
puts "Demo URL : http://#{guest_ip}"
puts "-------------------------------------------------"
```

5. Launch vagrant. It may take a while to pull and setup Ubuntu18.04 image.
```
vagrant up

-------------------------------------------------
Demo URL : http://192.168.2.3
-------------------------------------------------
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Checking if box 'bento/ubuntu-18.04' is up to date...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 80 (guest) => 8000 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Remote connection disconnect. Retrying...
    default: Warning: Remote connection disconnect. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default: 
    default: Guest Additions Version: 6.0.8
    default: VirtualBox Version: 5.1
==> default: Configuring and enabling network interfaces...
...
```

You may see the following error in the end. Ignore it for now!
```
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!

/sbin/ifdown eth1 2> /dev/null

Stdout from the command:



Stderr from the command:

mesg: ttyname failed: Inappropriate ioctl for device
```
6. ssh into vagrant box.
```
vagrant ssh

-------------------------------------------------
Demo URL : http://192.168.2.3
-------------------------------------------------
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-60-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 System information disabled due to load higher than 1.0

 * Congrats to the Kubernetes community on 1.16 beta 1! Now available
   in MicroK8s for evaluation and testing, with upgrades to RC and GA

     snap info microk8s

0 packages can be updated.
0 updates are security updates.

```

## Update all packages on host os
1. `sudo apt-get update`
2. `sudo apt-get dist-upgrade`

## Install nginx
1. `sudo apt-get install nginx`
2. Check if nginx is installed properly.
```
systemctl status nginx

● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enab
   Active: active (running) since Fri 2019-09-06 20:07:55 UTC; 29s ago
     Docs: man:nginx(8)
  Process: 1085 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=e
  Process: 654 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on;
 Main PID: 1097 (nginx)
    Tasks: 2 (limit: 1135)
   CGroup: /system.slice/nginx.service
           ├─1097 nginx: master process /usr/sbin/nginx -g daemon on; master_proce
           └─1099 nginx: worker process

Sep 06 20:07:42 vagrant systemd[1]: Starting A high performance web server and a r
Sep 06 20:07:55 vagrant systemd[1]: nginx.service: Failed to parse PID from file /
Sep 06 20:07:55 vagrant systemd[1]: Started A high performance web server and a re
```

3. Another way to test that nginx server serving traffic on port 80 is as follows:
- Open the browser on your host pc.
- Enter `localhost:8000` in the address bar.
- You should be able to see the default nginx page.
![nginx default page](https://github.com/grathore07/configuring_nginx_on_linux/blob/master/screenshots/nginx_default_page.png)


## Important directories in nginx
1. `/etc/nginx` - This directory hold the configuration for entire nginx installation. Inside this directory you will find the files that control the way the web server runs, along with the files that define the websites being served. The main configuration file is `/etc/nginx/nginx.conf`.
```
ls -la /etc/nginx

total 72
drwxr-xr-x  8 root root 4096 Sep  6 19:29 .
drwxr-xr-x 96 root root 4096 Sep  6 19:29 ..
drwxr-xr-x  2 root root 4096 Aug 20 12:46 conf.d
-rw-r--r--  1 root root 1077 Apr  6  2018 fastcgi.conf
-rw-r--r--  1 root root 1007 Apr  6  2018 fastcgi_params
-rw-r--r--  1 root root 2837 Apr  6  2018 koi-utf
-rw-r--r--  1 root root 2223 Apr  6  2018 koi-win
-rw-r--r--  1 root root 3957 Apr  6  2018 mime.types
drwxr-xr-x  2 root root 4096 Aug 20 12:46 modules-available
drwxr-xr-x  2 root root 4096 Sep  6 19:29 modules-enabled
-rw-r--r--  1 root root 1482 Apr  6  2018 nginx.conf
-rw-r--r--  1 root root  180 Apr  6  2018 proxy_params
-rw-r--r--  1 root root  636 Apr  6  2018 scgi_params
drwxr-xr-x  2 root root 4096 Sep  6 19:29 sites-available
drwxr-xr-x  2 root root 4096 Sep  6 19:29 sites-enabled
drwxr-xr-x  2 root root 4096 Sep  6 19:29 snippets
-rw-r--r--  1 root root  664 Apr  6  2018 uwsgi_params
-rw-r--r--  1 root root 3071 Apr  6  2018 win-utf
```

2. Inside `/etc/nginx` we also find directories `conf.d`, `sites-available`, `sites-enabled`. These locations are where we store server configuration files.
> nginx server configuration files are very similar in function to Apache's vhost files. In fact you will see the term vhost and server configuration used interchangeably.

3. An example configuration is stored in `/etc/nginx/sites-available/default` file. This configuration sets up the `welcome to nginx` page that we use to test nginx installation is running correctly. 

4. `/var/log/nginx` contains error and access logs.
```
ls -la /var/log/nginx

total 16
drwxr-xr-x 2 root     adm    4096 Sep  6 19:29 .
drwxrwxr-x 9 root     syslog 4096 Sep  6 20:07 ..
-rw-r----- 1 www-data adm     740 Sep  7 06:22 access.log
-rw-r----- 1 www-data adm     126 Sep  6 20:08 error.log
```

`access.log` records any accesses that web server has encountered.
`error.log` records any errors that web server has encountered.

5. `/var/www` stores the actual files that get served to a client, including html, images, and other documents. The default directory for this is `/var/www/html`. However, as we create and define new server configurations in vhost, we will be creating our own direcotries in `/var/wwww` to accomodate files that get served.




