# Kubernetes-lsf250-tutorial
This project will include mainly the markdown files, which explain the lessons learned when preparing for the KCNA exam through the Linux Foundation and Andela
# Initial Explanation
I currently do not have a Linux machine, so I will be learning this using VMware Workstation Pro as my virtual Linux desktop. 
# Creating a container
Command: bash ``` docker container run -dit -P nginx:lfs250 ```
 -d: to detach the container
 -i: make the container interactive
 -t: Allocate a pseudo tty
 -P: get random high range port (helps with accessing the application from outside)

 # To find an IP address
 Command: bash ``` curl ifconfig.io ```. This will provide you with the IP address of the VM that you can use to access the application deployed in the container.
 Remember, if you are not on the local machine, sometimes you might experience errors when trying to access the application via IP address.  This is because in the VM, the public IP addresses do not loop back to the container, but might go to the internet.  So you replace the IP address with localhost. Example: 100.251.125:8082 should be localhost:8082.

 # Kubernetes
 Start
 First, install Container RunTime Interface(CRI)
 From my VM, I asked ChatGPT to suggest a suitable one, and it suggested containerd (It's lightweight and a Kubernetes standard).
 From the documentation, it might suggest installing cri-dockered for the VM. It's not important, but it is worth reading about. 
 
 
