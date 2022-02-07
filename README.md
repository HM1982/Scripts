## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

-![Diagram](https://user-images.githubusercontent.com/90741065/152871955-cf994bd2-45bf-40a5-a850-f73028719cf8.PNG)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Aelect portions of the YAML file may be used to install only certain pieces of it, such as Filebeat..

  install_elk.yml
  
---
- name: Config Web elk with Docker
  hosts: elk
  remote_user: sysadmin
  become: true
  tasks:
  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present
  - name: Use more memory
    sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
  - name: download and launch a docker web container
    docker_container:
      name: elk
      image: sebp/elk:761
      state: started
      published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044


This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

This document contains the following details:

Description of the Topology
Access Policies
ELK Configuration
Beats in Use
Machines Being Monitored
How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly avulable, in addition to restricting inbound access to the network.
he load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users — namely, ourselves — will be able to connect in the first place.

What is the advantage of a jump box?
 -A jump box is a secure computer that all admins first connect to before launching any administrative task or use as an origination point to connect to other servers or untrusted environments.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network, as well as watch system metrics, such as CPU usage; attempted SSH logins; sudo escalation failures; etc.
 What does Filebeat watch for? Filebeat is a lightweight shipper for forwarding and centralizing log data. Installed as an agent on your servers, Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
- What does Metricbeat record? Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash. Metricbeat helps you monitor your servers by collecting metrics from the system and services running on the server, such as: Apache.

The configuration details of each machine may be found below.


| Name     | Function   | IP Address | Operating System |
|----------|----------  |------------|------------------|
| Jump Box | Gateway    | 10.0.0.8   | Linux            |
| Web-1    | Web Server | 10.0.0.9   | Linux            |
| Web-2    | Web Server | 10.0.0.11  | Linux            |
| ELK      | web server | 10.2.0.6   | Linux            |

In addition to the above, Azure has provisioned a load balancer in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:


Availability Zone 1: Web-1+ Web-2

Availability Zone 2: ELK

ELK Server Configuration
The ELK VM exposes an Elastic Stack instance. Docker is used to download and manage an ELK container.
Rather than configure ELK manually, we opted to develop a reusable Ansible Playbook to accomplish the task. This playbook is duplicated below.
To use this playbook, one must log into the Jump Box, then issue: ansible-playbook install_elk.yml elk. This runs the install_elk.yml playbook on the elk host.

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the  jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 
-5061 Kibana Port
Machines within the network can only be accessed by each other. The Web-1 and Web-2 VMs send traffic to the ELK server.
- My IP Address: 10.0.0.8
A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 10.0.0.1             |
|  Elk     | No                  | 10.2.0.6             |
|  Web-1   | No                  | 10.0.0.9             |
| Web-2    | No                  | 10.0.0.11            |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
: What is the main advantage of automating configuration with Ansible? Ansible automation helps considerably with the representation of Infrastructure as Code (IAC). IAC involves provisioning and management of computing infrastructure and related configuration through machine-processable definition files

The playbook implements the following tasks:
Free: Ansible is an open-source tool.
Very simple to set up and use: No special coding skills are necessary to use Ansible’s playbooks (more on playbooks later).
Powerful: Ansible lets you model even highly complex IT workflows.
Flexible: You can orchestrate the entire application environment no matter where it’s deployed. You can also customize it based on your needs.
Agentless: You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure.
Efficient: Because you don’t need to install any extra software, there’s more room for application resources on your server.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

The playbook implements the following tasks: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.
- Install docker.io
- Install pip3
- Install Docker python module
 -Increase virtual memory
 -Download and launch a docker

![docker ps](https://user-images.githubusercontent.com/90741065/152872163-7c735e02-d3f7-4bc5-b235-866c80a1b6b5.PNG)


 

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- - Name    IP Addresses
   Web-1	    10.0.0.9
   Web-2	    10.0.0.11

We have installed the following Beats on these machines:
Filebeat
Metricbeat
Packetbeat

These Beats allow us to collect the following information from each machine:
- Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
-Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics.
-Packetbeat: Packetbeat collects packets that pass through the NIC, similar to Wireshark. We use it to generate a trace of all activity that takes place on the network, in case later forensic analysis should be warranted.

The playbook below installs Metricbeat on the target hosts. The playbook for installing Filebeat is not included, but looks essentially identical — simply replace metricbeat with filebeat, and it will work as expected.

----
- name: Installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb

  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.4.0-amd64.deb

  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: Enable and configure system module
    command: filebeat modules enable system

  - name: Setup filebeat
    command: filebeat setup

  - name: Start filebeat service
    command: service filebeat start

  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

To use the playbooks, we must perform the following steps:
SSH into the control node and follow the steps below:
-Copy the playbooks to the Ansible Control Node
-Run each playbook on the appropriate targets


This copies the playbook files to the correct place.
Next, you must create a hosts file to specify which VMs to run each playbook on. Run the commands below:
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.9
10.0.0.11
10.0.0.12

[elk]
10.2.0.6
EOF

- After this, the commands below run the playbook:
$ cd /etc/ansible
$ ansible-playbook install_elk.yml elk
$ ansible-playbook install_filebeat.yml webservers
$ ansible-playbook install_metricbeat.yml webservers

o verify success, wait five minutes to give ELK time to start up.
Then, run: curl http://52.165.158.70:5601. This is the address of Kibana. If the installation succeeded, this command should print HTML to the console.
