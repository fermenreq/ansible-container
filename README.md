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
