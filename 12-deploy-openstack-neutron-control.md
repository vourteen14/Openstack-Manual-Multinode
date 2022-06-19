Activate Environtment File
- source admin-openrc.sh

Create Openstack Project
- openstack user create --domain default --project service --password <Password> neutron
- openstack role add --project service --user neutron admin
- openstack service create --name neutron --description "OpenStack Networking service" network
  
Create Openstack Endpoint
- openstack endpoint create --region RegionOne network public http://30.30.30.251:9696
- openstack endpoint create --region RegionOne network internal http://30.30.30.251:9696
- openstack endpoint create --region RegionOne network admin http://30.30.30.251:9696
  
Add user neutron to mysql
- sudo mysql
- create database neutron_ml2;
- grant all privileges on neutron_ml2.* to neutron@'localhost' identified by '<Password>'; 
- grant all privileges on neutron_ml2.* to neutron@'%' identified by '<Password>';
- flush privileges;
- exit;
  
Install Dependencies
- apt -y install neutron-server neutron-plugin-ml2 neutron-linuxbridge-agent neutron-l3-agent neutron-dhcp-agent neutron-metadata-agent python3-neutronclient

Backup Configuration File
- mv /etc/neutron/neutron.conf /etc/neutron/neutron.conf.bak
  
Write Configuration
- add file /etc/neutron/neutron.conf
  - [DEFAULT]
  - core_plugin = ml2
  - service_plugins = router
  - auth_strategy = keystone
  - state_path = /var/lib/neutron
  - dhcp_agent_notification = True
  - allow_overlapping_ips = True
  - notify_nova_on_port_status_changes = True
  - notify_nova_on_port_data_changes = True
  - transport_url = rabbit://openstack:password@10.0.0.30
  - [agent]
  - root_helper = sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf
  - [keystone_authtoken]
  - www_authenticate_uri = http://10.0.0.30:5000
  - auth_url = http://10.0.0.30:5000
  - memcached_servers = 10.0.0.30:11211
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = neutron
  - password = servicepassword
  - [database]
  - connection = mysql+pymysql://neutron:password@10.0.0.30/neutron_ml2
  - [nova]
  - auth_url = http://10.0.0.30:5000
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - region_name = RegionOne
  - project_name = service
  - username = nova
  - password = servicepassword
  - [oslo_concurrency]
  - lock_path = $state_path/tmp
  
Setup permission configuration files
- sudo chmod 640 /etc/neutron/neutron.conf
- sudo chown root:neutron /etc/neutron/neutron.conf
  
Edit file /etc/neutron/l3_agent.ini
- Seuaikan seperti dibawah
  - interface_driver = linuxbridge

Edit file /etc/neutron/dhcp_agent.ini 
- Sesuaikan seperti dibawah
  - interface_driver = linuxbridge
  - dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
  - enable_isolated_metadata = true
  
Edit file /etc/neutron/metadata_agent.ini
- Sesuaikan seperti dibawah
  - nova_metadata_host = 30.30.30.251
  - metadata_proxy_shared_secret = metadata_secret
  - memcache_servers = 30.30.30.251:11211
  
Edit file /etc/neutron/plugins/ml2/ml2_conf.ini
- Sesuaikan seperti dibawah
  - [ml2]
  - type_drivers = flat,vlan,vxlan
  - tenant_network_types = vxlan
  - mechanism_drivers = linuxbridge,l2population
  - extension_drivers = port_security
  - [ml2_type_flat]
  - flat_networks = provider
  - [ml2_type_vxlan]
  - vni_ranges = 1:1000
  - [securitygroup]
  - enable_ipset = true
  
Edit file /etc/neutron/plugins/ml2/linuxbridge_agent.ini
- Sesuaikan Seperti dibawah
  - [linux_bridge]
  - physical_interface_mappings = provider:<Interface>
  - [vxlan]
  - enable_vxlan = true
  - local_ip = 30.30.30.251
  - l2_population = true
  - [securitygroup]
  - enable_security_group = true
  - firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
  
Edit file 
- Sesuaikan seperti dibawah
  - [DEFAULT]
  - ...
  - [api]
  - ...
  - [glance]
  - ...
  - [neutron]
  - auth_url = http://30.30.30.251:5000
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - region_name = RegionOne
  - project_name = service
  - username = neutron
  - password = <Password>
  - service_metadata_proxy = True
  - metadata_proxy_shared_secret = metadata_secret
  
Sync Neutron Database
- ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini
- su -s /bin/bash neutron -c "neutron-db-manage --config-file /etc/neutron/neutron.conf --config-file /etc/neutron/plugin.ini upgrade head"
  
Restart Neutron Service
- sudo systemctl restart neutron-server && systemctl enable neutron-server
- systemctl restart neutron-l3-agent && systemctl enable neutron-l3-agent
- systemctl restart neutron-dhcp-agent && systemctl enable neutron-dhcp-agent
- systemctl restart neutron-metadata-agent && systemctl enable neutron-metadata-agent
- systemctl restart neutron-linuxbridge-agent && systemctl enable neutron-linuxbridge-agent

Restart Nova Service
- sudo systemctl restart nova-api
  
Check Neutron Service Status
- openstack network agent list
