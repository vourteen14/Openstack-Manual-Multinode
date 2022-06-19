Install Dependencies
- `````sudo apt install openstack-dashboard -y`````

Edit file /etc/openstack-dashboard/local_settings.py 
- Adjust as below
  - `````CACHES = {`````
  - `````   'default': {`````
  - `````     'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',`````
  - `````     'LOCATION': '[Control-IP]:11211',`````
  - `````  },`````
  - `````}`````
  - `````SESSION_ENGINE = "django.contrib.sessions.backends.cache"`````
  - `````OPENSTACK_HOST = "[Control-IP]"`````
  - `````OPENSTACK_KEYSTONE_URL = "http://[Control-IP]:5000/v3"`````
  - `````TIME_ZONE = "Asia/Jakarta"`````
  - `````OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True`````
  - `````OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = 'Default'`````
  - `````OPENSTACK_KEYSTONE_DEFAULT_ROLE = 'admin'`````

Reload & restart horizon service
- `````sudo systemctl reload apache2 && sudo systemctl restart apache2`````
