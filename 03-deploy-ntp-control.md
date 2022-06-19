Edit file /etc/systemd/timesyncd.conf
- Adjust as below
  - `````NTP=id.pool.ntp.org`````

Restart NTP
- `````sudo systemctl restart systemd-timesyncd`````

Status NTP
- `````sudo timedatectl timesync-status`````
