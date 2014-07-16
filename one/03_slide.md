!SLIDE

# Abstracting Data with Hiera #

## Without Hiera ##

### Data in code ###


    @@@puppet
    class { 'jenkins::slave':
        jenkins_ssh_key => 'AAAAB3NzaC1yc2EAAAAD...'
    }



!SLIDE

# Abstracting Data with Hiera #

## Without Hiera ##

### Secrets in code ###

    @@@puppet
    class { 'mysql::server':
        root_password => 'hunter2',
    }


!SLIDE

# Abstracting Data with Hiera #

## Without Hiera ##

### Secrets in code ###


    @@@puppet
    class { 'graphite':
        if $::osfamily == 'RedHat' {
            $pkgs = [
                'git',
                'python-django',
                'g++',
                'sqlite3',]
        }
    }



!SLIDE bullets incremental

# Abstracting Data with Hiera #

## High Level ##


* place to put your data
* backend driven
* function call to lookup on keys
* hierarchy that is facter aware
* default and overrides


!SLIDE

# Abstracting Data with Hiera #

## Demonstration ##






!SLIDE

# Abstracting Data with Hiera #

## Demonstration ##


    @@@puppet
    class { 'mysql::server':
        root_password => hiera('mysql_root_password'),
    }

.notes backend to encrypt
.notes data bindings


