nagios-check_lxc_resources
==========================

Nagios check LXC resources

You can check your LXC containers memory and swap usages from the host machine with that check.

Usage
-----
```
Usage : check_lxc_resources -h <container> -n <namespace> -w <warning%> -c <critical%> [-d]
    -h : set container name
    -n : set container namespace to check (memory|swap)
    -w : set warning percent alert
    -c : set critical percent alert
    -d : debug mode
    -help : print this message
```

Configuration
-------------

To make it work, you have to define this command:
```
define command {
        command_name    check_lxc_resources
        command_line    /usr/lib/nagios/plugins/check_nrpe -H $_HOSTPARENT$ -c check_lxc_resources -a $HOSTALIAS$ $ARG2$ $ARG3$ $ARG4$
}
```
* $_HOSTPARENT$: this can be a defined macro to indicate the parent host (which is the host machine)

You can use hostgroups templating:

```
define hostgroup {
    hostgroup_name  lxc 
    alias           lxc containers
}

define service{
    use                             generic-service
    hostgroup_name                  lxc
    service_description             Memory usage
    check_command                   check_lxc_resources!''!'memory'!90!95
}
 
define service{
    use                             generic-service
    hostgroup_name                  lxc
    service_description             SWAP usage
    check_command                   check_lxc_resources!''!'swap'!2!5
}
```
And then you can declare to your hosts:

define host{
    use                     generic-host,host-pnp
    host_name               hostname.domain.com
    alias                   hostname
    address                 hostname.domain.com
    hostgroups              lxc
    _PARENT                 parenthost.domain.com
}
```
