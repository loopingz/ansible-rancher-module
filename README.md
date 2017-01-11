# ansible-rancher-module
Module to manage Rancher through Ansible

You can configure the stacks, the environments and the catalogs.

```yaml
- name: 'Rancher module'
  hosts: localhost
  connection: local
  gather_facts: no
  tasks:
    - name: 'Setup Rancher'
      rancher:
        url: 'https://rancher.url/'
        access_key: 'ACCESS_KEY'
        secret_key: 'SECRET_KEY'
        catalogs:
          - url: 'https://.../rancher-catalog.git'
            name: 'MyCatalog'
            branch: 'master'
        environments:
          - name: Production
            stacks:
              - name: Janitor
                catalog_entry: 'MyCatalog:Janitor'
                answers:
                  - name: 'FREQUENCY'
                    value: '9200'
    - name: 'Display Registration Token'
      debug:
        msg: "Rancher Production Token is {{ rancher.envs.Production.token }}"
```

This playbook will :

- Add or update a catalog named MyCatalog with repository (https://.../rancher-catalog.git) on master branch
- Add a Production environment if it doesn't exist
- Add or update a Janitor stack based on the Janitor item of the catalog MyCatalog, with variable FREQUENCY set to 9200
- Gather fact in rancher.envs with registration token

**Parameters**

| Name | Required | Value | Default
|--------|-----|-------|----|
| url | * | Rancher URL
| access_key | * | API Access Key
| secret_key | * | API Secret Key
| catalogs |  | List of catalogs to add
| environments | | List of environments to add
| clean_envs | | Keep only specified environments | False
| clean_catalogs | | Keep only specified catalogs | False
| clean_stacks | | Keep only specified environments | False

**catalog**

```
url: 'https://.../rancher-catalog.git'
name: 'MyCatalog'
branch: 'master'
```

*branch* is by default master

*name* is used for matching catalog

**environments**

```
name: Default
stacks:
  - ...
```
*name* is the main key

*stacks* list of stack

**stack**

```
name: Janitor
catalog_entry: 'Arken:Janitor'
answers:
	- name: 'FREQUENCY'
	  value: '9200'
```

*name* of the stack

*catalog_entry* or (*rancher_compose* and *docker_compose*) to specify the stack
*catalog_entry* use CatalogName:TemplateKey format, it will load the rancher_compose and docker_compose from the catalog. Or you can directly define your composes. You can find the templateKey when browsing to the item : https://rancherurl/env/1a5/catalog/Arken:infra\*datadog?catalogId=Arken, the key is infra\*datadog

*answers* the different values to use for rancher compose, list of (*name*, *value*)

