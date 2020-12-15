# Project-ELk-Git
elk deployment/git repository 
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
[Elk_Diagram](Diagrams/Elkserver_Diagram.jpg )

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

- Ansible-playbook.yml
- Ansible-config.yml 
- Elk-playbook.yml
- Filebeat-playbook.yml
- Filebeat-config.yml
- Metricbeat-playbook
- Metric-config.yml
- Hosts

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting traffic to the network.
- Load balancer help protect: Availability, Web Traffic, Web Security
- Advantages of Jump-box: Automation, Security, Network Segmentation, Access Control 
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.

- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
- Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address              | Operating System     |
|----------|----------|-------------------------|----------------------|
|Jump-Box  | Gateway  | 10.0.0.4 / 52.162.96.34 | Linux Ubuntu 18.04   |        
|DVWA WEB-1| Vm       | 10.0.0.5                | Linux Ubuntu 18.04   |
|DVWA WEB-2| Vm       | 10.0.0.6                | Linux Ubuntu 18.04   |
|DVWA WEB-3| Vm       | 10.0.0.7                | Linux Ubuntu 18.04   |
|ELK-Vm    | ElkStack | 10.1.0.4 / 13.67.208.176| Linux Ubuntu 18.04   |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the the Jump-Box Provisoner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Home IP to Jumpbox IP 52.162.96.34 through ssh port 22

Machines within the network can only be accessed by port 22.
- The only machine allowed to access the Elk-Vm is the jump-box provisioner 

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box |     yes             |     Home IP          |
| DVWA Vms |     No              |     10.0.0.4         |
| ELK_VM   |     No              |     10.0.0.4         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- In addition to facilitate the configuration of multiple machines, the use of provisioners like ansible reduces considerably human errors. Also, Ansible can be run from the command line and will ensure our provisioning scripts run identically everywhere.

The playbook implements the following tasks:
- Make the remote user become true to not use sudo 
- Install docker.io
- Install python3-pip (pip3)
- Install docker python module
- Change the memory on the ELK VM
- Download and launch a docker elk stack


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

[Screenshot](Ansible/Screenshot_DOCKER_PS.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- DVWA WEB-1 10.0.0.5
- DVWA WEB-2 10.0.0.6
- DVWA WEB-3 10.0.0.7

We have installed the following Beats on these machines:
- filebeat-7.4.0-amd64.deb

These Beats allow us to collect the following information from each machine:
- Security Information and Event Management--eg: Auditbeat collect audit data from your host 
- Metric Data--eg: Elasticsearch fetch internal metrics from Elasticsearch
- Logs--eg: Elasticserach logs collect and parse logs created by Elasticsearch


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- Copy the Filebeat-configuration.yml file to the ELK VM.
- Update Filebeat-configuration file:

Line #1106: 
output.elasticsearch:
hosts: ["10.1.0.4:9200"]
username: "elastic"
password: "changeme"

Line #1806: setup.kibana:
host: "10.1.0.4:5601"

- Update the hosts file to include 
[Elkservers]
 10.1.0.4 ansible_python_interpreter=/usr/bin/python3

- Run the playbook, and navigate to Kibana to check that the installation worked as expected.

-The command to run the playbook:
 ansible-playbook filebeat-playbook.yml

the destination file of the drop in will be the same for the specified configured hosts under [webservers] in the hosts file  

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

 
  - name: install filebeat deb
    command: sudo dpkg -i filebeat-7.6.1-amd64.deb


  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/ansible/filebeat/

  - name: enable and configure system module
    command: sudo filebeat modules enable system

  - name: setup filebeat
    command: sudo filebeat setup

  - name: start filebeat service
    command: sudo service filebeat start

