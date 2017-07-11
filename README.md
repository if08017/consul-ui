# Consul
We need three or more servers :
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
rm -rf consul_0.8.5_linux_amd64.zip
mv consul /usr/bin/
git clone https://github.com/if08017/consul-ui.git
```

```
# Bootstrap / Web UI Server
---------------------------
wget https://releases.hashicorp.com/consul/0.6.4/consul_0.6.4_web_ui.zip
unzip consul_0.6.4_web_ui.zip
rm -rf consul_0.6.4_web_ui.zip
cd /root/consul
cp bootstrap.json config.json

# generate key and keep it
consul keygen
```

```
# Non Boostrap Consul Server
---------------------------
cd /root/consul
cp server.json config.json
```

```
# Consul Agent Server
---------------------------
yum -y install httpd
cd /root/consul
cp agent.json config.json
```

```
./setup.sh HOSTNAME ENCRYPT_KEY IP_OF_BOOTSTRAP IP_NON_BOOTSTRAP
nohup consul agent -config-dir /root/consul/config.json &

# Now lets test on our agent server.
curl -X PUT -d 'test' http://localhost:8500/v1/kv/web/key1
curl http://localhost:8500/v1/kv/web/key1
curl http://localhost:8500/v1/kv/web/key1?raw
```

Create service on all consul server
```
[Unit]
Description=consul agent
Requires=network-online.target
After=network-online.target

[Service]
EnvironmentFile=-/etc/sysconfig/consul
Environment=GOMAXPROCS=2
Restart=on-failure
ExecStart=/usr/bin/consul agent -config-dir=/root/consul/config.json -rejoin
ExecReload=/bin/kill -HUP $MAINPID
KillSignal=SIGTERM

[Install]
WantedBy=multi-user.target
```
Start service
```
systemctl start consul.service
systemctl enable consul
```
