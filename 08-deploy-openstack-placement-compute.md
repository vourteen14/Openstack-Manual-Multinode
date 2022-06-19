Activate Environtment File
- `````source admin-openrc.sh`````

Create Openstack Project
- `````openstack user create --domain default --project service --password [PASSWORD] placement`````
- `````openstack role add --project service --user placement admin`````
- `````openstack service create --name placement --description "OpenStack Compute Placement service" placement`````

Create Openstack Endpoint
- `````openstack endpoint create --region RegionOne placement public http://[Control-IP]:8778`````
- `````openstack endpoint create --region RegionOne placement internal http://[Control-IP]:8778`````
- `````openstack endpoint create --region RegionOne placement admin http://[Control-IP]:8778`````
  
Add user placement to mysql
- `````sudo mysql`````
- `````create database placement;`````
- `````grant all privileges on placement.* to placement@'localhost' identified by '[PASSWORD]';`````
- `````grant all privileges on placement.* to placement@'%' identified by '[PASSWORD]';`````
- `````flush privileges;`````
- `````exit;`````
  
Install Dependencies
- `````sudo apt install placement-api -y`````

Backup Configuration File
- `````sudo mv /etc/placement/placement.conf /etc/placement/placement.conf.bak`````
  
Write Configuration
- add file /etc/placement/placement.conf
  - `````[DEFAULT]`````
  - `````debug = false`````
  - `````[api]`````
  - `````auth_strategy = keystone`````
  - `````[keystone_authtoken]`````
  - `````www_authenticate_uri = http://[Control-IP]:5000`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````memcached_servers = [Control-IP]:11211`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = placement`````
  - `````password = [PASSWORD]`````
  - `````[placement_database]`````
  - `````connection = mysql+pymysql://placement:[PASSWORD]@[Control-IP]/placement`````

Setup permission configuration files
- `````sudo chmod 640 /etc/glance/glance-api.conf`````
- `````sudo chown root:glance /etc/glance/glance-api.conf`````

Sync Placement Database
- `````sudo su -s /bin/bash -c "placement-manage db sync"`````
  
Restart Apache2 for placement api
- `````systemctl restart apache2`````
  
Check Openstack Service
- `````openstack service list````` >> IF SHOW PLACEMENT, DEPLOY SUCCESS

Check Apache2 Service
- `````systemctl status apache2````` >> IF STATUS RUNNING, PLACEMENT RUNNING WELL
