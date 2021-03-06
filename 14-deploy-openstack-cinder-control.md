Activate Environtment File
- `````source admin-openrc.sh`````

Create Openstack Project
- `````openstack user create --domain default --project service --password [PASSWORD] cinder`````
- `````openstack role add --project service --user cinder admin`````
- `````openstack service create --name cinderv3 --description "OpenStack Block Storage" volumev3`````

Create Openstack Endpoint
- `````openstack endpoint create --region RegionOne volumev3 public http://[Control-IP]:8776/v3/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne volumev3 internal http://[Control-IP]:8776/v3/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne volumev3 admin http://[Control-IP]:8776/v3/%\(tenant_id\)s`````

Add user cinder to mysql
- `````sudo mysql`````
- `````create database cinder;`````
- `````grant all privileges on cinder.* to cinder@'localhost' identified by '[PASSWORD]';`````
- `````grant all privileges on cinder.* to cinder@'%' identified by '[PASSWORD]';`````
- `````flush privileges;`````
- `````exit;`````
  
Install Dependencies
- `````sudo apt install cinder-api cinder-scheduler python3-cinderclient -y`````

Backup Configuration File (Optional)
- `````sudo mv /etc/cinder/cinder.conf /etc/cinder/cinder.conf.bak`````
  
Write Configuration
- add file /etc/cinder/cinder.conf
  - `````[DEFAULT]`````
  - `````my_ip = [Control-IP]`````
  - `````rootwrap_config = /etc/cinder/rootwrap.conf`````
  - `````api_paste_confg = /etc/cinder/api-paste.ini`````
  - `````state_path = /var/lib/cinder`````
  - `````auth_strategy = keystone`````
  - `````transport_url = rabbit://openstack:[PASSWORD]@[Control-IP]:5672`````
  - `````enable_v3_api = True`````
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

Sync Neutron Database
- `````sudo su -s /bin/bash -c "cinder-manage db sync"`````
  
Restart Cinder Service
- `````sudo systemctl restart cinder-scheduler && sudo systemctl restart cinder-scheduler`````
  
Edit file admin-openrc.sh
- Sesuaikan/tambahkan seperti dibawah
  - `````export OS_VOLUME_API_VERSION=3`````
  
Re-Activate Environtment File
- `````source admin-openrc.sh`````
 
Check Cinder Service Status
- `````openstack volume service list`````
