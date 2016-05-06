docker-elasticsearch
============

[![Build Status](https://travis-ci.org/wangsha/docker-elasticsearch.svg?branch=master)](https://travis-ci.org/wangsha/docker-elasticsearch)
[![Ansible Galaxy](https://img.shields.io/badge/AnsibleGalaxy-wangsha.docker--elasticsearch-blue.svg)](https://galaxy.ansible.com/wangsha/docker-elasticsearch/)

Ansible role to manage and run the elasticsearch docker container.

Requirements
------------

This role has only been tested on Ubuntu 14.04. Since this uses Ansible's
docker module, you will need to ensure that a recent-ish version of `docker-py`
and `docker` are installed.

Examples
--------

Install this module from Ansible Galaxy into the './roles' directory:
```bash
ansible-galaxy install wangsha.docker-elasticsearch -p ./roles
```

Use it in a playbook as follows, assuming you already have docker setup:
```yaml
- hosts: 'servers'
  roles:
    - role: angstwad.docker_ubuntu
      become: true
    - role: wangsha.docker-elasticsearch
      become: true
```

Have a look at the [defaults/main.yml](defaults/main.yml) for role variables
that can be overridden.

If you need a playbook to set Docker itself, have a look at [angstwad.docker_ubuntu](https://github.com/angstwad/docker.ubuntu) Galaxy
role.

Performance Tuning
------------------
To enable `mlockall` on docker container, follow [this](http://stackoverflow.com/a/26365300) post to configure docker deamon.
Top section of the file `/etc/init/docker.conf` looks like:
```bash
description "Docker daemon"

start on (filesystem and net-device-up IFACE!=lo)
stop on runlevel [!2345]
limit nofile 524288 1048576
limit nproc 524288 1048576
limit memlock unlimited unlimited
respawn

...
```

Custom volume mappings
----------------------
Docker allows mounting a host directory or a host file as [data volume](https://docs.docker.com/engine/userguide/containers/dockervolumes/).
This role mounts host directories to persist container data and host files to configure container behavior.
`docker_elasticsearch_directory_volumes` and `docker_elasticsearch_file_volumes` are the two variables to control volume mappings.
If you wish to customize the mapping, please follow `<host directory>:<container directory>:<mapping mode>` format
 to ensure host directories are correctly created before launching containers.
 
To customize host file mappings, update `docker_elasticsearch_file_volumes`. 
This role will automatically create file parent directories and copy the template 
to host machine. The naming convention for template is `<host_file_name>.<host_file_extension>.j2`.
To copy template from your own ansible diretories, set `docker_elasticsearch_template_path`.

Example Config:
```yaml
docker_elasticsearch_file_volumes:
  - '/opt/myapp/conf/settings.conf:/etc/myapp/conf/settings.conf:ro'
docker_elasticsearch_template_path: /path/to/ansible/project/templates/
# make sure file /path/to/ansible/project/templates//settings.conf.j2 exists. 
```



Additional References
---------------------
- [default docker image](https://hub.docker.com/_/elasticsearch/)


License
-------

[MIT](LICENSE.txt)

Author Information
------------------

- wangsha
