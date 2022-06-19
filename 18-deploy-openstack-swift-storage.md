Install Dependencies
- `````sudo apt install swift swift-account swift-container swift-object xfsprogs -y`````

Copy Ring Files
- `````sudo cp /home/anggasuriana/*.gz /etc/swift/`````
 
Setup permission configuration files
- `````sudo chown -R swift /srv/node`````
- `````sudo chown -R /etc/swift/*.gz`````

Write Configuration
- add file /etc/swift/swift.conf
  - `````[swift-hash]`````
  - `````swift_hash_path_suffix = swift_shared`````
  - `````swift_hash_path_prefix = swift_shared`````
Write Configuration
- add file /etc/rsyncd.conf
  - `````pid file = /var/run/rsyncd.pid`````
  - `````log file = /var/log/rsyncd.log`````
  - `````uid = swift`````
  - `````gid = swift`````
  - `````address = storage`````
  - `````[account]`````
  - `````path            = /srv/node/sdc, /srv/node/sdd, /srv/node/sde`````
  - `````read only       = false`````
  - `````write only      = no`````
  - `````list            = yes`````
  - `````incoming chmod  = 0644`````
  - `````outgoing chmod  = 0644`````
  - `````max connections = 25`````
  - `````lock file =     /var/lock/account.lock`````
  - `````[container]`````
  - `````path            = /srv/node/sdc, /srv/node/sdd, /srv/node/sde`````
  - `````read only       = false`````
  - `````write only      = no`````
  - `````list            = yes`````
  - `````incoming chmod  = 0644`````
  - `````outgoing chmod  = 0644`````
  - `````max connections = 25`````
  - `````lock file =     /var/lock/container.lock`````
  - `````[object]`````
  - `````path            = /srv/node/sdc, /srv/node/sdd, /srv/node/sde`````
  - `````read only       = false`````
  - `````write only      = no`````
  - `````list            = yes`````
  - `````incoming chmod  = 0644`````
  - `````outgoing chmod  = 0644`````
  - `````max connections = 25`````
  - `````lock file =     /var/lock/object.lock`````
  - `````[swift_server]`````
  - `````path            = /etc/swift`````
  - `````read only       = true`````
  - `````write only      = no`````
  - `````list            = yes`````
  - `````incoming chmod  = 0644`````
  - `````outgoing chmod  = 0644`````
  - `````max connections = 5`````
  - `````lock file =     /var/lock/swift_server.lock`````

Edit file /etc/default/rsync
- Adjust as below
  - ...
  - `````RSYNC_ENABLE=true`````

Restart Swift Service
- `````sudo systemctl restart rsync swift-account-auditor swift-account-replicator swift-account swift-container-auditor swift-container-replicator swift-container-updater swift-container swift-object-auditor swift-object-replicator swift-object-updater swift-object`````
- `````sudo systemctl enable rsync swift-account-auditor swift-account-replicator swift-account swift-container-auditor swift-container-replicator swift-container-updater swift-container swift-object-auditor swift-object-replicator swift-object-updater swift-object`````
