Install Dependencies
- `````sudo apt install rabbitmq-server -y`````

Add openstack user to RabbitMQ
- `````sudo rabbitmqctl add_user openstack [PASSWORD]`````

Set permisson to openstack user
- `````sudo rabbitmqctl set_permissions openstack ".*" ".*" ".*"`````

Restart RabbitMQ Service
- `````sudo systemctl restart rabbitmq-server`````
  
Check status RabbitMQ server
- `````sudo systemctl status rabbitmq-server`````
