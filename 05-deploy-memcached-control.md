Install Dependencies
- `````sudo apt install memcached -y````` 

Edit file /etc/memcached.conf
- Adjust as below
  - `````-l 0.0.0.0`````

Restart Service
- `````sudo systemctl restart memchaced`````
