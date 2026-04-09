# Kubernetes-lsf250-tutorial
This project will include mainly the markdown files, which explain the lessons learned when preparing for the KCNA exam through the Linux Foundation and Andela
# Initial Explanation
I currently do not have a Linux machine, so I will be learning this using VMware Workstation Pro as my virtual Linux desktop. 
# Build an image
command ``` docker image build -t nginx:lfs250 ```
# Creating a container
Command: bash ``` docker container run -dit -P nginx:lfs250 ```
## best practice will be to define port when running the container, e.g. ``` docker run -d -p 8080:80 nginx:lfs250 ```
 -d: to detach the container
 -i: make the container interactive
 -t: Allocate a pseudo TTY
 -P: get random high range port (helps with accessing the application from outside)

 # To find an IP address
 Command: bash ``` curl ifconfig.io ```. This will provide you with the IP address of the VM that you can use to access the application deployed in the container.
 Remember, if you are not on the local machine, sometimes you might experience errors when trying to access the application via IP address.  This is because in the VM, the public IP addresses do not loop back to the container, but might go to the internet.  So you replace the IP address with localhost. Example: 100.251.125:8082 should be localhost:8082.

 # Kubernetes
 Start
 First, install Container RunTime Interface(CRI). This is a prerequisite before setting up the Kubernetes cluster
 From my VM, I asked ChatGPT to suggest a suitable one, and it suggested containerd (It's lightweight and a Kubernetes standard).
 1. Install containerd
 command: ``` sudo apt update
 sudo apt install -y containerd ```
2. Generate default config
 command: ``` sudo mkdir -p /etc/containerd
 sudo containerd config default | sudo tee /etc/containerd/config.toml ```
3. Enable systemd cgroup driver - this is important for Kubernetes
   command ``` sudo vim /etc/containerd/config.toml ``` then find SystemdCgroup and set it to true.
4. Then you restart the containerd and verify that it's running.
   command: ```
     sudo systemctl restart containerd
     sudo systemctl enable containerd
     sudo systemctl status containerd ```
   You should see active on the terminal (running)
5. Test container RunTime (CRI)
   5.1. first install the cri CLI - crictl and test it. 
   command: ```
        sudo apt install -y cri-tools
        sudo crictl info
   ```  if it displays information, it means it is working.
   Kubernetes talks to containerd via CRI
 Install core tools.  
 Because Kubernetes is strict when it comes to versions, these core tools are supposed to be the same version. 
 1. Add the Kubernetes repo
    ```
     sudo apt update
     sudo apt install -y apt-transport-https ca-certificates curl
    ```
    ```
     sudo mkdir -p /etc/apt/keyrings
     curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo tee /etc/apt/keyrings/kubernetes.asc
    ```
    ```
     echo "deb [signed-by=/etc/apt/keyrings/kubernetes.asc] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list 
    ```
2. Install packages
   ```
     sudo apt update
    sudo apt install -y kubelet kubeadm kubectl
   ```
3. Lock versions - This is very important (It prevents accidental upgrades that break your cluster)
   ```
      sudo apt-mark hold kubelet kubeadm kubectl     
   ```
4.  ```
       kubeadm version
      kubectl version --client
      kubelet --version
    ```
   Now we have to initialize kubeadm but before that we have to fix certain issues
   1. Disable swap - this is required and make it permanent
      command: ```
              sudo swapoff -a
              sudo sed -i '/ swap / s/^/#/' /etc/fstab
      ```
   2. Enable Kernel Modules
      command: ```
             sudo modprobe overlay
             sudo modprobe br_netfilter
            ```
 3. Set sysctl params  ```
        cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
        net.bridge.bridge-nf-call-iptables = 1
        net.ipv4.ip_forward = 1
        net.bridge.bridge-nf-call-ip6tables = 1
        EOF
    ```
    ```
     sudo sysctl --system
    ```
    Restarts the container runtime to apply configuration changes.
    ```
     sudo systemctl restart containerd
    ```
    Ensure that containerd starts automatically on boot
    ```
     sudo systemctl enable containerd
    ```
    Now initialize kubeadm (this command intializes the Kubernetes control plane and starts your cluster)
    command: ```
      sudo kubeadm init
    ```
 The documentation suggests installing cri-dockered for the VM. It's not important, but it is worth reading about. 
 
 
