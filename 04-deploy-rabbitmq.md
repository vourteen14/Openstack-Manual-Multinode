Install Dependencies
- sudo apt install rabbitmq-server -y

Configure RabbitMQ
- Add openstack user to RabbitMQ
  - sudo rabbitmqctl add_user openstack <Password>
- Set permisson to openstack user
  - sudo rabbitmqctl set_permissions openstack ".*" ".*" ".*"
- Restart service
  - sudo systemctl restart rabbitmq-server
  
Check status RabbitMQ server
- sudo systemctl status rabbitmq-server
  
END OF CONFIGURATION
