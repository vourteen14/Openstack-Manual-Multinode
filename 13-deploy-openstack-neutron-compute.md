Install Dependencies
- sudo apt install neutron-linuxbridge-agent python3-neutron-fwaas

Edit file /etc/neutron/neutron.conf
- Sesuaikan seperti dibawah
  - [DEFAULT]
  - ...
  - transport_url = rabbit://openstack:password@controller
  - auth_strategy = keystone
  - [agent]
  - root_helper = sudo /usr/bin/neutron-rootwrap /etc/neutron/rootwrap.conf
  - [keystone_authtoken]
  - ...
  - www_authenticate_uri = http://controller:5000
  - auth_url = http://controller:5000
  - memcached_servers = controller:11211
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = neutron
  - password = <Password>
  - [oslo_concurrency]
  - ...
  - lock_path = /var/lib/neutron/tmp

Edit file /etc/neutron/plugins/ml2/linuxbridge_agent.ini
- Sesuaikan seperti dibawah  
  - [linux_bridge]
  - physical_interface_mappings = provider:[Ext-Interface]
  - [vxlan]
  - enable_vxlan = true
  - local_ip = 30.30.30.252
  - l2_population = true
  - [securitygroup]
  - enable_security_group = true
  - firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver

Edit file /etc/nova/nova.conf
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
  
Restart Nova Service
- sudo systemctl restart nova-compute && sudo systemctl enable nova-compute
- sudo systemctl restart neutron-linuxbridge-agent && sudo systemctl enable neutron-linuxbridge-agent
