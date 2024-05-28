https://github.com/containerd/containerd/blob/main/contrib/ansible/README.md :)
		
1. Отключаем swap закомментировав раздел в /etc/fstab
	
2. [Forwarding-ipv4-and-letting-iptables-see-bridged-traffic](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#forwarding-ipv4-and-letting-iptables-see-bridged-traffic)

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf  
overlay  
br_netfilter  
EOF
```

```bash
sudo modprobe overlay  && sudo modprobe br_netfilter
```
<!--- sysctl params required by setup, params persist across reboots -->
```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf  
net.bridge.bridge-nf-call-iptables  = 1  
net.bridge.bridge-nf-call-ip6tables = 1  
net.ipv4.ip_forward                 = 1  
EOF
```

```bash
#Apply sysctl params without reboot  
sudo sysctl --system  
```

 3. [cgroup-drivers](Cgroupfs driver https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroup-drivers)

 To set systemd as the cgroup driver, edit the KubeletConfiguration option of cgroupDriver and set it to systemd. For example:
```yml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
...
cgroupDriver: systemd
```
*Note: Starting with v1.22 and later, when creating a cluster with kubeadm, if the user does not set the cgroupDriver field under KubeletConfiguration, kubeadm defaults it to systemd.*

4. [Install containerd](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)

```bash
version='1.7.17'; wget https://github.com/containerd/containerd/releases/download/v${version}/containerd-${version}-linux-amd64.tar.gz  && \
tar Cxzvf /usr/local containerd-${version}-linux-amd64.tar.gz && \
rm containerd-${version}-linux-amd64.tar.gz && \
curl -L https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -o /lib/systemd/system/containerd.service && \
systemctl daemon-reload && systemctl enable --now containerd && mkdir -p /etc/containerd && containerd config default > /etc/containerd/config.toml
```
5. [Configure containerd](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd-systemd)

```bash
nano /etc/containerd/config.toml
```

```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
```

6. [Overriding the sandbox (pause) image](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#override-pause-image-containerd)

```bash
nano /etc/containerd/config.toml
```

```toml
[plugins."io.containerd.grpc.v1.cri"]
  sandbox_image = "registry.k8s.io/pause:3.9"
```

7. [Installing runc](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#step-2-installing-runc)

```bash
wget https://github.com/opencontainers/runc/releases/download/v1.1.12/runc.amd64 && install -m 755 runc.amd64 /usr/local/sbin/runc
```

8. [Installing CNI plugins](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#step-3-installing-cni-plugins)
```bash
wget https://github.com/containernetworking/plugins/releases/download/v1.5.0/cni-plugins-linux-amd64-v1.5.0.tgz && mkdir -p /opt/cni/bin && tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.5.0.tgz && rm cni-plugins-linux-amd64-v1.5.0.tgz
```

9. [Debugging Kubernetes nodes with crictl](https://kubernetes.io/docs/tasks/debug/debug-cluster/crictl/)

```bash
/etc/crictl.yaml
```

```yml
runtime-endpoint: unix:///var/run/containerd/containerd.sock
image-endpoint: unix:///var/run/containerd/containerd.sock
timeout: 10
debug: true
```

 8.[Installing k8s](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```bash
sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```
```bash
# If the directory `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
```bash
# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
```

9. [Installing Addons](https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy)

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/custom-resources.yaml
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
kubectl taint nodes --all node-role.kubernetes.io/master-
```
