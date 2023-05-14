# How to use this playbook

- Create requirements.yaml
  ```
  collections:
    - name: https://github.com/CRASH-Tech/talos-playbook.git
      type: git
      version: master
  ```
- Install playbook
  ```
  ansible-galaxy collection install -r requirements.yaml
  ```
- Create ansible-vault secret file
  ```
  ansible-vault create .vault_pass.enc
  ```
- Decrypt vault secret file
  ```
  ansible-vault decrypt .vault_pass.enc --output .vault_pass
  ```
- Create .gitignore
  ```
  clusters/*/secrets/*
  .vault_pass
  ```
- Play role
  ```
  ansible-playbook crash_tech.kubernetes.talos -e "apply_configs=true install_charts=false upgrade_talos=false" -i test-cluster.yaml
  ```
- Enjoy!
  ```
  export KUBECONFIG=~/.kube/example_test-cluster
  ```
## Example inventory
```
all:
  vars:
    talos_version: v1.4.4
    kubernetes_version: 1.26.3
    cluster_endpoint: https://10.10.112.1:6443
    cluster_vip: 10.10.112.1
    kubeconfig_prefix: example_
    default_gateway: 10.10.112.254
    nameservers:
      - 8.8.8.8
      - 8.8.4.4
    charts: []
  children:
    controlplane:
      vars:
        patches:
          - install.yaml
          - network_vip.yaml
          - no-psp.yaml
          - cp-scheduling.yaml
          - admin-user.yaml
        local_patches: []
      hosts:
        test-cluster-m1:
          ip: 10.10.112.2/24
        test-cluster-m2:
          ip: 10.10.112.3/24
        test-cluster-m3:
          ip: 10.10.112.4/24
    worker:
      vars:
        patches:
          - install.yaml
          - network.yaml
          - canal-3.25.1.yaml
        local_patches: []
      hosts:
        test-cluster-w1:
          ip: 10.10.112.5/24
```

# Tips
* cluster_name == inventory file name(without ext.)
* "patches" are patches in the playbook repository
* "local_patches" are patches in your repository
