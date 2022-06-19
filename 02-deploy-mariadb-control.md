DEPLOYING MARIADB

Pre-Notes
- Openstack uses mariadb for backend service database, all service openstack saving data in mariadb.
- A runing operating system has been configure, like user can sudo withoud password, network, system upgrade etc.

Install Dependencies
- `````sudo apt update && sudo apt upgrade -y`````
- `````sudo apt install mariadb-server -y`````

Edit file /etc/mysql/mariadb.conf.d/50-server.cnf
- Adjust as below
  - `````character-set-server = utf8mb4`````
  - `````collation-server = utf8mb4_general_ci`````
  - `````bind-address = 0.0.0.0`````
  - `````max_connections = 500`````

Restart mariadb service
- `````sudo systemctl restart mariadb`````

Reconfigure MariaDB
- `````sudo mysql_secure_installation`````
  - Set root password? [Y/n] y
    New password: <input root database password>
    Re-enter new password: <input root database password>
  - Remove anonymous users? [Y/n] y
  - Disallow root login remotely? [Y/n] y
  - Remove test database and access to it? [Y/n] y
  - Reload privilege tables now? [Y/n] y
- restart mariadb service
  - `````sudo systemctl restart mariadb`````

Test Login
- `````sudo mysql -u root -p````` 
- `````Password: <input root database password>````` Ok, if success to mysql console
