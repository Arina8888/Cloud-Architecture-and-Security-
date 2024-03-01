# Cloud Architecture and Security Project  

**_step1_**   

**Setting up the Resource Group**
Use the Azure portal to create a resource group - Project_2 that will contain everything the Red Team needs in the cloud.
On the home screen, search for "resource."

Click on the + Add button or the Create resource group button.
Create a name for your resource group -Project_2  and choose a region - ((US) West US 3) .
Every resource you create after this must be created in the exact same region.

Click on Review + create.
Azure will alert you if there are any errors. Click on Create to finalize your settings and create the group.
Once the group is created, click on Go to resource group in the top-right corner of the screen to view your new resource group.

![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/RG.JPG)


**_step2_**

**Setting up the VNet**
Before you can deploy servers and services, there must be a network where these items can be accessed.
This network should have the capacity to hold any resource that the Red Team needs, now and in the future.
Return to the home screen and search for "net." Choose the search result for Virtual networks.

Click on the + Add button on the top-left of the page or the Create virtual network button on the bottom of the page.

Fill in the network settings:
Resource group: This should be the resource group you created in Step 1.
Name: A descriptive name so it will not get confused with other cloud networks in the same account.
Region: Make sure to choose the same region you chose for your resource group.
Carefully configuring the region of your resources is important for ensuring low latency and high availability. 
Resources should be located as close as possible to those who will be consuming them.
IP Addresses: Azure requires you to define a network and subnet.
Use the defaults on this tab.
Security: Leave the default settings.
In order to avoid recurring charges, do not enable DDoS Protection Standard.
Tags: No tags are needed.

Click Create.

Once you have created your resource group and VNet, return to the home screen and choose the resource group option.
This provides a list of all resource groups in your account.
Choose the group that you created and you should see your VNet listed as a resource.
You now have a resource group and VNet that you can use to create the rest of the cloud infrastructure throughout the unit.


**_step3_**

**Create a network security group**
On your Azure portal home screen, search "net" and choose Network security groups.

Create a new security group.
Add this security group to your resource group.
Give the group a recognizable name that is easy to remember.
Make sure the security group is in the same region that you chose during the previous activity.

Review and create, create

To create an inbound rule to block all traffic:
Once the security group is created, click on the group to configure it.
Choose Inbound security rules on the left.

Click on the + Add button to add a rule.
Configure the inbound rule 
Source: My IP Address
Source port ranges:  the wildcard (*) to match all source ports.
Destination:  Any 
Service: RDP.
Destination port ranges: 3389
Protocol: TCP.
Action: Allow.
Priority: 100 .
Name:AllowMyIpAddressRDPInbound.
Description:your choice 

Save the rule.

Choose Inbound security rules on the left.

Click on the + Add button to add a rule.

Configure the inbound rule 
Source: For now, we want to choose Any source to block all traffic.
Source port ranges: Source ports are always random, even with common services like HTTP. Therefore, we will want to keep the wildcard * to match all source ports.
Destination: Here, we will select Any to block any and all traffic associated with this security group.
Service: Here we will leave this as Custom as we are going to select all port ranges in the next configuration.
Destination port ranges: Usually we would specify a specific port or a range of ports for the destination. In this case, we can use the wildcard * to block all destination ports. You could also block all ports using a range like 0-65535.
Protocol: We will choose to block Any protocol that is used.
Action: We want to use the Block action to stop all of the traffic that matches this rule.
Priority: This rule will always be our last rule, so we want to choose the highest number for the priority available. Subsequent rules will always come before this rule. The highest number Azure allows is 4,096.
Name: Give your rule a name like "Default-Deny."
Description: Write a quick description similar to "Deny all inbound traffic."

Save the rule.

We should now have a VNet in place protected by a network security group that is blocking all traffic.

![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/nsg.JPG)

**_step4_**

**Generating public/private rsa key pair**
Allowing a server to use password authentication for SSH is insecure because the password can be brute forced.

Therefore, we will only use cryptographic SSH keys to access our cloud servers. Password authentication will not be allowed.

This is part of the "ground up" security approach.

Open your command line and run ssh-keygen to create a new SSH key pair.

Run cat ~/.ssh/id_rsa.pub to display your id_rsa.pub key

Highlight and copy the SSH key string to your clipboard

**create VM**
Type Virtual machines, click create azure VM.

Use the following settings for this VM:

Resource group: Choose the same resource group that you created for the Red Team.

Region: Use the same region that you used for your other resources.

Note that availability of VMs in Azure could cause you to change the region where your VMs are created.

The goal is to create three machines in the same resource group attached to the same security group. If you cannot add three machines to the resource group and security group that you have, a new resource group and security group may need to be created in another region.

Availability options: We will use this setting for other machines. For our jump box, we will leave this on the default setting.

Image: Choose the Ubuntu Server 20.04 option.

Choose the VM option that has:

Its offering set as Standard - B1s

1 CPU

1 GB RAM

For SSH, use the following settings:
Authentication type: SSH public key.

Username: Create any username you like.

SSH public key: Paste the public key string that you copied earlier.

Public inbound ports: Ignore this setting. It will be overwritten when you choose your security group.

Select inbound ports: Ignore this setting. It will be overwritten when you choose your security group.

Move to the Networking tab and set the following settings:
Virtual network: Choose the VNet you created for the Red Team.
Subnet: Choose the subnet that you created earlier.

Public IP: Choose Create new and choose Static under Assignment in the side panel. Give the IP Address a unique name.

NIC network security group: Choose the "Advanced" option so we can specify our custom security group.

Configure network security group: Choose your Red Team network security group.

Recommended Check the box to Delete public IP and NIC when VM is deleted.  If you have to delete the VM later, this will prevent extra IPs from appearing
Accelerated networking: Keep as the default setting (Off).

In the Networking settings, take note of the VM URL. You may use it later.

Load balancing: Keep as the default setting (No).

Click on Review + create.

Finalize all your settings and create the VM by clicking on the Create button.

**VMs 2 and 3 – Web VMs**
Create two more new VMs. Keep the following in mind when configuring these VMs:
Each VM should be named "Web-1" and "Web-2."
These VMs need to be in the same resource group you are using for all other resources.

The VMs should be located in the same region as your resource group and security group.

Note that availability of VMs in Azure could cause you to change the region where your VMs are created.

The goal is to create three machines in the same resource group attached to the same security group. If you cannot add three machines to the resource group and security group that you have, a new resource group and security group may need to be created in another region.

The administrative username should make sense for this scenario. You should use the same admin name for all three machines. Make sure to take a note of this name as you will need it to login later.
You will need to create a new SSH key for remote connections.

Note: Windows users should use GitBash to create ssh keys and ssh connections.

Choose the VM option that has:
Its offering set as Standard - B1ms
1 CPU
2 GB RAM

Note: These web machines should have 2 GB of RAM and the Jump-Box only needs 1 GB. All 3 machines should only have 1 vCPU because the free Azure account only allows 4 vCPUs in total per region.

Important: Make sure both of these VMs are in the same availability Set. Under Availability Options, select "Availability Set." Click on "Create New" under the Availability set. Give it an appropriate name. After creating it on the first VM, choose it for the second VM.

In the Networking tab and set the following settings:
Virtual network: Choose the VNet you created for the Red Team.

Subnet: Choose the subnet that you created earlier.

Public IP: NONE! Make sure these web VM's do not have a public IP address.

NIC network security group: Choose the Advanced option so we can specify our custom security group.

Configure network security group: Choose your Red Team network security group.

Accelerated networking: Keep as the default setting (Off).

Load balancing: Keep as the default setting (No).

NOTE: Notice that these machines will not be accessible at this time because our security group is blocking all traffic. 

![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/NSG%20(2).JPG)


**_step 5_**

**Jump Box Administration**
First, find your IP address by opening the terminal and entering the command curl icanhazip.com, or googling "What's my IP address?"

Next, log into portal.azure.com to create a security group rule to allow SSH connections from your current IP address.

Find your security group listed under your resource group.

Create a rule allowing SSH connections from your IP address.

Choose Inbound security rules on the left.

Click + Add to add a rule.

Source: Use the My IP address setting.

Note: Verify that the address that shows matches the address from step 1

Source port ranges: Set to Any or * here.

Destination: This can be set Service Tag/Virtual Network but a better setting is to specify the internal IP of your jump box to really limit this traffic.

Service: Set to SSH
Destination port ranges: This will default to port 22 as we selected SSH for the service.

Protocol: This will default to TCP.

Action: Set to Allow traffic.

Priority: This must be a lower number than your rule to deny all traffic (i.e., less than 4,096).

Name: Name this rule anything you like, but it should describe the rule. For example: SSH.

Description: Write a short description similar to: "Allow SSH from my IP."

Use your command line to SSH to the VM for administration. Windows users should use GitBash.

The command to connect is ssh admin-username@VM-public-IP.

Use the username you previously set up. (Your SSH key should be used automatically.)

The first time you connect, you will need to type yes to add this machine to your list of known hosts.

Once you are connected, check your sudo permissions.

Run the command sudo -l.

Notice that your admin user has full sudo permissions without requiring a password.

Please note that your public IP address will change depending on your location.

In a normal work environment, you would set up a static IP address to avoid continually creating rules to allow access to your cloud machine.

In our case, you will need to create another security rule allowing your home network to access your Azure VM.

NOTE: If you need to reset your SSH key, you can do so in the VM details page by selecting "Reset Password" on the left-hand column.



**_Step 6_**

**Containers**
Open your terminal and SSH into your jump box ($ ssh azureuser@20.38.36.21).

Once you are connected, run sudo apt-get update to update your machine.

Next, explain that we'll need to install docker.io.
sudo apt install docker.io

Double check that the docker service is running. 
Run: sudo systemctl status docker.

If the Docker service is not running, start it with sudo systemctl start docker.
Now that Docker is installed, we can pull the container cyberxsecurity/ansible.

Run: sudo docker pull cyberxsecurity/ansible.

docker pull: The Docker command to download containers.
cyberxsecurity/ansible: The specific container to download from the Docker Hub.
Now launch the Ansible Docker container and connect to it.

Run: sudo docker run -ti cyberxsecurity/ansible bash.

sudo docker run: The command to create and run a new container.
-ti stands for terminal and interactive sets up the container to allow you to run a terminal and interact with the container.
cyberxsecurity/ansible: The container image we just downloaded.
bash: The command we are running inside the container. This will give us a shell to control the container.
Point out that you get a new command prompt, showing that you are now connected to the container.

Run: exit to quit.

Now we need to create a security group rule that gives our jump box machine full access to our VNet. Without this permission, the jump box will not be able to access any resources inside the Azure portal.
Get the private IP address of your jump box from the VM resources page inside the Azure portal.
Go to your security group settings and create a rule. Use settings that allow SSH connections from your jump box's internal IP address.
Source: Use the IP Addresses setting with your jump box's internal IP address in the field.
Source IP addresses/CIDR ranges:10.0.0.4
Source port ranges: Any or * can be listed here.
Destination: Set to Service Tag/VirtualNetwork.
Service: Select SSH
Destination port ranges: This will default to port 22.
Protocol: Will default to TCP.
Action: Set to Allow traffic from your jump box.
Priority: Priority must be a lower number than your rule to deny all traffic.
Name: Name this rule anything you like, but it should describe the rule. For example: SSH-from-Jump-Box.
Description: Write a short description similar to "Allow SSH from the jump box IP."
While this is the easiest way to limit access to the VNet other types of secure access include setting up a site-to-site VPN or a client-to-site VPN.
If someone on the same network as you was able to access your SSH private key, they would be able to log into the jump box and access the entire VNet.

**_Step 7_**

**Provisioners**
Connect to your Ansible container. 
Once you're connected, create a new SSH key and copy the public key.

Run: sudo docker container list -a to find your image.

Run: sudo docker run -it cyberxsecurity/ansible /bin/bash

to start your container and connect to it. (Note that the prompt changes root@f2433e894795.)

Run: ssh-keygen to create an SSH key.

Run: ls .ssh/ to view your keys.

id_rsa  id_rsa.pub

Run: cat .ssh/id_rsa.pub to display your public key.

Copy your public key string.
Return to the Azure portal and locate one of your Web-VM's details page.
Reset password and add ssh public key , use existing public key use your container's new public key for the SSH user.
Get the internal IP for your new VM from the Details page.
After your VM launches, test your connection using ssh from your jump box Ansible container.

run ssh azureuser@10.0.0.6

Note: If only TCP connections are enabled for SSH in your security group rule, ICMP packets will not be allowed, so you will not be able to use ping.
root@23b86e1d62ad: ping 10.0.0.6
PING 10.0.0.6 (10.0.0.6) 56(84) bytes of data.
--- 10.0.0.6 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3062ms

 ssh ansible@10.0.0.6
 
The authenticity of host '10.0.0.6 (10.0.0.6)' can't be established.
ECDSA key fingerprint is SHA256:7Wd1cStyhq5HihBf+7TQgjIQe2uHP6arx2qZ1YrPAP4.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.0.0.6' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1027-azure x86_64)

To run a command as administrator (user "root"), use "sudo <command>".

See "man sudo_root" for details.
ansible@Pentest-1:~$
Exit this SSH session by running exit.
Locate the Ansible config file and hosts file.

root@1f08425a2967:ls /etc/ansible/
ansible.cfg  hosts  roles

Add this machine's internal IP address to the Ansible hosts file.
Open the file with nano /etc/ansible/hosts.
Uncomment the [webservers] header line.
Add the internal IP address under the [webservers] header.
Add the python line: ansible_python_interpreter=/usr/bin/python3 besides each IP.



    # This is the default ansible 'hosts' file.
    #
    # It should live in /etc/ansible/hosts
    #
    #   - Comments begin with the '#' character
    #   - Blank lines are ignored
    #   - Groups of hosts are delimited by [header] elements
    #   - You can enter hostnames or ip addresses
    #   - A hostname/ip can be a member of multiple groups
    # Ex 1: Ungrouped hosts, specify before any group headers.

    ## green.example.com
    ## blue.example.com
    ## 192.168.100.1
    ## 192.168.100.10

    # Ex 2: A collection of hosts belonging to the 'webservers' group

    [webservers]
    ## alpha.example.org
    ## beta.example.org
    ## 192.168.1.100
    ## 192.168.1.110
    10.0.0.6 ansible_python_interpreter=/usr/bin/python3
			10.0.0.7 ansible_python_interpreter=/usr/bin/python3
    ```

Change the Ansible configuration file to use your administrator account for SSH connections.
Open the file with 
nano /etc/ansible/ansible.cfg 
and scroll down to the remote_user option.
Uncomment the remote_user line and replace root with your admin username using this format:
- remote_user =user-name-for-web-VMs
Example What flags to pass to sudo

 ```

#WARNING: leaving out the defaults might create unexpected behaviours
#sudo_flags = -H -S -n
#SSH timeout
#timeout = 10
#default user to use for playbooks if user is not specified
#(/usr/bin/ansible will use current user as default)
remote_user = sysadmin

#logging is off by default unless this path is defined
#if so defined, consider logrotate
#log_path = /var/log/ansible.log

#default module name for /usr/bin/ansible
#module_name = command

 ```

Test an Ansible connection using the appropriate Ansible command.
Run ansible webservers -m ping
If you used ansible_python_interpreter=/usr/bin/python3, your output should look like:

10.0.0.5 | SUCCESS => {
"changed": false, 
"ping": "pong"
}
10.0.0.6 | SUCCESS => {
		"changed": false, 
		"ping": "pong"
}

If that line isn't present, you will get a warning like this:
root@1f08425a2967:ansible all -m ping
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host 10.0.0.6 should use 
/usr/bin/python3, but is using /usr/bin/python for backward compatibility with 
prior Ansible releases. A future Ansible release will default to using the 
discovered platform python for this host. See https://docs.ansible.com/ansible/
2.9/reference_appendices/interpreter_discovery.html for more information. This 
feature will be removed in version 2.12. Deprecation warnings can be disabled 
by setting deprecation_warnings=False in ansible.cfg.
10.0.0.6 | SUCCESS => {
		"ansible_facts": {
				"discovered_interpreter_python": "/usr/bin/python"
		}, 
		"changed": false, 
		"ping": "pong"
}



Ignore the [DEPRECATION WARNING] or add the line ansible_python_interpreter=/usr/bin/python3 next to each IP address in the host's file.

**_Step 8_**

**Ansible Playbooks**
Your task was to create an Ansible playbook that installed Docker and configure a VM with the DVWA web app.
Connect to your jump box, and connect to the Ansible container in the box.
If you stopped your container or exited it in the last activity, find it again using

sudo docker container list -a.

 ```
root@Red-Team-Web-VM-1:/home/RedAdmin# docker container list -a
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS                         PORTS               NAMES
Exited (0) 2 minutes ago                           hardcore_brown
a0d78be636f7        cyberxsecurity/ansible:latest   "bash"                   3 days ago  
 ```

Start the container again using docker start [container_name].

sudo docker start frosty_cerf

root@Red-Team-Web-VM-1:/home/RedAdmin# docker start hardcore_brown
hardcore_brown
Get a shell in your container using docker attach [container_name].

sudo docker attach frosty_cerf

root@Red-Team-Web-VM-1:/home/RedAdmin# docker attach hardcore_brown
root@1f08425a2967:
Create a YAML playbook file that you will use for your configuration.

root@1f08425a2967: nano /etc/ansible/pentest.yml


The top of your YAML file should read similar to:

 ```
- name: Config Web VM with Docker
    hosts: webservers
    become: true
    tasks:
 ```



DVWA runs on port 80, so we need to remove apache if it is installed.


By changing the state to absent the system will look to see if apache is installed and remove it.  If it is not installed, it will do nothing.

```
 - name: Uninstall apache if needed
     ansible.builtin.apt:
      update_cache: yes
      name: apache2
      state: absent
  ```      



Use the Ansible builtin.apt module to install docker.io and python3-pip:

Note: update_cache must be used here, or docker.io will not install. (This is the equivalent of running apt update.)
```
  - name: docker.io
    ansible.builtin.apt:
  			update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    ansible.builtin.apt:
      force_apt_get: yes
      name: python3-pip
      state: present
```

Note: update_cache: yes is needed to refresh the repositories to download and install docker.io.
Note: force_apt_get is used to prevent the broken packages or missing dependencies from erroring out on install.


Use the Ansible pip module to install docker:
```
  - name: Install Python Docker module
    pip:
      name: docker
      state: present
```



Note: Here we are installing the Python Docker Module, so Ansible can then utilize that module to control docker containers. More about the Python Docker Module HERE

Use the Ansible docker-container module to install the cyberxsecurity/dvwa container.

Make sure you publish port 80 on the container to port 80 on the host.


  - name: download and launch a docker web container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      restart_policy: always
      published_ports: 80:80




NOTE: restart_policy: always will ensure that the container restarts if you restart your web VM. Without it, you will have to restart your container when you restart the machine.
You will also need to use the systemd module to restart the docker service when the machine reboots. That block looks like this:

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes




Run your Ansible playbook on the new VM.
Your final playbook should read similar to:

 ```
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:

    - name: Uninstall apache if needed
      ansible.builtin.apt:
        update_cache: yes
        name: apache2
        state: absent
  
    - name: docker.io
      ansible.builtin.apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: Install pip3
      ansible.builtin.apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    - name: Install Docker python module
      pip:
        name: docker
        state: present

    - name: download and launch a docker web container
      docker_container:
        name: dvwa
        image: cyberxsecurity/dvwa
        state: started
        published_ports: 80:80

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes
 ```


Running your playbook should produce an output similar to the following:

root@1f08425a2967:~# ansible-playbook /etc/ansible/pentest.yml

PLAY [Config Web VM with Docker] ***************************************************************

TASK [Gathering Facts] *************************************************************************
ok: [10.0.0.6]

TASK [Uninstall apache if needed] **************************************************************
changed: [10.0.0.6]

TASK [docker.io] *******************************************************************************
[WARNING]: Updating cache and auto-installing missing dependency: python-apt

changed: [10.0.0.6]

TASK [Install pip3] *****************************************************************************
changed: [10.0.0.6]

TASK [Install Docker python module] ************************************************************
changed: [10.0.0.6]

TASK [download and launch a docker web container] **********************************************
changed: [10.0.0.6]

PLAY RECAP *************************************************************************************
10.0.0.6                   : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

To test that DVWA is running on the new VM, SSH to the new VM from your Ansible container.

SSH to your container:


root@1f08425a2967:ssh sysadmin@10.0.0.6
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 5.0.0-1027-azure x86_64)
Run: curl localhost/setup.php to test the connection. If everything is working, you should get back some HTML from the DVWA container.
ansible@Pentest-1:~$ curl localhost/setup.php

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

<html xmlns="http://www.w3.org/1999/xhtml">

  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />

    <title>Setup :: Damn Vulnerable Web Application (DVWA) v1.10 *Development*</title>

    <link rel="stylesheet" type="text/css" href="dvwa/css/main.css" />

    <link rel="icon" type="\image/ico" href="favicon.ico" />

    <script type="text/javascript" src="dvwa/js/dvwaPage.js"></script>

  </head>

**_Step 9_**

**Load Balancing**
Create a new load balancer and assign it a static IP address.
Start from the homepage and search for "load balancer."
Create a new load balancer in your red team resource group and give it a name.
sku-standart
type-public
tier-regional
Add a frontend IP address.
Give the IP address a unique address name. This name will be used to create a URL that maps to the IP address of the load balancer.
Create a new public IP address.
Assigment-static
availability zone - zone redundant 
routing preference-internet
Add a backend pool.
Add your Web VMs to the backend pool.
Do not add any inbound or outbound rules.
Configure the Health Probe.
Start by going to the Load Balancer you just created
On the left, select Health Probes
Click Add to create a health probe
Give it a unique name
Set Protocol to TCP and Port to 80
Set Interval to 5 seconds

**_Step 10_**

**Security Configuration**
Create a load balancing rule to forward port 80 from the load balancer to your Red Team VNet.
Name: Give the rule an appropriate name that you will recognize later.
IP Version: This should stay on IPv4.
Frontend IP address: There should only be one option here.
Protocol: Protocol is TCP for standard website traffic.
Port: Port is 80.
Backend port: Backend port is also 80.
Backend pool and Health probe: Select your backend pool and your health probe.
Session persistence: This should be changed to Client IP and protocol.
Remember, these servers will be used by the Red Team to practice attacking machines. If the session changes to another server in the middle of their attack, it could stop them from successfully completing their training.
Idle timeout: This can remain the default (4 minutes).
Floating IP: This can remain the default (Disabled).

Create a new security group rule to allow port 80 traffic from the internet to your internal VNet.
Source: Change this your external IPv4 address.
Source port ranges: We want to allow Any source port, because they are chosen at random by the source computer.
Destination: We want the traffic to reach our VirtualNetwork.
service http
Destination port ranges: We only want to allow port 80.
Protocol: Set the standard web protocol of TCP or Any.
Action: Set to Allow traffic.
Name: Choose an appropriate name that you can recognize later.
Remove the security group rule that blocks all traffic on your vnet to allow traffic from your load balancer through.
Remember that when we created this rule we were blocking traffic from the allow rules that were already in place. One of those rules allows traffic from load balancers.
Removing your default deny all rule will allow traffic through.
Verify that you can reach the DVWA app from your browser over the internet.
Open a web browser and enter the front-end IP address for your load balancer with /setup.php added to the IP address.
For example: http://40.122.71.120/setup.php
Note: With the stated configuration, you will not be able to access these machines from another location unless the security Group rule is changed.
![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/backend_pool.JPG)
![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/frontend.JPG)
![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/frontendipconfig.JPG)
![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/health_probe.JPG)
![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/load_balancing_rules.JPG)


**_Step 11_**

**Redundancy Testing**
you need to verify that all of your VMs are added to the backend pool for your load balancer and test whether the website continues work if one of your VMs has a problem.

Verify that all of your VMs are added to the backend pool for your load balancer.

Verify that the DVWA site is up and running and can be accessed from the web.

Position three windows on your screen so you can see the website and the details page of both VMs in your backend pool.

Turn off one of your VMs from the Azure portal. Confirm if you can still access the DVWA website.

Congratulations! You have configured a highly available web server on a secure Vnet using Azure's cloud platform.

**Diagram of my cloud infrastructure  that i created:**

![newpicture](https://github.com/Arina8888/Cloud_computing/blob/main/Capture.JPG)
