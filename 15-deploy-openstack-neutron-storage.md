Create LVM
- sudo pvcreate /dev/[disk]
- sudo vgcreate cinder-volumes /dev/[disk]



Install Dependencies
- sudo apt install cinder-volume python3-mysqldb python3-rtslib-fb targetcli-fb -y

Backup Configuration File (Optional)
- sudo mv /etc/cinder/cinder.conf /etc/cinder/cinder.conf.bak

Write Configuration
- add file /etc/cinder/cinder.conf
  - [DEFAULT]
  - my_ip = 10.0.0.50
  - rootwrap_config = /etc/cinder/rootwrap.conf
  - api_paste_confg = /etc/cinder/api-paste.ini
  - state_path = /var/lib/cinder
  - auth_strategy = keystone
  - transport_url = rabbit://openstack:password@10.0.0.30
  - enable_v3_api = True
  - glance_api_servers = http://10.0.0.30:9292
  - enabled_backends = lvm
  - [lvm]
  - target_helper = lioadm
  - target_protocol = iscsi
  - target_ip_address = 10.0.0.50
  - volume_group = cinder-volumes
  - volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver
  - volumes_dir = $state_path/volumes
  - [database]
  - connection = mysql+pymysql://cinder:password@10.0.0.30/cinder
  - [keystone_authtoken]
  - www_authenticate_uri = http://10.0.0.30:5000
  - auth_url = http://10.0.0.30:5000
  - memcached_servers = 10.0.0.30:11211
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = cinder
  - password = servicepassword
  - [oslo_concurrency]
  - lock_path = $state_path/tmp

Setup permission configuration files
- chmod 640 /etc/cinder/cinder.conf
- chgrp cinder /etc/cinder/cinder.conf

Restart Neutron Service
- sudo systemctl restart cinder-volume && sudo systemctl enable cinder-volume
