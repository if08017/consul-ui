# Consul
We need three or more servers.
Consul service discovery tool examples :
consul-bootstrap
consul-server
consul-agent

```
# Run these on all consul servers.
sudo su
yum -y update
yum -y install git unzip wget
cd /root
wget https://releases.hashicorp.com/consul/0.8.5/consul_0.8.5_linux_amd64.zip?_ga=2.128066436.964449614.1499710749-1685302108.1499710749
unzip consul_0.8.5_linux_amd64.zip
rm -f consul_0.8.5_linux_amd64.zip
mv consul /usr/bin/
git clone https://github.com/andrewpuch/consul_demo.git
```

```
# Bootstrap / Web UI Server
---------------------------
wget https://dl.bintray.com/mitchellh/consul/0.5.2_web_ui.zip
unzip 0.5.2_web_ui.zip
rm -f 0.5.2_web_ui.zip
cd /root/consul_demo
cp bootstrap.json config.json

# Save this keygen! Note, if your key has a slash in it you need to escape them for setup.sh. Or just regenerate one until it doesn't have a slash :)
consul keygen
```

```
# Non Boostrap Consul Server
---------------------------
cd /root/consul_demo
cp server.json config.json
```

```
# Consul Agent Server
---------------------------
apt-get install apache2 -y
cd /root/consul_demo
cp agent.json config.json
```

```
./setup.sh HOSTNAME ENCRYPT_KEY IP_OF_BOOTSTRAP IP_NON_BOOTSTRAP
nohup consul agent -config-dir /root/consul_demo/config.json &

# Now lets test on our agent server.
curl -X PUT -d 'test' http://localhost:8500/v1/kv/web/key1
curl http://localhost:8500/v1/kv/web/key1
curl http://localhost:8500/v1/kv/web/key1?raw
```
