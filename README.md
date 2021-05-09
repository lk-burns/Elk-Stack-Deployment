# Elk-Stack-Deployment
Project 1 - Automated Elk Stack Deployment


## Automated ELK Stack Deployment

The files contained in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/75952979/117557330-ae61b800-b03f-11eb-8e40-433abf45f41a.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above or alternatively, pieces of each playbook file may be used to install certain applications, such as Filebeat or Metricbeat.

  - _TODO: Enter the playbook file._

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network. A load balancer efficiently distributes network traffic within a multi-server environment, preventing too many requests from negatively impacting a server. It allows for work to be distributed evenly and ensures application responsiveness and availability. In addition to the load balancer, a jump box was designed to provide a secure access point for authorized users to connect to the DVWA
and other potentially untrusted environments.

Integrating an ELK (Elasticsearch-Logstash-Kibana) server allows users to easily monitor the vulnerable VMs for changes to their file systems on the network, record log events, and periodically collect and assess system and service metrics (i.e. attempted SSH logins, sudo escalation failures, and CPU usage).

The configuration details of each machine may be found below.

|   Name   |  Function  | IP Address | Operating System |
|:--------:|:----------:|:----------:|:----------------:|
| Jump Box |   Gateway  |  10.0.0.7  |       Linux      |
| DVWA 1   | Web Server |  10.0.0.5  |       Linux      |
| DVWA 2   | Web Server |  10.0.0.6  |       Linux      |
| ELK      | Monitoring |  10.1.0.4  |       Linux      |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP address: 137.135.93.69

Machines within the network can only be accessed by each other: the ELK server can receive traffic from the VMs of DVWA 1 and DVWA 2.

A summary of the access policies in place can be found in the table below.

|   Name   | Publicly Accessible | Allowed IP Addresses |
|:--------:|:-------------------:|:--------------------:|
| Jump Box |         Yes         |    137.135.93.69     |
| DVWA 1   |          No         |       10.0.0.5       |
| DVWA 2   |          No         |       10.0.0.6       |
| ELK      |          No         |       10.1.0.4       |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it reduces human error and coding mistakes, eliminates repetitive tasks, as well as saves time and effort that can be applied elsewhere.

The playbook implements the following tasks:
- Download and configure ELK VM with Docker container
  - In header of Ansible playbook, specify hosts and remote users
  - Ensure system requirements for the ELK container
    - Install docker.io
    - Install python3-pip
    - Install docker (Docker Python pip module)
    - Use sysctl module to increase memory of container
- Download and launch sebp/elk:761 Docker container
  - Designate published ports to start container
     - 5601:5601
     - 9200:9200
     - 5044:5044 
- Configure service Docker to start on boot 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following virtual machines: DVWA 1 (10.0.0.5) abd DVWA 2 (10.0.0.6).

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- **Filebeat:** collects and ships log files from the container including audit logs, gc logs, server logs, depreciation logs, and slow logs.
- **Metricbeat:** periodically collects system-wide metrics on CPU, disk, and memory usage, network utilizations, as well as detects failed sudo escalations and SSH login attempts.

### Using the Playbooks
In order to use the playbooks, an Ansible control node should be configured onto the jump box. Assuming you have such a control node provisioned: 

- SSH into the Ansibe control node and copy the following playbook files to their corresponding directories. Ensure the correct 'hosts' name id designated (webservers vs. elk):
  - install_elk.yml --> /etc/ansible
  - filebeat-playbook.yml --> /etc/ansible/roles  (installs both Filebeat and Metricbeat)

Ensure that 
  
- Update the 'hosts' file located in /etc/ansible/ to include the internal IP address of the ELK and DVWA servers, in addition to the command 'ansible_python_interpreter=/usr/bin/python3' beside each IP under the [webservers] header line. Make sure the [webservers] header line is uncommented.

- Change the 'ansible.cfg' file located in /etc/ansible with selected administrator user information for SSH connections. This can be updated under the [remote_user] option. Make sure the [remote_user] line is uncommented.

- Run the playbooks with the following commands:
  - $ cd /etc/ansible
  - $ ansible-playbook install_elk.yml
  - $ ansible-playbook filebeat-playbook.yml

It may take a few minutes for the ELK server to start up so be patient! 

Navigate to http://52.177.223.71:5601 which will direct you to Kibana. If installation was successful, this will demonstrate that the ELK server is running by printing HTMl code to the console.









Uncomment the [webservers] header line.


Add the internal IP address under the [webservers] header.

- Run the playbook, and navigate to the Filebeat installation page on the ELK server GUI to check that the installation worked as expected.

After the ELK container is installed, SSH to your container and double check that your `elk-docker` container is running.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- 
- _Which URL do you navigate to in order to check that the ELK server is running?


_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
