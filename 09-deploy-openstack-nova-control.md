Activate Environtment File
- `````source admin-openrc.sh`````

Create Openstack Project
- `````openstack user create --domain default --project service --password [PASSWORD] nova`````
- `````openstack role add --project service --user nova admin`````
- `````openstack service create --name nova --description "OpenStack Compute service" compute`````

Create Openstack Endpoint
- `````openstack endpoint create --region RegionOne compute public http://[Control-IP]:8774/v2.1/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne compute internal http://[Control-IP]:8774/v2.1/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne compute admin http://[Control-IP]:8774/v2.1/%\(tenant_id\)s`````

Add user placement to mysql
- `````sudo mysql`````
- `````create database nova;`````
- `````grant all privileges on nova.* to nova@'localhost' identified by '[PASSWORD]';`````
- `````grant all privileges on nova.* to nova@'%' identified by '[PASSWORD]';`````
- `````create database nova_api;`````
- `````grant all privileges on nova_api.* to nova@'localhost' identified by '[PASSWORD]';`````
- `````grant all privileges on nova_api.* to nova@'%' identified by '[PASSWORD]';`````
- `````create database nova_cell0;`````
- `````grant all privileges on nova_cell0.* to nova@'localhost' identified by '[PASSWORD]';````` 
- `````grant all privileges on nova_cell0.* to nova@'%' identified by '[PASSWORD]';`````
- `````flush privileges;`````
- `````exit;`````
  
Install Dependencies
- `````sudo apt install nova-api nova-conductor nova-scheduler nova-novncproxy python3-novaclient -y`````
  
Backup Configuration File (Optional)
-  `````sudo mv /etc/nova/nova.conf /etc/nova/nova.conf.bak`````
  
Write/Edit Configuration
- add file /etc/nova/nova.conf
  - `````[DEFAULT]`````
  - `````my_ip = [Control-IP]`````
  - `````state_path = /var/lib/nova`````
  - `````enabled_apis = osapi_compute,metadata`````
  - `````log_dir = /var/log/nova`````
  - `````transport_url = rabbit://openstack:[PASSWORD]@[Control-IP]:5672/`````
  - `````[api]`````
  - `````auth_strategy = keystone`````
  - `````[glance]`````
  - `````api_servers = http://[Control-IP]:9292`````
  - `````[oslo_concurrency]`````
  - `````lock_path = $state_path/tmp`````
  - `````[api_database]`````
  - `````connection = mysql+pymysql://nova:[PASSWORD]@[Control-IP]/nova_api`````
  - `````[database]`````
  - `````connection = mysql+pymysql://nova:[PASSWORD]@[Control-IP]/nova`````
  - `````[keystone_authtoken]`````
  - `````www_authenticate_uri = http://[Control-IP]:5000`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````memcached_servers = [Control-IP]:11211`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = nova`````
  - `````password = [PASSWORD]`````
  - `````[placement]`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````os_region_name = RegionOne`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = placement`````
  - `````password = [PASSWORD]`````
  - `````[wsgi]`````
  - `````api_paste_config = /etc/nova/api-paste.ini`````

Setup permission configuration files
- `````sudo chmod 640 /etc/nova/nova.conf`````
- `````sudo chown root:nova /etc/nova/nova.conf`````
  
Sync Nova Database
- `````sudo su -s /bin/bash -c "nova-manage api_db sync"`````
- `````sudo su -s /bin/bash -c "nova-manage cell_v2 map_cell0"`````
- `````sudo su -s /bin/bash -c "nova-manage db sync"`````
- `````sudo su -s /bin/bash -c "nova-manage cell_v2 create_cell --name cell1"`````
  
Restart Nova Service
- `````systemctl restart nova-api && systemctl enable nova-api`````
- `````systemctl restart nova-conductor && systemctl enable nova-conductor`````
- `````systemctl restart nova-scheduler && systemctl enable nova-scheduler`````
- `````systemctl restart nova-novncproxy && systemctl enable nova-novncproxy````` 

Check Nova Service Status
- `````openstack compute service list````` >> It should be shown nova-conductor and nova-scheduller
