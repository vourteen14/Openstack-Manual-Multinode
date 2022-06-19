Activate Environtment File
- source admin-openrc.sh

Create Openstack Project
- openstack user create --domain default --project service --password [Password] swift
- openstack role add --project service --user swift admin
- openstack service create --name swift --description "OpenStack Object Storage" object-store

Create Openstack Endpoint
- openstack endpoint create --region RegionOne object-store internal http://30.30.30.251:8080/v1/AUTH_%\(tenant_id\)s
- openstack endpoint create --region RegionOne object-store admin http://30.30.30.251:8080/v1
- openstack endpoint create --region RegionOne object-store public http://30.30.30.251:8080/v1/AUTH_%\(tenant_id\)s

Install Dependencies
- sudo apt install swift swift-proxy python3-swiftclient python3-keystonemiddleware python3-memcache -y

Write Configuration
- add file /etc/swift/proxy-server.conf
  - [DEFAULT]
  - bind_ip = 0.0.0.0
  - bind_port = 8080
  - user = swift
  - [pipeline:main]
  - pipeline = catch_errors gatekeeper healthcheck proxy-logging cache container_sync bulk ratelimit authtoken keystoneauth container-quotas account-quotas slo dlo versioned_writes proxy-logging proxy-server
  - [app:proxy-server]
  - use = egg:swift#proxy
  - allow_account_management = true
  - account_autocreate = true
  - [filter:authtoken]
  - paste.filter_factory = keystonemiddleware.auth_token:filter_factory
  - www_authenticate_uri = http://10.0.0.30:5000
  - auth_url = http://10.0.0.30:5000
  - memcached_servers = 10.0.0.30:11211
  - auth_type = password
  - project_domain_name = default
  - user_domain_name = default
  - project_name = service
  - username = swift
  - password = servicepassword
  - delay_auth_decision = true
  - [filter:keystoneauth]
  - use = egg:swift#keystoneauth
  - operator_roles = admin,SwiftOperator
  - [filter:healthcheck]
  - use = egg:swift#healthcheck
  - [filter:cache]
  - use = egg:swift#memcache
  - memcache_servers = 10.0.0.30:11211
  - [filter:ratelimit]
  - use = egg:swift#ratelimit
  - [filter:domain_remap]
  - use = egg:swift#domain_remap
  - [filter:catch_errors]
  - use = egg:swift#catch_errors
  - [filter:cname_lookup]
  - use = egg:swift#cname_lookup
  - [filter:staticweb]
  - use = egg:swift#staticweb
  - [filter:tempurl]
  - use = egg:swift#tempurl
  - [filter:formpost]
  - use = egg:swift#formpost
  - [filter:name_check]
  - use = egg:swift#name_check
  - [filter:list-endpoints]
  - use = egg:swift#list_endpoints
  - [filter:proxy-logging]
  - use = egg:swift#proxy_logging
  - [filter:bulk]
  - use = egg:swift#bulk
  - [filter:slo]
  - use = egg:swift#slo
  - [filter:dlo]
  - use = egg:swift#dlo
  - [filter:container-quotas]
  - use = egg:swift#container_quotas
  - [filter:account-quotas]
  - use = egg:swift#account_quotas
  - [filter:gatekeeper]
  - use = egg:swift#gatekeeper
  - [filter:container_sync]
  - use = egg:swift#container_sync
  - [filter:xprofile]
  - use = egg:swift#xprofile
  - [filter:versioned_writes]
  - use = egg:swift#versioned_writes 

Write Configuration
- add file /etc/swift/swift.conf
  - [swift-hash]
  - swift_hash_path_suffix = swift_shared_path
  - swift_hash_path_prefix = swift_shared_path

Configure Swift Ring Files
- swift-ring-builder /etc/swift/account.builder create 12 3 1
- swift-ring-builder /etc/swift/container.builder create 12 3 1
- swift-ring-builder /etc/swift/object.builder create 12 3 1

Configure Swift Account Builder
- swift-ring-builder /etc/swift/account.builder add r0z0-30.30.30.253:6002/sdc 100
- swift-ring-builder /etc/swift/account.builder add r0z0-30.30.30.253:6002/sdd 100
- swift-ring-builder /etc/swift/account.builder add r0z0-30.30.30.253:6002/sde 100
- swift-ring-builder /etc/swift/account.builder 
- swift-ring-builder /etc/swift/account.builder rebalance

Configure Swift Container Builder
- swift-ring-builder /etc/swift/container.builder create 12 3 1
- swift-ring-builder /etc/swift/container.builder add r0z0-30.30.30.253:6001/sdc 100 
- swift-ring-builder /etc/swift/container.builder add r0z0-30.30.30.253:6001/sdd 100 
- swift-ring-builder /etc/swift/container.builder add r0z0-30.30.30.253:6001/sde 100
- swift-ring-builder /etc/swift/container.builder
- swift-ring-builder /etc/swift/container.builder rebalance

Configure Swift Object Builder
- swift-ring-builder /etc/swift/object.builder create 12 3 1
- swift-ring-builder /etc/swift/object.builder add r0z0-30.30.30.253:6000/sdc 100
- swift-ring-builder /etc/swift/object.builder add r0z0-30.30.30.253:6000/sdd 100
- swift-ring-builder /etc/swift/object.builder add r0z0-30.30.30.253:6000/sde 100
- swift-ring-builder /etc/swift/object.builder
- swift-ring-builder /etc/swift/object.builder rebalance

Setup permission configuration files
- chown swift. /etc/swift/*.gz 

Restart Swift-Proxy Service
- systemctl restart swift-proxy 

Copy Swift Ring Files to Storage Node
- scp /etc/swift/*.gz anggasuriana@30.30.30.253:/home/anggasuriana
