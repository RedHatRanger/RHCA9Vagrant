### Save THIS as `app.yaml` and run as `oc apply -f app.yaml` to stand up AAP on an OpenShift Cluster:

```yaml
---
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: ansible-automation-platform-operator
  namespace: ansible-automation-platform
spec:
  targetNamespaces:
    - ansible-automation-platform
---
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: ansible-automation-platform
  namespace: ansible-automation-platform
spec:
  channel: 'stable-2.5'
  installPlanApproval: Automatic
  name: ansible-automation-platform-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
---
apiVersion: automationcontroller.ansible.com/v1beta1
kind: AutomationController
metadata:
  name: controller2.5
  namespace: ansible-automation-platform
spec:
  postgres_keepalives_count: 5
  postgres_keepalives_idle: 5
  metrics_utility_cronjob_report_schedule: '@monthly'
  create_preload_data: true
  route_tls_termination_mechanism: Edge
  garbage_collect_secrets: false
  ingress_type: Route
  loadbalancer_port: 80
  no_log: true
  image_pull_policy: IfNotPresent
  projects_storage_size: 8Gi
  auto_upgrade: true
  task_privileged: false
  postgres_keepalives: true
  metrics_utility_enabled: false
  postgres_keepalives_interval: 5
  ipv6_disabled: false
  projects_storage_access_mode: ReadWriteMany
  postgres_data_volume_init: true
  env:
    - name: PGDATA
      value: /mnt/pgdata
  postgres_init_container_commands: |
    chown 26:0 /var/lib/pgsql/data
    chmod 700 /var/lib/pgsql/data
  metrics_utility_pvc_claim_size: 5Gi
  set_self_labels: true
  projects_persistence: false
  replicas: 1
  admin_user: admin
  loadbalancer_protocol: http
  metrics_utility_cronjob_gather_schedule: '@hourly'
```
