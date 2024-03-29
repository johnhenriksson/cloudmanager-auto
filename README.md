# NetApp Cloud Manager automated deployment on VMware with Packer & Terraform
This automation builds a VM template on CentOS 7, deploys that template to **VMware vSphere** and installs **NetApp Cloud Manager** - all automated.

In this scenario we use the on-premise *(with internet access)* version of **NetApp Cloud Manager**, but you could easily change it to the *dark-site* version, just change the installation binary.

![Cloud Manager VM Screenshot](img/cloudmanager_vmware.png)

## pre-reqs

* Terraform https://www.terraform.io/downloads
* Packer https://www.packer.io/downloads
* CentOS 7 ISO https://www.centos.org/download/
* NetApp Cloud Manager install package https://mysupport.netapp.com/site/products/all/details/cloud-manager/downloads-tab

This installation is tested and validated with;

* Terraform v1.1.9
* Packer 1.8.0
* CentOS-7-x86_64-DVD-2009.iso
* OnCommandCloudManager-V3.9.17

I'm running the automation from my MacBook but should be the same for any *nix based OS.

# Run the automation

## Create environment variables

Rename the file `.env_example` to `.env` and adjust the content to your environment. You need to modify credentials and connection properties to your vCenter server.

**Important**, the automation uses the default password of `server` and you !need! to change that after deployment to secure your environment.

**Important**, Cloud Manager requires 4 vCPU, 16GB RAM & 100+GB storage(I recommend 250GB)

Packer requires a DHCP enabled network to build the VM template

## Deploy!

1. Make sure you have all the pre-reqs
2. Upload **CentOS-7-x86_64-DVD-2009.iso** to a VMware datastore and adjust the `TF_VAR_iso_url` variable in your `.env` file
3. Place the **OnCommandCloudManager-V3.9.17.sh** file in the `tf_deploy` folder
4. Load your environment variables with `export $(xargs < .env)`
5. Build the VM template with `packer build centos.json` - (takes about ~15-20 min)
![Cloud Manager Packer screenshot](img/cloudmanager_packer.png)

6. Move to the `tf_deploy` folder and run `terraform apply` to deploy Cloud Manager VM from the template. You may need to run `terraform init` the first time to initialize the environment.
![Cloud Manager Terraform screenshot](img/cloudmanager_terraform.png)

7. SSH to the VM and change the root passwordh with `passwd root`
8. After deployment, browse to http://{cloudmanager_ip} to finish the installation

Read more about Cloud Manager deployment on https://docs.netapp.com/us-en/cloud-manager-setup-admin/task-installing-linux.html

**Important**, the automation uses the default password of `server` and you **need(!)** to change that after deployment to secure your environment.