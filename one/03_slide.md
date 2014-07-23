!SLIDE bullets incremental

# Abstracting Data with Hiera #

* a place to put your data
* backend driven
* function call to lookup on keys
* hierarchy that is facter aware
* default and overrides

!SLIDE

## Public data in code

    @@@puppet
    class { 'jenkins::slave':
      jenkins_ssh_key => 'AAAAB3Nzbu84a....'
    }

!SLIDE commandline incremental

## Public data in code

    # cat /etc/puppet/hieradata/common.yaml 
    ---
    jenkins_key: AAAAB3NzaC1yc2EAAAADA...
    ...

    # hiera -d jenkins_key
    DEBUG: Hiera YAML backend starting
    DEBUG: Looking up jenkins_key in YAML backend
    DEBUG: Looking for data source common
    DEBUG: Found jenkins_key in common
    AAAAB3NzaC1yc2EAAAADAQAB...

!SLIDE code

# Public data in code

    @@@puppet
    $ssh_key = hiera('jenkins_key')
    class { 'jenkins::slave':
        jenkins_ssh_key => $ssh_key,
    }

!SLIDE

# Secrets in code

    @@@puppet
    class { 'mysql::server':
        root_password => 'hunter2',
    }


!SLIDE commandline incremental

# Secrets in code

    # cat /etc/puppet/hieradata/common.yaml
    ---
    ...
    mysql_root_password: hunter2
    ...


    # hiera -d mysql_root_password
    DEBUG: Hiera YAML backend starting
    DEBUG: Looking up mysql_root_password in YAML backend
    DEBUG: Looking for data source common
    DEBUG: Found mysql_root_password in common

    hunter2

!SLIDE code

# Secrets in code #

    @@@puppet
    $password = hiera('mysql_root_password')

    class { 'mysql::server':
      root_password => $password,
    }

!SLIDE

# Conditional data in code

    @@@puppet
    class { 'graphite':
        if $::osfamily == 'RedHat' {
            $pkgs = [
                'git',
                'python-django',
                'g++',
                'sqlite3',]
        ...
        }
    }


!SLIDE commandline incremental

# Hiera configuration

    # ln -s /etc/hiera.yaml /etc/puppet/hiera.yaml



!SLIDE commandline incremental


# Hiera configuration

    # cat /etc/puppet/hiera.yaml
    ---
    :backends:
      - yaml

    :yaml:
      :datadir: /etc/puppet/hieradata

    :hierarchy:
      - "%{clientcert}/common"
      - "osfamily/%{osfamily}/common"
      - common

!SLIDE

# Conditional data in code

    @@@puppet
    class { 'graphite':
        if $::osfamily == 'RedHat' {
            $pkgs = [
                'git',
                'python-django',
                'g++',
                'sqlite3',]
        ...
        }
    }

!SLIDE commandline incremental


# Hiera data

    # find .
    .
    ./common.yaml
    ./osfamily
    ./osfamily/RedHat
    ./osfamily/RedHat/common.yaml
    ./osfamily/Debian
    ./osfamily/Debian/common.yaml


!SLIDE commandline incremental


# Hiera data

    # cat osfamily/Debian/common.yaml
    ---
    graphite::pkgs:
      - graphite
      - python-django
      - virtualenv

!SLIDE commandline incremental

# Hiera data


    # cat osfamily/RedHat/common.yaml 
    ---
    graphite::pkgs:
      - git
      - python-django
      - g++
      - sqlite3
      - sqlite3-devel
      - python26-virtualenv

!SLIDE commandline incremental

# Hiera data

    # hiera graphite::pkgs  osfamily=RedHat
    ["git",
     "python-django",
     "g++",
     "sqlite3",
     "sqlite3-devel",
     "python26-virtualenv"]

!SLIDE commandline incremental

# Hiera data

    # hiera graphite::pkgs  osfamily=Debian
    ["graphite", "python-django", "virtualenv"]



!SLIDE

# Conditional data in code

    @@@puppet
    class graphite {
        if $::osfamily == 'RedHat' {
            $pkgs = [
                'git',
                'python-django',
                'g++',
                'sqlite3',]
        ...
        }
    }


!SLIDE

# Conditional data in code

    @@@puppet
    class graphite {
      $pkgs = hiera('graphite::pkgs')
      package { $pkgs:
        ensure => latest,
      }
    }


!SLIDE

# Conditional data in code

    @@@puppet
    class graphite (
      $pkgs,
    ){
      package { $pkgs:
        ensure => latest,
      }
    }


!SLIDE

# Backends #

* yaml, json
* file, ldap
* gpg, eyaml
* mysql, postgres, redis

!SLIDE

# Pros #

* Separation between data and code
* Composable classes via data bindings
* Backends, integration with existing datastores
* Some conditional logic irrelevant
* Puppet code sanitized

!SLIDE

# Cons #

* data bindings
* hard to figure out where things come from
* hiera-yaml can only support one data directory
* debugging
* public modules can't data bindings


!SLIDE

# In module data:


[puppet-module-data](https://github.com/ripienaar/puppet-module-data/)

