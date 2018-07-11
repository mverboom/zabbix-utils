# NAME

zabbix_fping - retrieve latency and packetloss information for zabbix

# SYNOPSIS

`zabbix_fping [option]`

# DESCRIPTION

This command supports the use of the included template in zabbix.

# INSTALLATION

In order for the command to be useful to zabbix, a number of installation steps
need to be done.

## AGENT CONFIGURATION

### ZABBIX_FPING INSTALLATION

The command needs to be installed on one or more zabbix agents. The default location
referenced in all files in this repository is:

`/usr/local/bin/zabbix_fping`

In order for this command to work, the following software needs to be available:

* fping
* gawk
* sed

### ZABBIX_FPING CONFIGURATION

The command needs a configuration file. An example configuration file is included
in this repository. The configuration file needs to be installed in the following
location:

`/etc/zabbix/zabbix-fping.conf`

The configuration file is in an ini style format.

Each header in the configuration file is a description of the target that needs to
be monitored. For example:

`[My website]`

Below the header two key/value pairs are available:

* ipv4
* ipv6

At least one of these needs to be configured. The value should either be a DNS
name used for the specific IP version or an IP adres.

### RUNNING

It is best to run the command from cron. The user running the command needs enough
privileges to run fping and read the configuration file.

In order to create statistics the command needs to be run with the getdata option:

`/usr/local/bin/zabbix_fping getdata`

Each run will be 50 seconds long. The output data will be stored in:

`/tmp/zabbix-fping`

A useful crontab entry would be:

`* * * * * /usr/local/bin/zabbix_fping getdata`

### CONFIGURING ZABBIX AGENT

Now that the information is begin gathered, the zabbix agent needs to be made aware
of how information can be retrieved. A configuration file is available in the
repository and should in installed at:

`/etc/zabbix/zabbix_agentd.d/zabbix_agent-fping.conf`

After the configuration has been added, reload the agent

`systemctl restart zabbix-agent`

## SERVER CONFIGURATION

The final step should be taken server side. In order to monitor the data a new
template needs to be imported.

* Open the zabbix webinterface
* Go to Configuration -> Templates
* Choose Import
* Choose browse and select the `template-fping.xml` file in the file browser.
* Make any require changes to rules
* Select Import

The template is now available. In order to configure it for a host with a zabbix
agent that has been configuratie with the command follow the steps below.

* Open the zabbix webinterface
* Go to Configuration -> Hosts
* Select the name of the host to configure
* Go to templates
* Choose Template Fping at the Link new templates box
* Choose Add
* Choose Update

Zabbix should now start collecting data.

# FILES

`/usr/local/bin/zabbix_fping` Command
`/etc/zabbix/zabbix-fping.conf` Command configuration file
`/etc/zabbix/zabbix-agentd.d/zabbix_agent-fping.conf` Agent configuration file

# EXAMPLES

Check the output for discovery:

`/usr/local/bin/zabbix_fping discover`

# AUTHOR

Written by Mark Verboom

# REPORTING BUGS

Prefferably by opening an issue on the github page.

# COPYRIGHT

Copyright  ©  2014  Free Software Foundation, Inc.  License GPLv3+: GNU
GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free  to  change  and  redistribute  it.
There is NO WARRANTY, to the extent permitted by law.
