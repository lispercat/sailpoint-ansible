This is a readme for provisioning and upgrading Sailpoint IIQ boxes for Linux and Windows using MS SQL Server as a backend.
Ansible is used for provisioning for both Linux and Windows with the only difference that to setup and upgrade a Windows box you need to spin off
an Ansible controller box (Linux), for IIQ in Linux, it's capable of provisioning itself using local ansible.
You need to have Vagrant + Oracle VirtualBox installed on your system to use it

## Features
* **Ansible Controller** box, use "vagrant up" or "vagrant up ac" from the root of the repository. That will create a linux box with ansible installed plus a few necessary collections to control either Linux or Windows targets. 
After you started the system you can connect to it using ssh and the private key provided in ..\.vagrant\machines\ac\virtualbox\private_key.
In order to ssh into the ansible controller box, run this: 
> ssh -i .vagrant\machines\ac\virtualbox\private_key vagrant@192.168.10.10
* **Windows based Sailpoint IIQ deployment (Tomcat+MS SQL server on one machine)**
  * Change directory to win folder and run "vagrant up" this will provision a VM
  * Build your identityiq.war via SSB, and put it in .\sailpoint-ansible\win\roles\iiq\files
  * ! Go to win\install-iiq-playbook.yml and put the right versions for **iiq_version** and **iiq_patch** variables
  * go to the ansible controller and run:
    > cd /vagrant/win
    > ./ansible_ping_all.sh (it ping the windows box)
    > ./ansible_install_iiq.sh (installs Tomcat, MS SQL server and deploys identityiq.war )
    **Wait ~1min** so that Tomcat deploys a new application
    Check that your IIQ is successfully deployed by logging onto http://192.168.10.12:8080/identityiq/
* **Windows based Sailpoint IIQ upgrade (Tomcat+MS SQL server on one machine)**
  * Now that you have the IIQ up and running it's time to upgrade it to the next version
  * Go to https://community.sailpoint.com/t5/IdentityIQ-Server-Software/ct-p/IdentityIQ and download your IIQ GA and patch, and put them under .\sailpoint-ansible\win\roles\iiq-upgrade\files
  * ! Go to win\upgrade-iiq-playbook.yml and put the right values for **iiq_version_old**, **iiq_patch_old**, **iiq_version_new**, **iiq_patch_new variables**
  * go to the ansible controller and run:
  > cd /vagrant/win
  > ./ansible_upgrade_iiq.sh
  **Wait ~1min** so that Tomcat deploys a new application
  Check that your IIQ is successfully deployed by logging onto http://192.168.10.12:8080/identityiq/
  * **Linux based Sailpoint IIQ deployment (Tomcat+MS SQL server on one machine)**
    * Steps are similar to those of Windows but it's located under /vagrant/linux and you **don't need to run** ./ansible_install_iiq.sh since when you run "vagrant up" it will automatically provision the IIQ environent given that your identityiq.war is in the right place (see above)
    * After "vagrant up" is finished make sure you can logon to http://192.168.10.11:8080/identityiq/
  * **Linux based Sailpoint IIQ upgrade (Tomcat+MS SQL server on one machine)**
    * Steps are similar to the upgrade procedure for Windows (see above) only it's located under /vagrant/linux
    * After you're done with running ./ansible_upgrade_iiq.sh make sure you can logon to http://192.168.10.11:8080/identityiq/ 
