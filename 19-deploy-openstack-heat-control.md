Activate Environtment File
- `````source admin-openrc.sh`````

Create Openstack Project
- `````openstack user create --domain default --project service --password [PASSWORD] heat`````
- `````openstack role add --project service --user heat admin`````
- `````openstack role create heat_stack_owner`````
- `````openstack role create heat_stack_user`````
- `````openstack role add --project admin --user admin heat_stack_owner`````
- `````openstack service create --name heat --description "Openstack Orchestration" orchestration`````
- `````openstack service create --name heat-cfn --description "Openstack Orchestration" cloudformation`````
- `````openstack endpoint create --region RegionOne orchestration public http://[CONTROL-IP]:8004/v1/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne orchestration internal http://[CONTROL-IP]:8004/v1/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne orchestration admin http://[CONTROL-IP]:8004/v1/%\(tenant_id\)s`````
- `````openstack endpoint create --region RegionOne cloudformation public http://[CONTROL-IP]:8000/v1`````
- `````openstack endpoint create --region RegionOne cloudformation internal http://[CONTROL-IP]:8000/v1`````
- `````openstack endpoint create --region RegionOne cloudformation admin http://[CONTROL-IP]:8000/v1`````
- `````openstack domain create --description "Stack projects and users" heat`````
- `````openstack user create --domain heat --password servicepassword heat_domain_admin`````
- `````openstack role add --domain heat --user heat_domain_admin admin`````

