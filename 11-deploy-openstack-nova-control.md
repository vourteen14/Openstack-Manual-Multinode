Activate Environtment File
- `````source admin-openrc.sh`````

Database Sync Nova
- `````sudo su -s /bin/bash nova -c "nova-manage cell_v2 discover_hosts"`````

View Nova Service
- `````openstack compute service list````` > IF SHOW NEW NOVA COMPUTE CONFIG SUCCESS
