Edit file /etc/systemd/timesyncd.conf
- Adjust as below
  - `````NTP=id.pool.ntp.org`````

Restart NTP
- `````sudo systemctl restart systemd-timesyncd`````

Set NTP Zone to Asia/Jakarta
- `````sudo timedatectl set-timezone Asia/Jakarta`````

Status NTP
- `````sudo timedatectl timesync-status`````
