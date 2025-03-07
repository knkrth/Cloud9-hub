# Cloud9 Hub
Simple hub page for Cloud9 SDK. Each user has one workspace, authenticate with linux pam module.

This is an nginx reverse proxy config which will try to authenticate user:password with linux pam module ,and try to execute command to spawn a cloud9 workspace by that user, and then proxy_pass to it.


## Script install for Ubuntu 18.04
**Warning:** This script will reinstall your nginx and npm.

**Warning:** This script will upgrade your system by ```apt-get upgrade -y``` command.

Run this in root console

```sh -c "$(wget -O- https://raw.githubusercontent.com/HuJK/Cloud9-hub/master/install.sh)"```

And goto url : [https://your_server_ip:8443](https://your_server_ip:8443)

Demo:
[https://3.91.44.12:8443/](https://3.91.44.12:8443/)

user|passwd
------|---------
demo01|demo)!
demo02|demo)@
demo03|demo)#

# Manual install 
Prenstall
--

Ubuntu (run as root):
```bash
apt-get update
apt-get update  -y
# In debian, when I installed nginx, I can't install nginx-full. Because they use different version of nginx. So I have to remove nginx first.
apt-get remove  -y nginx
# The install script will detect npm exist or not on the system. If exist, it will not use itself's npm
# But in Ubuntu 19.04, npm from apt are not compatible with it. So I have to remove first, and install back later.
apt-get remove  -y npm
apt-get install -y nginx-full
apt-get install -y libnginx-mod-http-auth-pam
apt-get install -y lua5.2 lua5.2-doc liblua5.2-dev
apt-get install -y luajit
apt-get install -y libnginx-mod-http-lua
apt-get install -y tmux gdbserver gdb git python python3 build-essential wget libncurses-dev nodejs 
apt-get install -y python-pip python3-pip golang default-jdk coffeescript php-cli php-fpm ruby
apt-get install -y zsh fish tree ncdu aria2  p7zip-full python3-dev perl curl
curl https://install.meteor.com/ | sh
pip3 install pexpect

mkdir /etc/c9
mkdir /etc/c9/sock
mkdir /etc/c9/util
wget -O- https://raw.githubusercontent.com/kikito/md5.lua/master/md5.lua > /etc/c9/util/md5.lua
wget -O- https://raw.githubusercontent.com/HuJK/Cloud9Hub/master/pip2su.py > /etc/c9/util/pip2su.py

cd /etc/c9
HOME=/etc/c9
git clone https://github.com/c9/core sdk
cd sdk
./scripts/install-sdk.sh

chmod -R 755 /etc/c9/sdk
chmod -R 755 /etc/c9/.c9
chmod -R 755 /etc/c9/util
chmod -R 773 /etc/c9/sock
HOME=/root
# Install npm back
apt-get install -y npm
```

Install
--

```bash
usermod -aG shadow nginx
usermod -aG shadow www-data
wget -O- https://raw.githubusercontent.com/HuJK/Cloud9Hub/master/c9io > /etc/nginx/sites-available/c9io
ln -s ../sites-available/c9io /etc/nginx/sites-enabled/c9io
cd /etc/c9
wget -O- https://raw.githubusercontent.com/HuJK/Cloud9Hub/master/logout.patch | patch -p0
mkdir /etc/c9/.c9/runners/
wget https://raw.githubusercontent.com/HuJK/Cloud9-hub/master/Python%203.run -O "/etc/c9/.c9/runners/Python 3.run"
```

Postinstall.
--
Edit ```/etc/nginx/sites-enabled/c9io``` with vim, nano, or any other text editior with root. And follow following instructions.

##### 1. Configure ssl certificates

use self-signed certificates:
```bash
apt-get install -y install openssl
mkdir /etc/c9/cert
chmod 600 /etc/c9/cert
cd /etc/c9/cert
openssl genrsa -out ssl.key 2048
openssl req -new -x509 -key ssl.key -out ssl.pem -days 3650 -subj /CN=localhost
```

##### 2. Use valid ssl certificates

1. Buy or get a free domain
2. Get a valid certificate from letsencrypt
3. Edit line 10~11
```
    ssl_certificate     /etc/c9/cert/ssl.pem;
    ssl_certificate_key /etc/c9/cert/ssl.key;
```
to your certificate and keys.



##### 3. Change port number(if you want)
Edit line 8~9
```
    listen 8443 ssl;
    listen [::]:8443 ssl;
``` 
from 8443 to other ports that you prefer.

Now, reload nginx with ```nginx -s reload```
