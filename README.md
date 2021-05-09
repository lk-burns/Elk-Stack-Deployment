# Elk-Stack-Deployment
Project 1 - Automated Elk Stack Deployment


## Automated ELK Stack Deployment

The files contained in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/75952979/117557330-ae61b800-b03f-11eb-8e40-433abf45f41a.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above or alternatively, pieces of each playbook file may be used to install certain applications, such as Filebeat or Metricbeat.

  - Install-Elk.yml
  - Filebeat-Playbook.yml
  - Metricbeat-Playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network. A load balancer efficiently distributes network traffic within a multi-server environment, preventing too many requests from negatively impacting a server. It allows for work to be distributed evenly and ensures application responsiveness and availability. In addition to the load balancer, a jump box was designed to provide a secure access point for authorized users to connect to the DVWA and other environments.

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

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from my personal IP address: 108.XXX.XX.XX

Machines within the network can only be accessed by each other. For example, the ELK server can receive traffic from the VMs of DVWA 1 and DVWA 2.

A summary of the access policies in place can be found in the table below.

|   Name   | Publicly Accessible | Allowed IP Addresses |
|:--------:|:-------------------:|:--------------------:|
| Jump Box |         Yes         |    108.XXX.XX.XX     |
| DVWA 1   |          No         |       10.0.0.5       |
| DVWA 2   |          No         |       10.0.0.6       |
| ELK      |          No         |       10.1.0.4       |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it reduces human error and coding mistakes, eliminates repetitive tasks, as well as saves time and effort that can be applied elsewhere.

The Install-Elk playbook implements the following tasks:
- Downloads and configures ELK VM with Docker container
  - In header of Ansible playbook, specify hosts and remote users
  - Ensures system requirements for the ELK container
    - Install docker.io
    - Install python3-pip
    - Install docker (Docker Python pip module)
    - Use sysctl module to increase memory of container
- Downloads and launches sebp/elk:761 Docker container
  - Designates published ports to start container
     - 5601:5601
     - 9200:9200
     - 5044:5044 
- Configures service Docker to start on boot 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/75952979/117557926-3fd42880-b046-11eb-9dae-d9c8ed49658e.png)


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

- SSH into the Ansible control node and copy the following playbook files to their corresponding directories:
  - Install_Elk.yml --> /etc/ansible
  - Filebeat-Playbook.yml --> /etc/ansible/roles
  - Metricbeat-Playbook.yml --> /etc/ansible/roles


You can also copy the playbooks with Git to their destination:

```bash
$ cd /etc/ansible
$ mkdir files
# Clone Repository + IaC Files
$ git clone https://github.com/lk-burns/Elk-Stack-Deployment.git
# Move Playbooks and hosts file into `/etc/ansible`
$ cp Elk-Stack-Deployment/playbooks/* .
$ cp Elk-Stack-Deployment/files/* ./files
```

- Create a `hosts` file to specify which VMs to run each playbook on. Run the commands below:

```bash
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.5
10.0.0.6

[elk]
10.1.0.4
EOF
```

- Update the 'hosts' file located in /etc/ansible/ to include the private IP addresses of the ELK and DVWA servers, in addition to the command 'ansible_python_interpreter=/usr/bin/python3' beside each IP address under the [webservers] header line. Make sure the [webservers] header line is uncommented.

![image](https://user-images.githubusercontent.com/75952979/117558267-ca6a5700-b049-11eb-98a5-873a4cc3c468.png)


- Run the playbooks with the following commands:
  - $ cd /etc/ansible
  - $ ansible-playbook Install_Elk.yml elk
  - $ ansible-playbook Filebeat-Playbook.yml webservers
  - $ ansible-playbook Metricbeat-Playbook.yml webservers

It may take a few minutes for the ELK server to start up so be patient! 

Navigate to http://52.177.223.71:5601/app/Kibana which will direct you to Kibana. If installation was successful, this will demonstrate that the ELK server is running by printing HTMl code to the console.

![Uploading image.png…]()
![image](https://user-images.githubusercontent.com/75952979/117558267-ca6a5700-b049-11eb-98a5-873a4cc3c468.png)

![image](https://user-images.githubusercontent.com/75952979/117559279-f4277c00-b051-11eb-9965-edc158a3cf00.png)

![image](https://user-images.githubusercontent.com/75952979/117559285-03a6c500-b052-11eb-8942-a6b13090e437.png)









- Copy the Filebeat and Metricbeat configuration files to /etc/ansible/files/. Edit both files to include your ELK server's private IP address and port 9200 on line 1106 and port 5601 on line 1806.

![image](https://user-images.githubusercontent.com/75952979/117558599-d3a8f300-b04c-11eb-8045-d7f1726f902b.png)

![image](https://user-images.githubusercontent.com/75952979/117558714-a3158900-b04d-11eb-820a-d8d8920492a7.png)





- Change the 'ansible.cfg' file located in /etc/ansible with selected administrator user information for SSH connections. This can be updated under the [remote_user] option. Make sure the [remote_user] line is uncommented.

![image](https://user-images.githubusercontent.com/75952979/117558219-60ea4880-b049-11eb-8774-e30f6b370806.png)


- Change the 'ansible.cfg' file located in /etc/ansible with selected administrator user information for SSH connections. This can be updated under the [remote_user] option. Make sure the [remote_user] line is uncommented.
![image](https://user-images.githubusercontent.com/75952979/117558412-284b6e80-b04b-11eb-82eb-f0cb906de1d8.png)













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
