!SLIDE

# Architecting the code base #


!SLIDE

# Openstack puppet #

* openstack::server ->
* openstack::template ->
* openstack::base ->
* file['puppet.conf'],

!SLIDE

  * node inhertiance
  * multiple levels of class inheritance
  * cross module dependencies
  * global code in site.pp
  * long node definitions
  * repeating yourself

!SLIDE bullets incremental

# Roles and Profiles #


*  Node definitions should only include roles
*  Two ways to do this
*  role per machine type
*  Base class and service-mapped roles


!SLIDE

* nodes include roles
* roles include profiles
* profiles include resources and classes
* resources come from two places
* puppet core and modules


!SLIDE

![testimg.png](testimg.png)



!SLIDE

TODO: Block diagram of Garys interperation

!SLIDE

TODO: Block diagram of our interpretation

!SLIDE

# Profile #

    @@@puppet
    class profile::pg_server (){
        class { 'postgresql::globals': } ->
        class { 'postgresql::server': }
        cron { 'backup_postgres.sh':
            hour => '0', }
        logrotate::rule { 'postgresql-common': }
    }

!SLIDE

# Role per Machine Type #


    @@@puppet
    node 'paste.openstack.org' {
      class { 'openstack_project::paste':
        db_host     => hiera('paste_db_host', 'localhost'),
        db_password => hiera('paste_db_password', 'XXX'),
        sysadmins   => hiera('sysadmins', []),
      }
    }



!SLIDE code

# Base class and service-mapped roles #


    @@@puppet
    node
      'rita.cat.pdx.edu'
    {
      class { 'cecs':
        notifications => 'daytime',
        login         => 'all_students',
        root_level    => 'junior_rooters',
      }

      include cecs::role::linux_login_sys
      include cecs::role::killvnc
      include cecs::role::license::comsol
    }


!SLIDE 

# Questions on Architecture?
