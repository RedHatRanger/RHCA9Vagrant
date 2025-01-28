# Automation Controller
[automationcontroller]
controller.local

# Automation Hub
[automationhub]
automationhub.local

# External Database
[database]
db.local

[all:vars]
# General Configuration
admin_password='redhat'
pg_host='db.local'
pg_port=5432
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'

# Automation Hub Configuration
automationhub_admin_password='redhat'
automationhub_pg_host='db.local'
automationhub_pg_port=5432
automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='redhat'
automationhub_pg_sslmode='prefer'

# Registry Configuration
registry_url='registry.redhat.io'
registry_username='<your_registry_username>'
registry_password='<your_registry_password>'

# SSL Configuration (Optional)
# custom_ca_cert=/path/to/ca.crt
# web_server_ssl_cert=/path/to/tower.cert
# web_server_ssl_key=/path/to/tower.key
# automationhub_ssl_cert=/path/to/automationhub.cert
# automationhub_ssl_key=/path/to/automationhub.key
