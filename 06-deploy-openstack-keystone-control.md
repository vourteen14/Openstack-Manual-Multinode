Configuring Database
- `````sudo mysql`````
- `````create database keystone;````` 
- `````grant all privileges on keystone.* to keystone@'localhost' identified by '<Password>';````` 
- `````grant all privileges on keystone.* to keystone@'%' identified by '<Password>';`````
- `````flush privileges;````` 
- `````exit;`````
  
Install Dependencies
- `````sudo apt install keystone python3-openstackclient apache2 libapache2-mod-wsgi-py3 python3-oauth2client -y`````
  
Configure Keystone
- Edit file /etc/keystone/keystone.conf
  - `````memcache_servers = 30.30.30.251:11211 <line 442>`````
  - `````connection = mysql+pymysql://keystone:<Password>@30.30.30.251/keystone <line 599>`````
  - `````provider = fernet <line 2516>`````
- Initialize Keystone key
  - ````keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone````
  - ````keystone-manage credential_setup --keystone-user keystone --keystone-group keystone````
- Bootstrap Keystone
  - ````keystone-manage bootstrap --bootstrap-password <Password> --bootstrap-admin-url http://30.30.30.251:5000/v3/ --bootstrap-internal-url http://30.30.30.251r:5000/v3/ --bootstrap-public-url http://30.30.30.251:5000/v3/ -bootstrap-region-id RegionOne````

Write Environtment file
- add file admin-openrc.sh
  - export OS_PROJECT_DOMAIN_NAME=default
  - export OS_USER_DOMAIN_NAME=default
  - export OS_PROJECT_NAME=admin
  - export OS_USERNAME=admin
  - export OS_PASSWORD=<Password>
  - export OS_AUTH_URL=http://30.30.30.251r:5000/v3
  - export OS_IDENTITY_API_VERSION=3
  - export OS_IMAGE_API_VERSION=2
- setup permission to file
  - chmod 600 admin-openrc.sh
- activate the file
  - source admin-openrc.sh

Add openstack project
- openstack project create --domain default --description "Service Project" service
- openstack project list

SUCCESS if show project name service
