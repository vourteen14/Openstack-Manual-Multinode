Install Dependencies
- sudo apt install memcached -y 

Edit file /etc/memcached.conf
- edit listener to 0.0.0.0 on line 35, where
- -l 0.0.0.0

Restart Service
- sudo systemctl restart memchaced
