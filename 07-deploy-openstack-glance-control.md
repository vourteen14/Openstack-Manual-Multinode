Activate Environtment File
- `````source admin-openrc.sh`````

Create Openstack Project
- `````openstack user create --domain default --project service --password [PASSWORD] glance`````
- `````openstack role add --project service --user glance admin`````
- `````openstack service create --name glance --description "OpenStack Image service" image`````

Create Openstack Endpoint
- `````openstack endpoint create --region RegionOne image public http://[Control-IP]:9292`````
- `````openstack endpoint create --region RegionOne image internal http://[Control-IP]:9292`````
- `````openstack endpoint create --region RegionOne image admin http://[Control-IP]:9292`````
  
Add user glance to mysql
- `````sudo mysql`````
- `````create database glance;`````
- `````grant all privileges on glance.* to glance@'localhost' identified by '[PASSWORD]';`````
- `````grant all privileges on glance.* to glance@'%' identified by '[PASSWORD]';`````
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
  - `````connection = mysql+pymysql://glance:[PASSWORD]@[Control-IP]/glance`````
  - `````[keystone_authtoken]`````
  - `````www_authenticate_uri = http://[Control-IP]:5000`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````memcached_servers = [Control-IP]:11211`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = glance`````
  - `````password = [PASSWORD]`````
  - `````[paste_deploy]`````
  - `````flavor = keystone`````
  
Setup permission configuration files
- `````sudo chmod 640 /etc/glance/glance-api.conf`````
- `````sudo chown root:glance /etc/glance/glance-api.conf`````
  
Sync Glance Database
- `````sudo su -s /bin/bash -c "glance-manage db_sync"`````

Restart service glance
- `````sudo systemctl restart glance-api && sudo systemctl enable glance-api`````

Add Image to glance
- `````wget https://download.cirros-cloud.net/0.5.2/cirros-0.5.2-x86_64-disk.img`````
- `````openstack image create --file cirros-0.5.2-x86_64-disk.img --container-format bare --disk-format qcow2 cirros-0.5.2-x86_64`````
- `````openstack image list > IF Show Image name cirros-0.5.2-x86_64, setup success`````
