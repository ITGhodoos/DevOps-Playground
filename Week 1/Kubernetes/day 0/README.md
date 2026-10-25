# day 0

## installing Kubernetes with kubeadm

### step 0: Prequisits & Requirements

okay so here are the stuff that i used to initiate my cluster:

- prebuilt ubuntu server 26.04 LTS
- containerd
- cri-tools
- gpg
- curl
- apt-transport-https
- ca-certificates
- net-tools
- tun2socks

#### NOTE: cri-tools & net-tools are used for troubleshooting; and i used tun2socks to overcome filterations to access k8s image registry

 i used a prebuilt vm file to load up ubuntu with prequisits for Kubernetes installation, used my own startup menu script to prepare my nodes for installation. i planned to create a 3 node cluster including one master node and 2 worker nodes for k8s practice and a test environment for small projects.

### step 1: disabling swap
i edited /etc/fstab file of my nodes wtih nano and deleted the swap line, then rebooted the nodes

### step 2: setting up ipv4 bridge networking between nodes
i tweaked /etc/modules-load.d/containerd.conf and added "overlay" & "br_netfilter" to the file so it can provide bridge networking between nodes
after that i loaded "overlay" and "br_netfilter" with the "modeprobe" command

### step3: allow node bridge networking on iptables
i added these rules to /etc/sysctl.d/kubernetes.conf so the firewall may allow the traffics between nodes to passthrough:

```bash
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
```
this makes iptables to see the bridge networking traffics between nodes.
after all this applied newely added sysctl parameters without rebooting the system by the command:
`sudo sysctl --system`

### step 4: installing containerd
`apt install containerd`
nothing really special

### step 5: setting up default configuration file for containerd
i ran `containerd config default` command to generate default configuration file for containerd and store it on "/etc/containerd/config.toml" file
after that, i edited the generated config file by changing the "SystemdCgroup" parameter to "true".
lastly restarted containerd by `systemctl restart containerd` command

### step 6: installing kubeadm, kubelet, kubectl

i installed prequisits which are "apt-transport-https", "ca-certificates", "curl" and "gpg"
after that it was time to download public key signing for the Kubernetes, for this purpose i ran:
```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
next i added Kubernetes apt to my ubuntu repository by:
`echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list`
and lastly i ran:
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
to install Kubernetes components on my nodes
```
#### ERRROR: i made an minor error on this stage, "kubectl" is not required on worker nodes, unless you want to be able to manage your cluster on your worker nodes, "kubeadm" and "kubelet" is only required on worker nodes, the rest will be automatically installed after joining your worker node to your cluster

### step 7: pulling Kubernetes components images
`sudo kubeadm config images pull`
it didn't permit me to do it due to Kubernetes access restriction for iran so i used `curl -x` and `sudo env` and `export` to use my proxy connection for it, it didn't work again, apparently kubeadm doesnt like to use any environment variable for proxies, so i start to research on alternative solutions.
i tried DOH(DNS Over HTTPS) Providers but it didn't work. i am looking towards "redsocks" and "tun2socks" as a solution, right now im trying to learn tun2socks configuration to imply on my nodes
i decided to use tun2socks since i do have a socks proxy server on my network that i can use.
tun2socks official link:
[https://github.com/xjasonlyu/tun2socks/releases?utm_source=chatgpt.com](URL)
i followed the instructions from the link below:
[https://github.com/xjasonlyu/tun2socks/wiki/Examples](URL)

#### ERROR:
 i started the service, it acted awkwardly, first there was an unknown network interface added to my ubuntu "evice", i tested the connection to my proxy server with "netcat" command "nc -vz -w 3 "<my proxy server address> <port>". and connection failed to establish.
i looked up at my server and everything was ok; it was listening on ports and its addresses and connection test to its addresses on proxy ports were successful.

i came back to ubuntu side; checked the routing table and noticed that because that we deleted the default route in our tun2socks installation procedure now all traffics are going through tunnel interface which was currently down.
i added a static route for my server network to ubuntu and it did fix the connection issue. but tunnel interface and that unknown "evice" connection were still there.
i asked ai again about it and it did come up with this command:
```bash
sudo tun2socks \
  --device tun://tun0 \
  --proxy socks5://192.168.100.6:1080 \
  --loglevel debug
  ```
basically a typo error. tun2socks reads "-device" as "-d" "evice"; that is where that "evice" connection came from.
i applied this new command to start proxy connection and it worked. tun interface turned up and "evice" was gone
now back to our main topic.

## step 8: initializing kubernetes cluster on master node

since its a home environment cluster and my hardware resource is limited i didn't set up any reverse proxy server for my cluster; so i used my master node as my api server. and used 10.244.0.0/16 network for my pod networking.

## step 9: flannel

i opened GitHub page for flannel and copied it kubectl apply command for applying flannel manifest to my flannel.

## step 10: joining nodes

after tweaking tun2socks solution on my worker nodes it finally was able to pull images for flannel and finally my worker nodes joined to my cluster
cluster is up and running

## step 11: initiating kubectl

i used `export KUBECONFIG=/etc/kubernetes/admin.conf` to generate kubeconfig file for my kubectl, but unfortunately it will stop working after a reboot since it will write the configuration to your ".bashrc", so i created a .kube directory on my root home directory(since i only want root to have access to the cluster)
and copied admin.conf file from "/etc/Kubernetes" directory to "~/.kube/config"
finally i changed the ownership of the file to root user

**done**

### final notes:
solution i used for bypassing internet restrictions is both difficult and might not work on every network environment; so alternatively i recommend pulling image Kubernetes images from an alternative image registry such as google containers
-when running kube config images pull command use option --image-repository <your preferred alternate image registry>.
if youre Iranian, trust me, it will make life much easier.
