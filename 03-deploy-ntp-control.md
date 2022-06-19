Edit file /etc/systemd/timesyncd.conf
- Adjust as below
  - `````set NTP=..````` to `````NTP=id.pool.ntp.org`````

Restart NTP
- `````sudo systemctl restart systemd-timesyncd`````

Status NTP
- `````sudo timedatectl timesync-status`````
