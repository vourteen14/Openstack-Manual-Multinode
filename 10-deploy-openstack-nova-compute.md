Install Dependecies
- sudo apt install nova-compute nova-compute-kvm qemu-system-data -y

Backup Configuration File
- sudo mv /etc/nova/nova.conf /etc/nova/nova.conf.bak

Write Configuration
- add file /etc/nova/nova.conf
  - [DEFAULT]
  - my_ip = 10.0.0.51
  - state_path = /var/lib/nova
  - enabled_apis = osapi_compute,metadata
  - log_dir = /var/log/nova
  - transport_url = rabbit://openstack:password@10.0.0.30
  - [api]
  - auth_strategy = keystone
  - [vnc]
  - enabled = True
  - server_listen = 0.0.0.0
  - server_proxyclient_address = $my_ip
  - novncproxy_base_url = http://10.0.0.30:6080/vnc_auto.html
  - [glance]
  - api_servers = http://10.0.0.30:9292
  - [oslo_concurrency]
  - lock_path = $state_path/tmp
  - [cinder]
  - os_region_name = RegionOne
  - [keystone_authtoken]
  - www_authenticate_uri = http://10.0.0.30:5000
  - auth_url = http://10.0.0.30:5000
  - memcached_servers = 10.0.0.30:11211
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = nova
  - password = servicepassword
  - [placement]
  - auth_url = http://10.0.0.30:5000
  - os_region_name = RegionOne
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = placement
  - password = servicepassword
  - [wsgi]
  - api_paste_config = /etc/nova/api-paste.ini

Edit file /etc/nova/nova-compute.conf
- Sesuaikan seperti dibawah
  - ...
  - [libvirt]
  - virt_type=qemu

Setup permission configuration files
- sudo chmod 640 /etc/nova/nova.conf
- sudo chgrp nova /etc/nova/nova.conf
- sudo systemctl restart nova-compute
