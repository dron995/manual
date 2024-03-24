https://github.com/containerd/containerd/blob/main/contrib/ansible/README.md :)
		
1. Отключаем swap
`
закомментировав раздел в /etc/fstab
`
	
2. [Forwarding-ipv4-and-letting-iptables-see-bridged-traffic](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#forwarding-ipv4-and-letting-iptables-see-bridged-traffic)

`
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
`

`
sudo modprobe overlay
sudo modprobe br_netfilter
`

`
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
`

`
sudo sysctl --system
`
	
		
		
	Cgroupfs driver https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroup-drivers
			
		
		https://sleeplessbeastie.eu/2021/09/10/how-to-enable-control-group-v2/
		https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/

	Install containerd https://github.com/containerd/containerd/blob/main/docs/getting-started.md
		wget https://github.com/containerd/containerd/releases/download/v1.7.1/containerd-1.7.1-linux-amd64.tar.gz
		tar Cxzvf /usr/local containerd-1.7.1-linux-amd64.tar.gz
		## install as daemon https://github.com/containerd/containerd/blob/main/containerd.service
		nano /lib/systemd/system/containerd.service
		systemctl daemon-reload && systemctl enable --now containerd && mkdir -p /etc/containerd && containerd config default > /etc/containerd/config.toml
		
	Configure containerd https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd-systemd
	
	Installing runc (https://github.com/opencontainers/runc/releases)
		wget https://github.com/opencontainers/runc/releases/download/v1.1.7/runc.amd64 && install -m 755 runc.amd64 /usr/local/sbin/runc
		install -m 755 runc.amd64 /usr/local/sbin/runc
	Installing CNI plugins (https://github.com/containernetworking/plugins/releases)
		wget https://github.com/containernetworking/plugins/releases/download/v1.3.0/cni-plugins-linux-amd64-v1.3.0.tgz && mkdir -p /opt/cni/bin && tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.3.0.tgz
	
	Installing k8s (https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
		sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl
		curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/google.gpg
		sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
		
		sudo apt-get update && sudo apt-get install -y kubelet kubeadm kubectl && sudo apt-mark hold kubelet kubeadm kubectl
		
		
	

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube &&   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config &&   sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.81.8:6443 --token kwexqu.f9pfdigryk3eho4l \
        --discovery-token-ca-cert-hash sha256:c02b11b578848c8e45dbcde33bcaeebf50e94b9574e19ad2c6950e859e4645aa



kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec.containers[*].image}" |tr -s '[[:space:]]' '\n' |sort |uniq -c
