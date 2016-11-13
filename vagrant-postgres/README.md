
# PostgreSQL database set up on a Vagrant Provisioned Virtual Machine

Running the Vagrantfile along with accompanying configuration will set up ready to use PostgreSQL 9.5

## Requirements

- [Install Vagrant](https://www.vagrantup.com/), for more detailed explanation and OSX instructions you can [see this blogpost](http://www.kgolev.com/virtualization-made-easy-with-vagrant/).
- [Install Ansible](http://docs.ansible.com/ansible/intro_installation.html)
- psql client (optional)

## Run instructions

- Clone this project and navigate to it in your terminal
- run `vagrant up`
- wait for several minutes
- ssh to the machine with `vagrant ssh` and run `psql` to connect to db as user `vagrant`. Provide password `vagrant` when asked
- on your machine run `psql --user vagrant --host localhost` and provide password `vagrant` to connect

## Managing users and databases

### Using `vagrant` user

`vagrant` is a SUPERUSER, so you can use it to manage your users and database right away.

Of course you can remove SUPERUSER from the line creating users in `Vagrantfile` and manage this from the Virtual Machine.

### From the Virtual Machine

After machine is up, enter `vagrant ssh` and you will be logged in as user `vagrant`.

Change your user to `postgres` by executing `sudo su - postgres` and proceed to manage from there. Use postgres provided commands or enter `psql` to connect as `postgres` user which has SUPERUSER rights.

### Changing the provisioning to create your own user/database

Change these commands:

```
"DROP DATABASE IF EXISTS vagrant;"
"DROP USER IF EXISTS vagrant;"
"CREATE USER vagrant WITH PASSWORD 'vagrant' SUPERUSER;"
"CREATE DATABASE vagrant WITH ENCODING 'UTF-8' LC_COLLATE='en_US.UTF-8' LC_CTYPE='en_US.UTF-8' OWNER = vagrant;"
"GRANT ALL ON DATABASE vagrant TO vagrant;"
```

## Configuration

### Vagrant file

Everything is pretty self explanatory and commented.

- uses high quality Ubuntu image built by [the guys at Chef](https://github.com/chef/bento)
- sets up port forwarding
- sets locale, so we get UTF support
- sets proper configuration files - postgresql.conf and pg_hba.conf
- creates vagrant user and db

### postgresql.conf

Configured to listen to all addresses:

`listen_addresses = '"*"' # listening to all`

Failing to do so will make connecting from outside the VM not possible and the following error message will occur: `psql: server closed the connection unexpectedly`

### pg_hba.conf

Configured to allow connections from everywhere
`host    all             all             0.0.0.0/0            md5`

you can alternatively choose to allow connection from your subnet only.

Failing to configure this will result to messages similar to this:
```
FATAL:  no pg_hba.conf entry for host "10.0.2.2", user "postgres", database "postgres", SSL on
FATAL:  no pg_hba.conf entry for host "10.0.2.2", user "postgres", database "postgres", SSL off
```

## License

This content is licensed under the [MIT License](https://opensource.org/licenses/MIT).
