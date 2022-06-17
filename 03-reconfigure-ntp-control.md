Configure File /etc/systemd/timesyncd.conf
- set NTP=.. to NTP=id.pool.ntp.org

Restart NTP
- sudo systemctl restart systemd-timesyncd

Status NTP
- sudo timedatectl timesync-status

END OF CONFIG
