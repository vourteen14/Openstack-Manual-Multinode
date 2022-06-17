Create Openstack Project
- openstack user create --domain default --project service --password <Password> glance
- openstack role add --project service --user glance admin
- openstack service create --name glance --description "OpenStack Image service" image

Create Openstack Endpoint
- openstack endpoint create --region RegionOne image public http://30.30.30.251:9292
- openstack endpoint create --region RegionOne image internal http://30.30.30.251:9292
- openstack endpoint create --region RegionOne image admin http://30.30.30.251:9292
  
Add user glance to mysql
- sudo mysql
- create database glance; 
- grant all privileges on glance.* to glance@'localhost' identified by '<Password>';
- grant all privileges on glance.* to glance@'%' identified by '<Password>';
- flush privileges;
- exit;

Install Dependencies
- sudo apt install glance -y

