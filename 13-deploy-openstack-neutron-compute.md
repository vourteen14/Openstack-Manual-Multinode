Install Dependencies
- `````sudo apt install neutron-linuxbridge-agent python3-neutron-fwaas -y`````

Edit file /etc/neutron/neutron.conf
- Adjust as below
  - `````[DEFAULT]`````
  - `````...`````
  - `````transport_url = rabbit://openstack:[PASSWORD]@[Control-IP]:5672`````
  - `````auth_strategy = keystone`````
  - `````[agent]`````
  - `````root_helper = sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf`````
  - `````[keystone_authtoken]`````
  - `````...`````
  - `````www_authenticate_uri = http://[Control-IP]:5000`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````memcached_servers = [Control-IP]:11211`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````project_name = service`````
  - `````username = neutron`````
  - `````password = [PASSWORD]`````
  - `````[oslo_concurrency]`````
  - `````...`````
  - `````lock_path = /var/lib/neutron/tmp`````

Edit file /etc/neutron/plugins/ml2/linuxbridge_agent.ini
- Adjust as below  
  - `````[linux_bridge]`````
  - `````physical_interface_mappings = provider:[Ext-Interface]`````
  - `````[vxlan]`````
  - `````enable_vxlan = true`````
  - `````local_ip = [Control-IP]`````
  - `````l2_population = true`````
  - `````[securitygroup]`````
  - `````enable_security_group = true`````
  - `````firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver`````

Edit file /etc/nova/nova.conf
- Adjust as below
  - `````[DEFAULT]`````
  - `````...`````
  - `````[api]`````
  - `````...`````
  - `````[glance]`````
  - `````...`````
  - `````[neutron]`````
  - `````auth_url = http://[Control-IP]:5000`````
  - `````auth_type = password`````
  - `````project_domain_name = default`````
  - `````user_domain_name = default`````
  - `````region_name = RegionOne`````
  - `````project_name = service`````
  - `````username = neutron`````
  - `````password = [PASSWORD]`````
  - `````service_metadata_proxy = True`````
  - `````metadata_proxy_shared_secret = metadata_secret`````
  
Restart Nova Service
- `````sudo systemctl restart nova-compute && sudo systemctl enable nova-compute`````
- `````sudo systemctl restart neutron-linuxbridge-agent && sudo systemctl enable neutron-linuxbridge-agent`````
