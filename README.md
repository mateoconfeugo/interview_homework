Introduction:
=============

This is a homework assignment for an interview candidate to
complete as part of initial screening. This is a full vagrant
workspace that will execute the ansible provisioner.  To setup
you environment to run this first execute in this directory:

	. setup_env

Then execute:

	vagrant up

You should get a message:

	'complete me'

If you prefer another config management tool such as puppet
or chef, that is up to the interviewee to setup the provisioner
portion in the Vagrantfile and either provide documentation on
environment setup or automate that process as well.

Key Objectives:
===============

* Install lighttpd
* Configure a lighttpd named vhost via a jinja2 template
* S3 artifact deployment into virtual host document root
  /srv/bogusapp/
* Full automation ("vagrant up" to working site with no
  manual intervention)
* Idempotency

Artifact deployment:
====================

The following access and secret keys have read only access
to the 'bogus-app-homework' bucket in the us-west-2 region:

***** These keys don't work for the forementioned bucket: *****
Access key: AKIAJH2C7CAWKL7BLTBA
Secret key: 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

The artifact to deploy is 'homework.tar.gz'.

Bonus:
======

* Secure the s3 access and secret key, even if it means a
  password is supplied at "vagrant up".  For example if using
  ansible-vault it is acceptable for the user to be required
  to type in a vault password on 'vagrant up'.
* Present some output at the end of the vagrant run with
  local host setup to test the named virtual host.
* Be mindful of how roles or any config files are organized.

                          ____________________

                           ASSIGNMENT FOR FOX

                                by:  matt burns
                          ____________________


Table of Contents
_________________

1 Notes
2 DONE Prerequists
..... 2.0.1 DONE Get the Assignment
..... 2.0.2 DONE Python dependencies
..... 2.0.3 DONE virturalenv
..... 2.0.4 DONE Install Ansible
..... 2.0.5 DONE Install Vagrant
..... 2.0.6 DONE Install Virtualbox
..... 2.0.7 DONE Enable Virtualization in BIOS
..... 2.0.8 DONE What is the Definition of Done for each task
3 DONE Steps
.. 3.1 DONE Create github project
.. 3.2 DONE Layout directories and files
.. 3.3 Possibly useful
.. 3.4 DONE Initial Check In
..... 3.4.1 Tests
.. 3.5 DONE Run Debug Fix
.. 3.6 DONE Check In Working Version
.. 3.7 TODO Bonus Tasks
..... 3.7.1 TODO Use Ansible Vault to get the keys out of the playbook task
..... 3.7.2 TODO Put the ansible repo update into its own role
4 Artifact deployment Info


Please email the completed assignment to me by Monday. Turn around time
should be 2-3 days.


1 Notes
=======

  - Keys are incorrect in the readme - doh
  - Uses Ansible 2.4 as 2.1.0.0 has a bug for getting s3 bucket
  - Put web site in /var/www instead of srv directory because - some
    permission issue


2 DONE Prerequists
==================

2.0.1 DONE Get the Assignment
-----------------------------

* 2.0.1.1 DONE Retrieve via AWS CLI:

  - s3://bogus-app-homework/interview_homework.tar
  - Using the following credentials:
  + AWS_ACCESS_KEY_ID: AKIAI2DB7HIYHKOH2HXA
  + AWS_SECRET_ACCESS_KEY: 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
  These are accomplished by running the about setup_env script


2.0.2 DONE Python dependencies
------------------------------


2.0.3 DONE virturalenv
----------------------


2.0.4 DONE Install Ansible
--------------------------


2.0.5 DONE Install Vagrant
--------------------------

  ,----
  | sudo apt-get install vagrant
  `----


2.0.6 DONE Install Virtualbox
-----------------------------


2.0.7 DONE Enable Virtualization in BIOS
----------------------------------------


2.0.8 DONE What is the Definition of Done for each task
-------------------------------------------------------


3 DONE Steps
============

3.1 DONE Create github project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


3.2 DONE Layout directories and files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* 3.2.0.1 DONE Vagrant configuration

  ,----
  | VAGRANTFILE_API_VERSION = "2"
  |
  | this_role = 'homework'
  | this_env = 'dev'
  |
  | ENV['ANSIBLE_CONFIG'] = "ansible.cfg"
  | ENV['ANSIBLE_DIR'] = "ansible/"
  |
  | vagrant_ip = "192.168.52.101"
  |
  | boxes = {
  |  "centos7" => "centos/7",
  | }
  |
  | Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  |   boxes.each do |distribution, box|
  |     config.ssh.insert_key = false
  |     config.ssh.forward_agent = true
  |
  |     config.vm.provider :virtualbox do |vb|
  |       vb.customize ["modifyvm", :id, "--cpus", "1"]
  |       vb.customize ["modifyvm", :id, "--memory", "2048"]
  |       vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
  |       vb.customize ["modifyvm", :id, "--nictype1", "virtio" ]
  |       vb.customize ["modifyvm", :id, "--nictype2", "virtio" ]
  |     end
  |
  |     config.vm.define "#{distribution}-#{this_role}" do |host|
  |       host.vm.hostname = this_role
  |       host.vm.box = box
  |       host.vm.network :private_network, ip: vagrant_ip
  |       host.vm.provision "ansible" do |ansible|
  |         ansible.extra_vars = { ENV: this_env }
  |         ansible.playbook = "ansible/homework.yml"
  |         ansible.ask_sudo_pass = true
  |         ansible.groups = {
  |           "#{this_role}-#{this_env}" => [ "#{distribution}-#{this_role}" ]
  |         }
  |       end
  |     end
  |   end
  | end
  `----


* 3.2.0.2 DONE Ansible configuration

  ,----
  | [defaults]
  | inventory = .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory
  | remote_user = vagrant
  | pipelining = yes
  | roles_path = $TECHOPS_AWS/ansible/roles
  | deprecation_warnings = False
  | [privilege_escalation]
  | become = yes
  `----


* 3.2.0.3 DONE top level yaml   file homework.yml  - not used

  ,----
  | ---
  | - name: Install lighttpd and deploy website via tarball residing on s3 bucket
  |   hosts: homework-dev
  |   remote_user: root
  | #  become: yes
  | #  become_method: sudo
  |   vars:
  |     instructions:
  |
  |   tasks:
  |   - debug:
  |       msg:
  |        - "To test locally add the following line to the /etc/hosts file on the host"
  |        - "bogusapp.com   192.168.52.101"
  |        - "Then point your webbrowser at bogusapp.com"
  |
  |   roles:
  |     - common
  |     - lighttpd
  `----


* 3.2.0.4 DONE hosts - not used!

  ,----
  | ---
  | all:
  |   hosts:
  |     centos7-homework:
  |       ansible_ssh_host: 127.0.0.1
  |       ansible_ssh_port: 2222
  |       ansible_ssh_user: vagrant
  |       ansible_ssh_private_key_file: /home/mburns/.vagrant.d/insecure_private_key
  |   children:
  |     webservers:
  |       hosts:
  |         centos7-homework:
  |     homework-dev:
  |       hosts:
  |         centos7-homework:
  `----


* 3.2.0.5 DONE group_vars directory

  + 3.2.0.5.1 DONE all

    ,----
    | ---
    | lighttpd_port: 80
    | lighttpd_user:  lighttpd
    | lighttpd_group: lighttpd
    | s3_repository: bogus-app-homework
    | aws_region: us-west-2
    | tarball: homework.tar.gz
    | web_root_dir: /var/www/html
    | cache_root_dir: /var/cache
    | log_root_dir: /var/log/
    | pid_dir: /var/run
    | srv_dir: /srv
    | server_bind_ip: 0.0.0.0
    | staging_dir: /tmp
    | bogus_domain: bogusapp
    `----


* 3.2.0.6 DONE roles directory

  + 3.2.0.6.1 DONE Common

    - 3.2.0.6.1.1 DONE handlers

      ,----
      | ---
      | - name: restart iptables
      |   service: name=iptables state=restarted
      `----


    - 3.2.0.6.1.2 DONE tasks

      - name: Install EPEL repo.  yum: name:
        [https://dl.fedoraproject.org/pub/epel/epel-release-latest]-{{
        ansible_distribution_major_version }}.noarch.rpm state: present

      - name: Import EPEL GPG key.  rpm_key: key:
        /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-{{
        ansible_distribution_major_version }} state: present

      ,----
      | - name: insert iptables rule for lighttpd
      |   lineinfile: dest=/etc/sysconfig/iptables create=yes state=present regexp="{{ lighttpd_port }}" insertafter="^:OUTPUT "
      |               line="-A INPUT -p tcp  --dport {{ lighttpd_port }} -j  ACCEPT"
      |   notify: restart iptables
      |
      | - name: Install epel
      |   yum:
      |     name: epel-release
      |     state: present
      `----


  + 3.2.0.6.2 DONE lighttpd

    - change bind server ip to "0.0.0.0" so can view via host web
      browser pointing at virtual box instance set to the ip above in
      the vagrant file


    - 3.2.0.6.2.1 DONE templates

      * 3.2.0.6.2.1.1 DONE lighttpd.conf

        ,----
        | server.document-root = "/var/www/"
        | mimetype.assign = ("" => "text/html")
        | server.bind = "0.0.0.0"
        | server.port  =  80
        | server.modules              = (
        |                               "mod_rewrite",
        |                               "mod_redirect",
        |                               "mod_access",
        |                               "mod_status",
        |                               "mod_simple_vhost",
        |                               "mod_accesslog",
        |                               "mod_indexfile"
        |                                )
        |
        | server.indexfiles = ("index.html")
        |
        | simple-vhost.server-root   = "/var/www/vhosts/"
        | simple-vhost.default-host  = "{{ bogus_domain }}.com"
        | simple-vhost.document-root = "public"
        |
        | server.errorlog = "/var/log/lighttpd/error.log"
        | debug.log-file-not-found = "enable"
        | debug.log-request-header = "enable"
        | debug.log-request-handling = "enable"
        | debug.log-response-header = "enable"
        `----


    - 3.2.0.6.2.2 DONE handlers

      ,----
      | ---
      | - name: restart lighttpd
      |   service: name=lighttpd state=restarted enabled=yes
      `----


    - 3.2.0.6.2.3 DONE tasks

      ,----
      | - name: Install lighttpd
      |   yum:
      |     name: lighttpd
      |     state: present
      |
      | - name: Copy main configuration for lighttpd
      |   template: src=simple-lighttpd.conf.j2 dest=/etc/lighttpd/lighttpd.conf
      |
      | - name: Retrieve homework web site tarball from s3 bucket
      |   connection : local
      |   aws_s3:
      |     bucket: bogus-app-homework
      |     object: homework.tar.gz
      |     dest: "{{ staging_dir }}/homework.tar.gz"
      |     mode: get
      |     aws_access_key:  AKIAI2DB7HIYHKOH2HXA
      |     aws_secret_key:  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
      |     region: us-west-2
      |
      | - name: Creates directory
      |   file:
      |     path: "/var/www/vhosts"
      |     state: directory
      |     owner: "{{ lighttpd_user }}"
      |     group: "{{ lighttpd_group }}"
      |     mode: 0744
      |
      | - name: Creates directory
      |   file:
      |     path: "/var/www/vhosts/{{ bogus_domain }}.com"
      |     state: directory
      |     owner: "{{ lighttpd_user }}"
      |     group: "{{ lighttpd_group }}"
      |     mode: 0744
      |
      | - name: Extract homework.tar.gz
      |   unarchive:
      |     src: "{{ staging_dir }}/homework.tar.gz"
      |     dest: "/var/www/vhosts/{{ bogus_domain }}.com/"
      |     owner: "{{ lighttpd_user }}"
      |     group: "{{ lighttpd_group }}"
      |     mode: 0744
      |   notify:
      |     - restart lighttpd
      |
      | - name: ensure lighttpd is running (and enable it at boot)
      |   service: name=lighttpd state=started enabled=yes
      `----


3.3 Possibly useful
~~~~~~~~~~~~~~~~~~~

  sudo setsebool -P httpd_setrlimit on
  - name: Clean up the tarball file: state: absent path:
    /srv/bogusapp/homework.tar.gz


3.4 DONE Initial Check In
~~~~~~~~~~~~~~~~~~~~~~~~~

3.4.1 Tests
-----------

* 3.4.1.1 yum lighttpd not present

  ,----
  | ansible homework-dev  -m yum -a "name=lighttpd state=present"
  | centos7-homework | FAILED! => {
  |     "changed": false,
  |     "failed": true,
  |     "msg": "No Package matching 'lighttpd' found available, installed or updated",
  |     "rc": 0,
  |     "results": []
  | }
  `----


* 3.4.1.2 firewall rule not present

  ,----
  | ansible homework-dev -a "iptables --list-rules"
  | centos7-homework | SUCCESS | rc=0 >>
  | -P INPUT ACCEPT
  | -P FORWARD ACCEPT
  | -P OUTPUT ACCEPT
  `----


3.5 DONE Run Debug Fix
~~~~~~~~~~~~~~~~~~~~~~


3.6 DONE Check In Working Version
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


3.7 TODO Bonus Tasks
~~~~~~~~~~~~~~~~~~~~

3.7.1 TODO Use Ansible Vault to get the keys out of the playbook task
---------------------------------------------------------------------


3.7.2 TODO Put the ansible repo update into its own role
--------------------------------------------------------


4 Artifact deployment Info
==========================

  - Bucket: 'bogus-app-homework'
  - Region: us-west-2
  - <-- Wrong access key in the README
  - <-- Wrong secret key in the README
  - Object: 'homework.tar.gz'.
