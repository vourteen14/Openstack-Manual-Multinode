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
  - ````keystone-manage bootstrap --bootstrap-password adminpassword --bootstrap-admin-url http://$controller:5000/v3/ --bootstrap-internal-url http://$controller:5000/v3/ --bootstrap-public-url http://$controller:5000/v3/ -bootstrap-region-id RegionOne````

