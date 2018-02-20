# ansible-container
Migrate  Dockerfiles to Ansible


## Ansible container bd 
```
[root@osboxes ansible-db]# ansible-container import /home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/db/
Project successfully imported. You can find the results in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/db/ansible-db
A brief description of what you will find...


container.yml
-------------

The container.yml file is your orchestration file that expresses what services you have and how to build/run them.

settings:
  conductor_base: ubuntu
services:
  ? ''
  : roles:
    - ''


I added a single service named  for your imported Dockerfile.
As you can see, I made an Ansible role for your service, which you can find in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/db/ansible-db/roles/

ansible-db/roles/tasks/main.yml
-------------------------------

The tasks/main.yml file has your RUN/ADD/COPY instructions.

- name: Add the PostgreSQL PGP key to verify their Debian packages. It should be the
    same key as https://www.postgresql.org/media/keys/ACCC4CF8.asc
  shell: apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys
    B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8
- name: Add PostgreSQL's repository. It contains the most recent stable release of
    PostgreSQL, ``9.3``.
  shell: echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
- name: Install ``python-software-properties``, ``software-properties-common`` and
    PostgreSQL 9.3 There are some warnings (in red) that show up during the build.
    You can hide them by prefixing each apt-get statement with DEBIAN_FRONTEND=noninteractive
  shell: apt-get update && apt-get install -y python-software-properties software-properties-common
    postgresql-9.3 postgresql-client-9.3 postgresql-contrib-9.3 gedit
- name: Adjust PostgreSQL configuration so that remote connections to the database
    are possible.
  shell: echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.3/main/pg_hba.conf
- name: And add ``listen_addresses`` to ``/etc/postgresql/9.3/main/postgresql.conf``
  shell: echo "listen_addresses='*'" >> /etc/postgresql/9.3/main/postgresql.conf
- shell: mkdir $DIR_DB
- name: Ensure  exists
  file:
    path: ''
    state: directory
  remote_user: postgres
- copy:
    src: init-db.sql
    dest: "{{ lookup('env', 'DIR_DB') }}"
  remote_user: postgres
- shell: service postgresql start
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"
- shell: psql -a -f init-db.sql
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"
- shell: service postgresql stop
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"
- name: Ensure  exists
  file:
    path: ''
    state: directory
  remote_user: postgres
- copy:
    src: postgresql-template.conf
    dest: "{{ lookup('env', 'DIR_DB') }}"
  remote_user: postgres
- shell: cd /etc/postgresql/9.3/main
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"
- shell: chmod 700 postgresql.conf
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"
- shell: echo "envsubst < $DIR_DB/postgresql-template.conf > ./postgresql.conf"
  remote_user: postgres
  args:
    chdir: "{{ lookup('env', 'DIR_DB') }}"


I tried to preserve comments as task names, but you probably want to make
sure each task has a human readable name.

ansible-db/roles/meta/container.yml
-----------------------------------

Metadata from your Dockerfile went into meta/container.yml in your role.
These will be used as build/run defaults for your role.

from: ubuntu
maintainer: Fernando Mendez Requena <fernando.mendez@atos.net>
environment:
  DIR_DB: /db
working_dir: "{{ lookup('env', 'DIR_DB') }}"
user: postgres
# WORKDIR /docker-entrypoint-initdb.d/
# ADD init-db.sql /docker-entrypoint-initdb.d
ports:
- '5432'
# Set the default command to run when starting the container
command:
- /usr/lib/postgresql/9.3/bin/postgres
- -D
- /var/lib/postgresql/9.3/main
- -c
- config_file=/etc/postgresql/9.3/main/postgresql.conf


I also stored ARG directives in the role's defaults/main.yml which will used as
variables by Ansible in your build and run operations.

Good luck!
Project imported.	


```
## Ansible container webApp

```
[root@osboxes ansible-web]# ansible-container import /home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/webapp//ShowcaseServer/
Project successfully imported. You can find the results in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/webapp/ShowcaseServer/ansible-web
A brief description of what you will find...


container.yml
-------------

The container.yml file is your orchestration file that expresses what services you have and how to build/run them.

settings:
  conductor_base: ubuntu:xenial
services:
  ? ''
  : roles:
    - ''


I added a single service named  for your imported Dockerfile.
As you can see, I made an Ansible role for your service, which you can find in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/webapp/ShowcaseServer/ansible-web/roles/

ansible-web/roles/tasks/main.yml
--------------------------------

The tasks/main.yml file has your RUN/ADD/COPY instructions.

- shell: apt-get update
- shell: apt-get -qq install -y
- shell: sudo libpq-dev postgresql postgresql-contrib -y -qq
- shell: sudo postgresql-client-common
- shell: dialog apt-utils
- shell: nodejs npm nodejs-legacy -y -qq
- shell: python-pip
- shell: python-dev
- shell: python-virtualenv
- shell: apache2
- shell: pip install --upgrade pip
- shell: mkdir /webapp && mkdir $WEBAPP_DIR
- name: Ensure  exists
  file:
    path: ''
    state: directory
- copy:
    src: basic-dep.sh
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: cd $WEBAPP_DIR
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: chmod 700 basic-dep.sh
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: ./basic-dep.sh
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- name: Ensure  exists
  file:
    path: ''
    state: directory
- copy:
    src: requirements.txt
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: pip install -r requirements.txt
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- name: Ensure  exists
  file:
    path: ''
    state: directory
- copy:
    src: manage.py
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}"
- name: Ensure {{ lookup('env', 'WEBAPP_DIR') }} exists
  file:
    path: "{{ lookup('env', 'WEBAPP_DIR') }}"
    state: directory
- name: ADD ShowcaseServer $WEBAPP_DIR/ShowcaseServer (showcase_server)
  synchronize:
    src: showcase_server
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}/showcase_server"
    recursive: yes
- name: Ensure {{ lookup('env', 'WEBAPP_DIR') }} exists
  file:
    path: "{{ lookup('env', 'WEBAPP_DIR') }}"
    state: directory
- synchronize:
    src: backend
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}/backend"
    recursive: yes
- name: Ensure {{ lookup('env', 'WEBAPP_DIR') }} exists
  file:
    path: "{{ lookup('env', 'WEBAPP_DIR') }}"
    state: directory
- synchronize:
    src: dashboard
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}/dashboard"
    recursive: yes
- name: Ensure  exists
  file:
    path: ''
    state: directory
- copy:
    src: dev-setup.sh
    dest: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: cd $WEBAPP_DIR
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: chmod 700 dev-setup.sh
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"
- shell: ./dev-setup.sh
  args:
    chdir: "{{ lookup('env', 'WEBAPP_DIR') }}"


I tried to preserve comments as task names, but you probably want to make
sure each task has a human readable name.

ansible-web/roles/meta/container.yml
------------------------------------

Metadata from your Dockerfile went into meta/container.yml in your role.
These will be used as build/run defaults for your role.

from: ubuntu:xenial
maintainer: Fernando Mendez Requena <fernando.mendez.external@atos.net>
environment:
# Show python errors
  PYTHONUNBUFFERED: '1'
  WEBAPP_DIR: /webapp/ShowcaseServer/
working_dir: "{{ lookup('env', 'WEBAPP_DIR') }}"


I also stored ARG directives in the role's defaults/main.yml which will used as
variables by Ansible in your build and run operations.

Good luck!
Project imported.	



```
## Ansible container apache server

```
[root@osboxes ansible-apache]# ansible-container import /home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/apache/
Project successfully imported. You can find the results in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/apache/ansible-apache
A brief description of what you will find...


container.yml
-------------

The container.yml file is your orchestration file that expresses what services you have and how to build/run them.

settings:
  conductor_base: ubuntu
services:
  ? ''
  : roles:
    - ''


I added a single service named  for your imported Dockerfile.
As you can see, I made an Ansible role for your service, which you can find in:
/home/osboxes/Desktop/STAMP/atos-uc-city2go/city2go/apache/ansible-apache/roles/

ansible-apache/roles/tasks/main.yml
-----------------------------------

The tasks/main.yml file has your RUN/ADD/COPY instructions.

- name: Update the repository sources list
  shell: apt-get update
- name: Install and run apache
  shell: apt-get install -y apache2 && apt-get clean
- shell: mkdir $DIR_APACHE
- name: ADD mpm_prefork.conf $DIR_APACHE
  shell: cd /etc/apache2/mods-available
  args:
    chdir: "{{ lookup('env', 'DIR_APACHE') }}"
- shell: chmod 700 mpm_prefork.conf
  args:
    chdir: "{{ lookup('env', 'DIR_APACHE') }}"
- shell: chmod 700 mpm_worker.conf
  args:
    chdir: "{{ lookup('env', 'DIR_APACHE') }}"
- shell: echo "envsubst < $DIR_APACHE/mpm_prefork-template.conf > ./mpm_prefork.conf"
  args:
    chdir: "{{ lookup('env', 'DIR_APACHE') }}"
- shell: echo "envsubst < $DIR_APACHE/mpm_worker-template.conf > ./mpm_worker.conf"
  args:
    chdir: "{{ lookup('env', 'DIR_APACHE') }}"


I tried to preserve comments as task names, but you probably want to make
sure each task has a human readable name.

ansible-apache/roles/meta/container.yml
---------------------------------------

Metadata from your Dockerfile went into meta/container.yml in your role.
These will be used as build/run defaults for your role.

from: ubuntu
maintainer: Fernando Mendez Requena - fernando.mendez@atos.net
environment:
  DIR_APACHE: /apache
working_dir: "{{ lookup('env', 'DIR_APACHE') }}"
ports:
- '80'
command: apachectl -D FOREGROUND


I also stored ARG directives in the role's defaults/main.yml which will used as
variables by Ansible in your build and run operations.

Good luck!
Project imported.	


```


