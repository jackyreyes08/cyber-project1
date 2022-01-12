 ## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/96931132/148710697-350c79f4-dcd5-4eb6-9043-3470971b690d.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

Playbook:

name: Config Web VM with Docker
hosts: webservers
become: true
tasks:

name: docker.io
apt:
update_cache: yes
name: docker.io
state: present

name: Install pip3
apt:
name: python3-pip
state: present

name: Install Docker python module
pip:
name: docker
state: present

name: download and launch a docker web container
docker_container:
name: dvwa
image: cyberxsecurity/dvwa
state: started
published_ports: 80:80

name: Enable docker service
systemd:
name: docker
enabled: yes
 
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly effective_, in addition to restricting traffic_ to the network.
- _TODO: What aspect of security do load balancers protect? They prevent unauthorized traffic from reaching servers.
 What is the advantage of a jump box?_ They add an extra layer of protection. They are placed between a secure zone and a DMZ to provide transparent management of devices. Acts as a single audit point for traffic.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the config files and system files__.
- _TODO: What does Filebeat watch for?_Watches for Log files and log events.
- _TODO: What does Metricbeat record?_ Metricbeat helps you monitor your servers by collecting metrics from the system and services running on the server, such as: Apache.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| web1     | webserver| 10.0.0.5   | Linux            |
| web2     | webserver| 10.0.0.6   | Linux            |
| Elk      | webserver| 10.2.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _TODO: JumpBoxProvisioner-ip 23.99.229.174

Machines within the network can only be accessed by Jumpbox.
- _TODO: Which machine did you allow to access your ELK VM? My personal What was its IP address?_97.99.134.104

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes/No              | 10.0.0.4             |
|  web1    |        no           |    13.67.229.254     |
|  web2    |        no           |    13.67.229.254     |
|   Elk    |        Yes(ssh)     |    172.17.0.1        |
  
  
  
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_the flexibility it allows changes to be made from any VM.

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Intalls docker.io
- Installs phython3
- Installs docker Python module downloads and launches containers.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: 10.0.0.4, 10.0.0.5, 10.0.06, 10.2.0.4

We have installed the following Beats on these machines:

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
Filebeat monitors log files and log events; log data. Metricbeat collects cpu to memory information it is a lightweight way to send statistics. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the ansible config file to run playbooks.
- Update the ansible host file to include...
- Run the playbook, and navigate to jumpbox to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_ Elk-playbook.yml
    
- _Which file do you update to make Ansible run the playbook on a specific machine? The elk playbook elk-playbook.yml file
How do I specify which machine to install the ELK server on versus which to install Filebeat on?_by using the different Ips assigned to the diff servers.
- _Which URL do you navigate to in order to check that the ELK server is running? http:// 172.17.0.1:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._

Elk\Config.yml
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azureuser
  become: true
  tasks:
    \# Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
      \# Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
      \# Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present
      \# Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=26214

filebeat-playbook.yml
---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    \# Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

    \# Use command module
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

    \# Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

    \# Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system

    \# Use command module
  - name: Setup filebeat
    command: filebeat setup
    \# Use command module
  - name: Setup filebeat
    command: filebeat setup

    \# Use command module
  - name: Start filebeat service
    command: service filebeat start

    \# Use systemd module
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes
      
Metricbeat  
---
- name: Installing and Launch metricbeat
  hosts: webservers
  become: yes
  tasks:
    \# Use command module
  - name: Download metricbeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

    \# Use command module
  - name: Install metricbeat .deb
    command: dpkg -i metricbeat-7.6.1-amd64.deb

    \# Use copy module
  - name: Drop in metricbeat.yml
    copy:
      src: /etc/ansible/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    \# Use command module
  - name: Enable and Configure docker Module
    command: metricbeat modules enable docker

    \# Use command module
  - name: Setup metricbeat
    command: metricbeat setup

    \# Use command module
  - name: Start metricbeat service
    command: service metricbeat start

    \# Use systemd module
  - name: Enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes

playbook
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:
    - name: docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

    - name: Install pip3
      apt:
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
        






