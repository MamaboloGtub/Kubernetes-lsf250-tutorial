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
 -t: Allocate a pseudo tty
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
   You should see active on the terminal active (running) 
 Install kubeadm
 Then, install kubectl - this is a CLI utility used to communicate with the Kubernetes cluster to deploy workloads
 Then, Kubelet - this is the main Kubernetes component that runs as a system service on every node of the cluster
 The documentation suggests installing cri-dockered for the VM. It's not important, but it is worth reading about. 
 
 
