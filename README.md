# kubernetes
kubernetes the easy way!
========================================================================================================================================
INSTALLATION OF K8'S CLUSTER USING MINIKUBE
========================================================================================================================================
What you’ll need/Prerequisites:
2 CPUs or more
2GB of free memory
20GB of free disk space
Internet connection
Container or virtual machine manager, such as: Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMWare

For Linux users, we provide 3 easy download options:

Binary download
 curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
 sudo install minikube-linux-amd64 /usr/local/bin/minikube
Debian package
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
RPM package
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -ivh minikube-latest.x86_64.rpm
Now,
start your cluster 
minikube start
AND BAM! IT'S UP AND RUNNING 

=======================================================================================================================================
INSTALLATION OF K8'S CLUSTER USING KUBEADM
=======================================================================================================================================
Prerequisites:
1.Make an entry of each hosts in etc/hosts file  on all the nodes as well as configure it on DNS if you have DNS server.
cat /etc/hosts
192.168.2.1 kubernetes-master.learnitguide.net kubernetes-master
192.168.2.2 kubernetes-worker1.learnitguide.net kubernetes-worker1
192.168.2.3 kubernetes-worker2.learnitguide.net kubernetes-worker2
2.Make sure kubernetes master and worker node are reachable between each other by ping command.
3.Kubernetes doesn't support swap disable swap on all nodes using below command and to make it permanent comment out swap entry in 
/etc/fstab file.
swapoff -a
4.Internet must be enabled on all nodes
NOW STEPS INVOLVED IN MAKING K8'S CLUSTER
On all Nodes:
1.Enable k8's nodes repository on master and all worker nodes.
2.Install the required packages on master and all worker nodes
3.Start and Enable docker and kubelet services on master and all worker nodes
4.Allow Network ports in firewall on master and all worker nodes

On Master Node:
5.Intializing and setting up the kubernetes cluster on Master Node.
6.Copy /etc/kubernetes/admin.conf and Change ownership only on Master Node.
7.Install Network add-on to enable the communication between the pods only on Master Node.

On Worker Node:
8.Join all worker nodes with kubernetes master node

Now in action:
1.Enable Kubernetes repository on master and all worker nodes
create a repo file for kubernetes and add the lines given below.
cat /etc/yum.repos.d/kubernetes.repo
======================================================================================================================================
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
======================================================================================================================================
2. install the required packages on master and all worker nodes.
yum -y install docker kubeadm
3.Start and enable docker and kubelet services on master and all worker nodes.
systemctl start docker && systemctl enable docker
systemctl start kubelet && systemctl enable kubelet
4.Allow network ports in firewall on master and all worker nodes
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --reload
kubernetes service's endpoints are being set with port 6443 and kubelet listens on port 10250
Also set "l" to bridge firewall rules.
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
if there is an error try with this.
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
5.Initializing and setting up the k8's cluster on master node.
[root@kubernetes-master ~]# kubeadm init --apiserver-advertise-address 192.168.2.1 --pod-network-cidr=172.16.0.0/16
Output:
[init] using Kubernetes version: v1.11.2
[preflight] running pre-flight checks
        [WARNING Firewalld]: firewalld is active, please ensure ports [6443 10250] are open or your cluster may not function correctly
I0811 21:10:04.905996   12195 kernel_validator.go:81] Validating kernel version
I0811 21:10:04.906058   12195 kernel_validator.go:96] Validating kernel config
[preflight/images] Pulling images required for setting up a Kubernetes cluster
[preflight/images] This might take a minute or two, depending on the speed of your internet connection
[preflight/images] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[preflight] Activating the kubelet service
[certificates] Generated ca certificate and key.
.........................................................................
suppressed few messages
-------------------------------------------------------
[bootstraptoken] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy
Your Kubernetes master has initialized successfully!
To start using your cluster, you need to run the following as a regular user:
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/
You can now join any number of machines by running the following on each node
as root:
  kubeadm join 192.168.2.1:6443 --token pxavv6.zwqgdlivwfgbaaud --discovery-token-ca-cert-hash sha256:0cd1e77fd1514a6ec60e3c67c678c0d88ac80b18ff8184271ecef1ccdc01ee55
[root@kubernetes-master ~]#
6.Copy /etc/kubernetes/admin.conf and change the ownership only on master node
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
7.Install Network add-on to enable the communication between pods only on master node
[root@kubernetes-master ~]# kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
Output:
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.extensions/kube-flannel-ds-amd64 created
daemonset.extensions/kube-flannel-ds-arm64 created
daemonset.extensions/kube-flannel-ds-arm created
daemonset.extensions/kube-flannel-ds-ppc64le created
daemonset.extensions/kube-flannel-ds-s390x created
Use "kubectl get nodes" command to ensure the master node status is ready.
Output:
[root@kubernetes-master ~]# kubectl get nodes
NAME                STATUS    ROLES     AGE       VERSION
kubernetes-master   Ready     master    14m       v1.11.1
[root@kubernetes-master ~]#
8.Join all the kubernetes worker nodes with  master node using token.
kubeadm join 192.168.2.1:6443 --token pxavv6.zwqgdlivwfgbaaud --discovery-token-ca-cert-hash sha256:0cd1e77fd1514a6ec60e3c67c678c0d88ac80b18ff8184271ecef1ccdc01ee55
dont use this use the one that has been generated on your machine
Output:
[preflight] running pre-flight checks
        [WARNING RequiredIPVSKernelModulesAvailable]: the IPVS proxier will not be used, because the following required kernel modules are not loaded: [ip_vs_wrr ip_vs_sh ip_vs ip_vs_rr] or no builtin kernel ipvs support: map[nf_conntrack_ipv4:{} ip_vs:{} ip_vs_rr:{} ip_vs_wrr:{} ip_vs_sh:{}]
you can solve this problem with following methods:
 1. Run 'modprobe -- ' to load missing kernel modules;
2. Provide the missing builtin kernel ipvs support
I0811 21:22:23.219089    2523 kernel_validator.go:81] Validating kernel version
I0811 21:22:23.219199    2523 kernel_validator.go:96] Validating kernel config
[discovery] Trying to connect to API Server "192.168.2.1:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://192.168.2.1:6443"
[discovery] Requesting info from "https://192.168.2.1:6443" again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "192.168.2.1:6443"
[discovery] Successfully established connection with API Server "192.168.2.1:6443"
[kubelet] Downloading configuration for the kubelet from the "kubelet-config-1.11" ConfigMap in the kube-system namespace
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[preflight] Activating the kubelet service
[tlsbootstrap] Waiting for the kubelet to perform the TLS Bootstrap...
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "kubernetes-worker1" as an annotation
This node has joined the cluster:
* Certificate signing request was sent to master and a response
  was received.
* The Kubelet was informed of the new secure connection details.
Run 'kubectl get nodes' on the master to see this node join the cluster.

Now verify with "kubectl get nodes"
[root@kubernetes-master ~]# kubectl get nodes
NAME                 STATUS    ROLES     AGE       VERSION
kubernetes-worker1   Ready     <none>    56s       v1.11.2
kubernetes-worker2   Ready     <none>    48s       v1.11.2
kubernetes-master    Ready     master    50m       v1.11.1
[root@kubernetes-master ~]#
  
Now k8's cluster is Ready!
