Create Openstack Project
- `````openstack user create --domain default --project service --password <Password> glance`````
- `````openstack role add --project service --user glance admin`````
- `````openstack service create --name glance --description "OpenStack Image service" image`````

Create Openstack Endpoint
- `````openstack endpoint create --region RegionOne image public http://30.30.30.251:9292`````
- `````openstack endpoint create --region RegionOne image internal http://30.30.30.251:9292`````
- `````openstack endpoint create --region RegionOne image admin http://30.30.30.251:9292`````
  
Add user glance to mysql
- `````sudo mysql`````
- `````create database glance;`````
- `````grant all privileges on glance.* to glance@'localhost' identified by '<Password>';`````
- `````grant all privileges on glance.* to glance@'%' identified by '<Password>';`````
- `````flush privileges;`````
- `````exit;`````

Install Dependencies
- `````sudo apt install glance -y`````

Backup Configuration File
- `````sudo mv /etc/glance/glance-api.conf /etc/glance/glance-api.conf.bak`````

Write Configuration 
- add file /etc/glance/glance-api.conf
 - `````[DEFAULT]`````
 - `````bind_host = 0.0.0.0`````
 - `````[glance_store]`````
 - `````stores = file,http`````
 - `````default_store = file`````
 - `````filesystem_store_datadir = /var/lib/glance/images/`````
 - `````[database]`````
 - `````connection = mysql+pymysql://glance:<Password>@30.30.30.251/glance`````
 - `````[keystone_authtoken]`````
 - `````www_authenticate_uri = http://30.30.30.251:5000`````
 - `````auth_url = http://30.30.30.251:5000`````
 - `````memcached_servers = 30.30.30.251:11211`````
 - `````auth_type = password`````
 - `````project_domain_name = default`````
 - `````user_domain_name = default`````
 - `````project_name = service`````
 - `````username = glance`````
 - `````password = <Password>`````
 - `````[paste_deploy]`````
 - `````flavor = keystone`````
  
Setup permission configuration files
- `````sudo chmod 640 /etc/glance/glance-api.conf`````
- `````sudo chown root:glance /etc/glance/glance-api.conf`````
  
Sync Glance Database
- `````sudo su -s /bin/bash glance -c "glance-manage db_sync"`````

Restart service glance
- `````sudo systemctl restart glance-api && sudo systemctl enable glance-api`````
