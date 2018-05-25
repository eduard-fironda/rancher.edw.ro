# Setup Rancher server


ENV

- create VM for rancher server (rancherOS) 

- create several VM as rancher hosts (used until now 31 march 2018) (rancherOS) 

- create VM for nfs-server 

- create VM for Load Balancer (rancherOS) 


### Part1 Rancher VMs config 


1.  create VMs  with rancherOS 

2.  install for all vm's 

<pre><code class="python">
```
sudo ip addr add 10.0.30.61/24 dev eth0
sudo route add default gw 10.0.30.250
sudo ros install -c https://pastebin.com/raw/h3r4NCWP -d /dev/sda
###################################################################
// if this method does not work properly use `wget https://pastebin.com/raw/h3r4NCWP`  then `sudo ros install -c h3r4NCWP -d /dev/sda` 
####################################################################
ros config set hostname <hostname>
cd /var/lib/rancher/conf
sudo ros config validate -i cloud-config.yml
```
</code></pre>

3.  config just for rancher server 

<pre><code class="python">
```
sudo wget https://github.com/docker/compose/releases/download/1.19.0/run.sh
mv run.sh docker-compose
sudo chmod +x docker-compose
sudo mv docker-compose /usr/bin/
cd /usr/bin/
export set PATH=$PATH:/usr/bin/
source docker-compose
mkdir /var/local/
mkdir /var/local/rancher-server/
vi docker-compose.yml // see it in this repo
#in /var/local/rancher-server
#put `docker-compose.yml` (use eea config file)
#`$ docker-compose up -d`

docker-compose up -d

```
</code></pre>


4.  config just for rancher nfs server 

<pre><code class="python">
```
yum -y install nfs-utils
systemctl enable nfs-server.service
systemctl start nfs-server.service
mkdir /var/nfs
chown nfsnobody:nfsnobody /var/nfs
chmod 755 /var/nfs
vi /etc/exports
`/var/nfs        *(rw,sync,no_root_squash,no_subtree_check)` //*=all ips  if you want a specific replace * with your desired ip 

exportfs -a
systemctl restart nfs-server

```
</code></pre>


### Part2 Rancher WEB-INTERFACE config 

<pre><code class="python">
```
1. go to http://your-ip:80  //rancher server vm ip 

2. Admin tab -> Access Control : choose github login 

3.  Manage environments -> Edit default env -> from here you can add organizations add environments and github users 

4. Infrastructure - Hosts -> Add Hosts : you will see an instruction page  copy and paste the command from step 5 in your created VM hosts 

Do not forget to add in rancher all the previous created VMs ( rancher-load-balancer, +example host 1 2 3) 
```
</code></pre>


### Part3 Rancher WEB-INTERFACE config 

1. config just for rancher load-balancer 

<pre><code class="python">
```
1. Infrastructure -> Hosts -> rancher-load-balancer //(it is not mandatory to use this name, it is just an example to be more clear) 

2. Edit rancher-load-balancer host 

3. Labels -> add label : frontend = yes 

```
</code></pre>

2. Add LB stack
<pre><code class="python">
```
scheduling load balancer `host must have label frontend yes`

```
</code></pre>

3. Add NFS stack from catalog 

<pre><code class="python">
```
1. Catalog -> Search NFS -> View details 
2. NFS Server: your nfs vm ip
3. Export Base Directory: /var/nfs
4. Launch
```
</code></pre>


