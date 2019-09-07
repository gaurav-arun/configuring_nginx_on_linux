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

5. `/var/www` stores the actual files that get served to a client, including html, images, and other documents. The default directory for this is `/var/www/html`. However, as we create and define new server configurations in vhost, we will be creating our own direcotries in `/var/www` to accomodate files that get served.

## nginx CLI
1. `sudo systemctl status nginx`
2. `sudo systemctl [start|stop] nginx` - commands to start/stop nginx.
3. `sudo systemctl is-active nginx` - tells if nginx is currently running.
```
sudo systemctl is-active nginx

active
```
4. `sudo systemctl reload nginx` - reload nginx
5. `nginx -h`
```
nginx -h

nginx version: nginx/1.14.0 (Ubuntu)
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/share/nginx/)
  -c filename   : set configuration file (default: /etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
```
6. `nginx -t` - test if nginx configuration is good. It also warns the user if there is any syntactical errors.
7. `nginx -T | less` - test if nginx configuration is good, and dump it to stdout. We can use pager like `less` to see the output page by page.

## /etc/nginx/nginx.conf
It is rarely required to edit `nginx.conf` file.
```
cat nginx.conf

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}


#mail {
#	# See sample authentication script at:
#	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#	# auth_http localhost/auth.php;
#	# pop3_capabilities "TOP" "USER";
#	# imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#	server {
#		listen     localhost:110;
#		protocol   pop3;
#		proxy      on;
#	}
# 
#	server {
#		listen     localhost:143;
#		protocol   imap;
#		proxy      on;
#	}
#}
```
- `user www-data;` is a single line directive.
- `events` and `http` are block directives that contain one or more single line directives.
- Following directives configure the default log files.
```
access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;
```
- Following include directives tell the main configuration file to process configuration files in other directories, particulary the `/etc/nginx/conf.d` and `/etc/nginx/sites-enabled` directories.
```
	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
```
There are varying schools of thought on how additional configurations get added to nginx when it comes to these two directories. You'll notice that the `conf.d` directory has a wildcard for files ending in `.conf`, and sites-enabled directory has wildcard enabled for all files. So, one method to add configuration is to place file ending in the `.conf` in 
`conf.d` directory. Another method is to place configurations in **`sites-available` directory and then link to them using a symlink from `sites-enabled` directory**. Let's take a look:
```
vagrant@vagrant:/etc/nginx/sites-enabled$ ls -la
total 8
drwxr-xr-x 2 root root 4096 Sep  6 19:29 .
drwxr-xr-x 8 root root 4096 Sep  6 19:29 ..
lrwxrwxrwx 1 root root   34 Sep  6 19:29 default -> /etc/nginx/sites-available/default
```
**We will be using `conf.d` directory for all our configuration files.**

## Configure virtual host - Part 1
Let's create our first server configuration.
1. Swith to `root` to avoid entering sudo everytime.
```
sudo su -
```
2.`cd /etc/nginx/sites-enabled/`
```
ls -la

total 8
drwxr-xr-x 2 root root 4096 Sep  6 19:29 .
drwxr-xr-x 8 root root 4096 Sep  6 19:29 ..
lrwxrwxrwx 1 root root   34 Sep  6 19:29 default -> /etc/nginx/sites-available/default
```
3. Remove the symlink to default configuration using `unlink default`.
> The `default` configuration file will still be available in `/etc/nginx/sites-available`. we are simply removing the link from `/etc/nginx/sites-enabled` directory.

```
ls -la 

total 8
drwxr-xr-x 2 root root 4096 Sep  7 08:00 .
drwxr-xr-x 8 root root 4096 Sep  6 19:29 ..
```
4. Now we need to create the configuration file that will hold the configuration for our site. We will be storing our configurations in `/etc/nginx/conf.d/` directory. We will be setting up a simple site for a veterinary clinic called wisdom pet medicine.And our site will be called `wisdompetmed.local`. 
5. Create a configuration file in `conf.d` directory.
```
nano /etc/nginx/conf.d/wisdompetmed.local.conf
```
Naming the configuration file same as the site makes things easier to manage. The `.conf` extension is important so that vhost configuration gets picked up when nginx configuration is reloaded.
6.`nano etc/nginx/conf.d/wisdompetmed.local.conf`. Add the following lines:
```
server {
  listen 80;
  root /var/www/wisdompetmed.local;
}
```
7. Test the configuration.
```
nginx -t

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
8. Reload nginx configuration.
```
systemctl reload nginx
systemctl status nginx

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@vagrant:/etc/nginx/sites-enabled# systemctl reload nginx
root@vagrant:/etc/nginx/sites-enabled# systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2019-09-06 20:07:55 UTC; 12h ago
     Docs: man:nginx(8)
  Process: 1857 ExecReload=/usr/sbin/nginx -g daemon on; master_process on; -s reload (code=exited, status=0/SUCCESS)
  Process: 1085 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
  Process: 654 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
 Main PID: 1097 (nginx)
    Tasks: 2 (limit: 1135)
   CGroup: /system.slice/nginx.service
           ├─1097 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─1858 nginx: worker process

Sep 06 20:07:42 vagrant systemd[1]: Starting A high performance web server and a reverse proxy server...
Sep 06 20:07:55 vagrant systemd[1]: nginx.service: Failed to parse PID from file /run/nginx.pid: Invalid argument
Sep 06 20:07:55 vagrant systemd[1]: Started A high performance web server and a reverse proxy server.
Sep 06 20:08:40 vagrant systemd[1]: Reloading A high performance web server and a reverse proxy server.
Sep 06 20:08:40 vagrant systemd[1]: Reloaded A high performance web server and a reverse proxy server.
Sep 07 08:21:24 vagrant systemd[1]: Reloading A high performance web server and a reverse proxy server.
Sep 07 08:21:24 vagrant systemd[1]: Reloaded A high performance web server and a reverse proxy server.
```
9. Now lets create the directory `/var/www/wisdompetmed.local` we used in server config.
```
mkdir /var/www/wisdompetmed.local
```
10. Place an `index.html` file to test if the configuration is taking effect.
```
echo "Website coming soon" > /var/www/wisdompetmed.local/index.html
```
11. Open the browser on your host machince and go to `http://localhost:8000`. NGINX should serve new `index.html` file with content `Website coming soon`.
![index.html](https://github.com/grathore07/configuring_nginx_on_linux/blob/master/screenshots/testing_virtualhost_config_1.png)

## Configure virtual host - Part 2
1. Let's add few more directives to `wisdompetmed.local.conf` file.
```
server {
  listen 80 default_server;
  server_name wisdompetmed.local www.wisdompetmed.local;
  index index.html index.htm index.php;
  root /var/www/wisodmpetmed.local;
}
```
- `default_server` : NGINX can server multiple sites from the same IP address. We can use the `default_server` statement to tell nginx as default configuration if no other configuration match the name of the site being requested as mentioned in `server_name`

- `server_name` : If you are familiar with Apache vhost configuration, this is similar to setting up a name-based vhost. By including the server name `wisdompetmed.local` and `www.wisdompetmed.local`, we let nginx know which sites this server configuration applies to. Otherwise, if we were serving multiple sites from the same IP address, nginx might server the wrong content.
> More info on [request_processing.html](https://nginx.org/en/docs/http/request_processing.html)

- `index` : This tells nginx what file to use when it responds to a request. It searches and returns the first match in the list provided.

2. Test the configuration changes.
```
nginx -t
```
3. Reload the new configuration.
```
systemctl reload nginx
curl localhost

Website coming soon
```

## Configure permissions on root directory
1. Extract and place the demo site under `/var/www/wisdompetmed.local` directory.
```
mkdir /var/www/wisdompetmed.local
unzip -o /vagrant/Wisdom_Pet_Medicine_responsive_website_LYNDA_12773.zip -d /var/www/wisdompetmed.local
```
2. Adjust the permission on files and folders under `wisdompetmed.local` directory.
Following commands set the permission on every file to be writable by root and readable by everyone else and on every directory such that root has all permissions but everyone else has read and execute permissions.
```
find /var/www/wisdompetmed.local -type f -exec chmod 644 {} \; -print
find /var/www/wisdompetmed.local -type d -exec chmod 755 {} \; -print
```

3. Load the configuration
```
systemctl reload nginx
```

## Configure locations and error in configuration file
1. Add location directives and error directives to `/etc/nginx/conf.d/wisdompetmed.local.conf`
```
server {
    listen 80 default_server;

    root /var/www/wisdompetmed.local;

    server_name wisdompetmed.local www.wisdompetmed.local;

    index index.html index.htm index.php;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /images/ {
        # Allow the contents of the /image folder to be listed
        autoindex on;
    }

    error_page 404 /404.html;
    location = /404.html {
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        internal;
    }
    
    location = /500 {
        fastcgi_pass unix:/this/will/fail;
    }
}
```
2. Reload nginx configuration.
```
systemctl reload nginx
```
## Configure logs
1. Add logging configuration for server and location context
```
server {
    listen 80 default_server;

    root /var/www/wisdompetmed.local;

    server_name wisdompetmed.local www.wisdompetmed.local;

    index index.html index.htm index.php;
    
    access_log /var/log/nginx/wisdompetmed.local.access.log;
    error_log /var/log/nginx/wisdompetmed.local.error.log;
    
    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /images/ {
        # Allow the contents of the /image folder to be listed
        autoindex on;
	
	# For demonstrating that access and error in this folder
	# can be logged separately
	access_log /var/log/nginx/wisdompetmed.local.images.access.log;
	error_log /var/log/nginx/wisdompetmed.local.images.error.log;
    }

    error_page 404 /404.html;
    location = /404.html {
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        internal;
    }
    
    location = /500 {
        fastcgi_pass unix:/this/will/fail;
    }
}
```
2. Test the configuration changes
```
nginx -t
```
3. Reload the configuration
```
systemctl reload nginx
```
4. Test if logs are going into right files.
```
for i in {1..10}; do curl localhost > /dev/null; done
```
Now, if we look into the access logs for wisdompetmet.local, we will find some new access logs.

Similarly,

```
for i in {1..10}; do curl localhost/images/ > /dev/null; done
```
Now, if we look into the access logs for wisdompetmet.local.images, we will find some new access logs.

## Troubleshooting nginx
1. Catch configuration errors using. Helps us identify typo in the configuration file(s). 
```
nginx -t
```
2. Check for typos in root and server name directives. Sometimes these typos may not be flagged by `nginx -t`.
```
#Should be html, not htm1
root /var/www/htm1
#Should be www.example.com
server_name example.com www.examples.com
```
3. If site cannot be reached, make sure nginx is running and new configuration has taken effect.
```
systemctl status nginx
systemctl reload nginx
```
4. If nginx is running, Verify that standart ports for http and https are open
```
sudo lsof -P -n -i :80 -i :443 | grep LISTEN

nginx   987     root    6u  IPv4  18895      0t0  TCP *:80 (LISTEN)
nginx   989 www-data    6u  IPv4  18895      0t0  TCP *:80 (LISTEN)
```
Another way,
```
sudo netstat -plan | grep nginx

tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      987/nginx: master p 
unix  3      [ ]         STREAM     CONNECTED     18914    987/nginx: master p  
unix  3      [ ]         STREAM     CONNECTED     18913    987/nginx: master p 
```

5. If configuration is good and nginx up, running and listening on right ports, we can tail the logs.
```
tail -f /var/logs/nginx/*.log
```

6. Google it. Stackoverflow it. Ask an Expert.

## Configure HTTPS
### Create an SSL certificate and key
1. Create a key a named `nginx.key` and a x509 certificate named `nginx.cert`. 
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx.key -out /etc/ssl/certs/nginx.crt
```
Fill in the details as necessary. Adding `-batch` flag at the end removes the prompt for additional info.
The certificate can be public since the server uses the certificate to identify itself over https. The key needs to stay private as it will be used to encrypt the traffic.

### Install SSL certificate on nginx
1. The first thing we need to do is to force all traffic to be served using `https`. We will do this by creating a new default server that listens on `port 80`. Create a new `server` block in vhost file - `/etc/nginx/conf.d/wisdompetmed.conf`. We do not need a `root` directive for this block as we are redirecting all traffic to https with a `return` directive.
```
server {
  listen 80 default_server;
  return 301 https://$server_addr$request_uri;
}
```
Any request that comes to `port 80` will get permanent redirect to https by response code `301` to the address specified as second argument. 

2. Now we need to update our old server block to listen on `port 443`. We also need to add the `ssl` directive to this block to tell nginx to encrypt the traffic served on this port. Just because we configure nginx to listen on `port 443`, it does'nt configure the SSL automatically. That's why we have to include `ssl` directive. We also need to add `ssl_certificate_key` and `ssl_certificate` directives along with their paths.

```
server {
  listen 443 ssl default_server;
  ssl_certificate /etc/ssl/certs/nginx.crt;
  ssl_certificate_key /etc/ssl/private/nginx.key;
  ...
  ...
}
```
3. Test the changes in configuration file.
```
nginx -t
```

4. Reload the configuration file to take effect.
```
systemctl reload nginx
```

5. Reloading the page in the browser will show a warning because we are using a self signed certificate. But now all the requests are being served on https. Bingo!

## Other features of nginx
NGINX can also be configured as a `reverse proxy` or a `load balancer`. Watch this [video](https://www.linkedin.com/learning/learning-nginx/reverse-proxies-and-load-balancing?autoplay=true) for a great explanation.

A `reverse proxy` applies to the case where there's is **only one server on the backend**. This could be a web application implemented in python or node.js, for example. Or it could be another web server running apache tomcat or even nginx. In these cases nginx is great at simplifying thing that might be harder to implement in other technologies like `ssl termination` and `logging`. NGINX proxies are also good for accelerating the response of backend servers by caching content.
It also has capabilities to compress data which can reduce the amount of network bandwidth needed to fulfil a request.

Acting as a `load balancer`, NGINX offers the same capabilites as a `reverse proxy`. But instead of only connecting to one server in the backend, a load balancer connects to multiple backend. This configuration is useful for applications that require more than one backend server to handle multiple clients or high volume of traffic, for example. With more than one server, a site can be more reliable. Another feature of `load balancer` is to enable `session persistence`. This is useful when the clients need the same server to accept and respond to all requests.

### Configure nginx as reverse proxy
Before we can setup a proxy or a load balancer in NGINX, we need to define the backend servers that NGINX will be connecting to. This is where the upstream directives come into play. If we consider NGINX's configuration there are certain contexts where we can apply directives. We can apply them at the `global context` that affects all of nginx. The `http context` for items that work with the http protocol and inside the http context, `server` and `location` context for example that specifically configure virtual hosts and the URI's used to access them.
```
http {
  ...
  server {
    ...
  }
  ...
  location {
    ...
  }
  ...
}
```
`Upstreams` are defined in `http` context. This is useful so that one upstream can be defined and then reused by multiple servers also defined in the http context. **As a side note, we refer to these resources as upstream because, in a flow of web request the resources would be upstream from the NGINX and the client.**

1. Create a new configuratoin file `upstream.conf`
```
nano /etc/nginx/conf.d/upstream.conf
```
Since nginx is configured to load all `.conf` files from `cond.d` directory, this is a good place to store our file.

2. Here, `app_server_7001` is to easily understand that the app server is listening on `port 7001`. 
```
upstrem app_server_7001 {
  server 127.0.0.1:7001;
}
```
**The server statement in the upstream block is different from the server statement used to define a virtual host.** In case of the upstream server, we have to include the name and ip address of the server that this block connects to. We could also use a `unix socket` if the backend server is listening on a socket, but in this case the server is another `http process` running on the same vm as our NGINX process. So we'll use the local IP address `127.0.0.1` with `port 7001`.

3. Now that we have our upstream defined, we need to connect to it. To connect to it we need to have a server block. In this case we will use the same file that our `upstream` is defined in. In this case, we will use the same file that our upstream is defined in. But it is not a requirement.

```
upstream app_server_7001 {
  server 127.0.0.1:7001;
}

server {
  listen 80;
  location /proxy {
    # trailing slash is the key here!!
    proxy_pass http://app_server_7001/;
  }
}
```
We have added proxy with the `proxy_pass` directive. This comes from `ngx_http_proxy_module`. Refer [docs](http://nginx.org/en/docs/http/ngx_http_proxy_module.html) for more info. 

The server we defined will listen on `port 80`. If request comes in on `/proxy`, the server will use the location that matches, so it will send the traffic into the `/proxy` location for processing. With a `proxy pass` directive, the request will be sent to http resource defined in the upstream named `app_server_7001`, which in the case is our process listening on 7001. **Any response generated by the upstream is then returned to nginx to be sent back to the client.** 

Without the trailing slash, nginx would try to connect to a locatoin name `/proxy` on the backend server. In most cases you would want to proxy to the root of the backend server and let the application route the requests as needed.

4. Save the file, test the configuration and reload.
```
nginx -t
systemctl reload nginx
```

5. Open the browser and enter `localhost/proxy`. The request will be routed to the application root URI.
