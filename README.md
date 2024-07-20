## Vagrant K8s Cluster MAC Silicon
This repository contains instructions and configurations to kubernetes setup on MAC Silicon M1/M2 laptops using Vagrant and VMWare Fusion.
### Prerequisites
Before you begin, ensure you have the following installed on your Mac:

1. [Homebrew](https://brew.sh/)
2. [Vagrant](https://www.vagrantup.com/)
3. [VMware Fusion](https://www.vmware.com/products/fusion.html) (ensure it's the latest version)
4. [Vagrant VMware Utility](https://www.vagrantup.com/docs/providers/vmware/vagrant-vmware-utility)

## Installation Steps
> Once all the above mentioned installations are done, restart your MAC. If you dont restart your MAC, you will get the following error.
> ```
> Vagrant failed to create a new VMware networking device. The following
> error message was generated while attempting to create a new device:
>
> Failed to enable device
>
> Please resolve any problems reported in the error message above and
> try again.
> ```
## Building a VM with Vagrant
 - **Initialize a Vagrant Project**
   
   Create a directory for your Vagrant project and initialize it:
   ```
   mkdir my-vm-project
   cd my-vm-project
   vagrant init
   ```
 - **Edit the Vagrantfile** :
   
   - To test the setup, you can clone the repo to deploy three VMs.
     
   - `cd` in the the setup folder. It has a Vagrantfile with bento/ubuntu-22.04 vargantbox for arm architecture that deploys three VM’s. You can modify the VM details in the settings.yaml file present in the folder.
     
   - Now, bring up the VMs using vagrant.
     ```
     sudo vagrant up
     ```
   > Ensure you use sudo with the command. Without sudo, it wont work.

***It should bring up three VMs.***

## Using the VM :
  - To get the node names, execute:
    ```sudo vagrant status```
    
<img width="686" alt="image" src="https://github.com/user-attachments/assets/0e17b1cc-e39c-4209-bd5f-4c3ec7ca8ce8">


  - Use the machine names to ssh. For example:
    ```sudo vagrant ssh controlplane```
    
<img width="701" alt="image" src="https://github.com/user-attachments/assets/525071e8-faf7-47e6-b5b6-a251facb2a70">


## Set Kubeconfig file variable

 - You can connect to the Vagrant cluster from your local mac terminal by configuring the following.
   
```
cd configs
sudo chmod +r config 
export KUBECONFIG=$(pwd)/config
```
   
 - Validate the cluster access

 ```
 kubectl get po -n kube-system
```

 
## Install Kubernetes Dashboard

The dashboard is automatically installed by default, but it can be skipped by commenting out the dashboard version in settings.yaml before running vagrant up.

If you skip the dashboard installation, you can deploy it later by enabling it in settings.yaml and running the following:

```
vagrant ssh -c "/vagrant/scripts/dashboard.sh" controlplane
```

## Kubernetes Dashboard Access

- To get the login token, copy it from config/token or run the following command:
```
kubectl -n kubernetes-dashboard get secret/admin-user -o go-template="{{.data.token | base64decode}}"
```

- Make the dashboard accessible:
```
kubectl proxy
```

- Open the site in your browser:
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
```

 - Top stop the VMs,
```
sudo vagrant halt
```
   
 - To delete the VMs
```
sudo vagrant destroy
```
     
 - To bring up the VMs again, run
```
sduo vagrant up
```
> [!TIP]
> However, there are a few improvements and considerations that might enhance the setup:
> 
> ### ***Versioning Consistency***:
> 
> Ensure the versioning for software components like Kubernetes and Calico is consistent across the Vagrant and shell scripts.
> 
> ### ***Provisioning Order*** :
> 
> Make sure your provisioning steps are in the correct order. For example, ensure that the common.sh script is executed before controlplane.sh and node.sh to avoid missing dependencies.
>
> ### ***Shared Folders*** :
> 
> I have commented out the shared folders section. If needed, uncomment and configure this section to facilitate file sharing between the host and the VMs.
>
> ### ***DNS Configuration*** :
> 
> The DNS servers configuration is correct, but ensure they are reliable for your environment. If you have internal DNS servers, consider adding them here.
>
> ### ***Resource Allocation*** :
> 
> Verify that the allocated resources (CPU and memory) for each VM are sufficient for your Kubernetes components to run smoothly. You might need to adjust these values based on your host machine's capabilities.
>
> ### ***Scripts Validation***:
> 
> Ensure that the scripts (common.sh, controlplane.sh, node.sh, dashboard.sh) exist and are executable. Double-check their content for any potential issues or missing dependencies.    

> [!CAUTION]
> If you face any errors during the setup, drop a comment. I will look at it. 
   


   
   
