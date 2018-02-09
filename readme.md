# docker compose TICK stack setup

- nginx for auth & SSL
- influxdb for timeseries data runs on port 8080 or 8443 (https), non standard port but reduces firewall issues
- cartograf for display, runs on port 80 or 443 (https)
- kapacitor for alerting
- telegraf for self monitoring
- telegraf for sending metrics from other hosts
- letsencrypt via letsencrypt-nginx-proxy-companion

can be cheaply deployed on any cloud hoster e.g hetzner cloud for 2.49 EUR / Month

# setup docker compose host

```

sudo apt-get remove docker docker-engine docker.io
sudo apt-get update
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common


sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update

sudo apt-get install docker-ce

sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo apt-get install ufw

sudo ufw default deny incoming

sudo ufw allow 22
sudo ufw allow 2222
sudo ufw allow 80
sudo ufw allow 8080
sudo ufw allow 443
sudo ufw allow 8443

sudo ufw enable

git clone https://github.com/chrillo/grandcentral-telemetry.git

sudo sh -c "echo -n '${USER}:' >> /etc/nginx/.htpasswd"
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"

cd grandcentral-telemetry

docker-compose up

```

# setup a telegraf client

curl -s https://raw.githubusercontent.com/chrillo/grandcentral-telemetry/master/setup-telegraf.sh | sudo bash /dev/stdin 'https://${USER}:${PASSWORD}@${URL}:8443'