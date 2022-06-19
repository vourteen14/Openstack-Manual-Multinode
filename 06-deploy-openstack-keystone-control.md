Configuring Database
- `````sudo mysql`````
- `````create database keystone;````` 
- `````grant all privileges on keystone.* to keystone@'localhost' identified by '[PASSWORD]';````` 
- `````grant all privileges on keystone.* to keystone@'%' identified by '[PASSWORD]';`````
- `````flush privileges;````` 
- `````exit;`````
  
Install Dependencies
- `````sudo apt install keystone python3-openstackclient apache2 libapache2-mod-wsgi-py3 python3-oauth2client -y`````
  
Edit file /etc/keystone/keystone.conf
- Adjust as below
  - `````memcache_servers = control:11211`````
  - `````connection = mysql+pymysql://keystone:[PASSWORD]@control/keystone`````
  - `````provider = fernet`````

Initialize Keystone key
- ````sudo keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone````
- ````sudo keystone-manage credential_setup --keystone-user keystone --keystone-group keystone````

Bootstrap Keystone
- ````sudo keystone-manage bootstrap --bootstrap-password [PASSWORD] --bootstrap-admin-url http://[Control-IP]:5000/v3/ --bootstrap-internal-url http://[Control-IP]:5000/v3/ --bootstrap-public-url http://[Control-IP]:5000/v3/ --bootstrap-region-id RegionOne````

Write Environtment file
- add file admin-openrc.sh
  - `````export OS_PROJECT_DOMAIN_NAME=default`````
  - `````export OS_USER_DOMAIN_NAME=default`````
  - `````export OS_PROJECT_NAME=admin`````
  - `````export OS_USERNAME=admin`````
  - `````export OS_PASSWORD=<Password>`````
  - `````export OS_AUTH_URL=http://30.30.30.251:5000/v3`````
  - `````export OS_IDENTITY_API_VERSION=3`````
  - `````export OS_IMAGE_API_VERSION=2`````
- setup permission to file
  - `````chmod 600 admin-openrc.sh`````
- activate the file
  - `````source admin-openrc.sh`````

Add openstack project
- `````openstack project create --domain default --description "Service Project" service`````
- `````openstack project list`````
