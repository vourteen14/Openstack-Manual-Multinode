Install Dependencies
- `````sudo apt install cinder-volume python3-mysqldb python3-rtslib-fb targetcli-fb thin-provisioning-tools -y`````

Create LVM
- `````sudo pvcreate /dev/[disk]`````
- `````sudo vgcreate cinder-volumes /dev/[disk]`````

Backup Configuration File (Optional)
- `````sudo mv /etc/cinder/cinder.conf /etc/cinder/cinder.conf.bak`````

Write Configuration
- add file /etc/cinder/cinder.conf
  - `````[DEFAULT]`````
  - `````my_ip = [Storage-IP]`````
  - `````rootwrap_config = /etc/cinder/rootwrap.conf`````
  - `````api_paste_confg = /etc/cinder/api-paste.ini`````
  - `````state_path = /var/lib/cinder`````
  - `````auth_strategy = keystone`````
  - `````transport_url = rabbit://openstack:[PASSWORD]@[Control-IP]`````
  - `````enable_v3_api = True`````
  - `````glance_api_servers = http://[Control-IP]:9292`````
  - `````enabled_backends = lvm`````
  - `````[lvm]`````
  - `````target_helper = lioadm`````
  - `````target_protocol = iscsi`````
  - `````target_ip_address = [Storage-IP]`````
  - `````volume_group = cinder-volumes`````
  - `````volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver`````
  - `````volumes_dir = $state_path/volumes`````
  - `````[database]`````
  - `````connection = mysql+pymysql://cinder:[PASSWORD]@[Control-IP]/cinder`````
  - `````[keystone_authtoken]`````
  - `````www_authenticate_uri = http://[Control-IP]:5000`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````memcached_servers = [Control-IP]:11211`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = cinder`````
  - `````password = [PASSWORD]`````
  - `````[oslo_concurrency]`````
  - `````lock_path = $state_path/tmp`````

Setup permission configuration files
- `````sudo chmod 640 /etc/cinder/cinder.conf`````
- `````sudo chgrp cinder /etc/cinder/cinder.conf`````

Restart Neutron Service
- `````sudo systemctl restart cinder-volume && sudo systemctl enable cinder-volume`````
