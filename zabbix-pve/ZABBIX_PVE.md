# NAME

zabbix_pve - retrieve proxmox virtual environment information for zabbix.

# SYNOPSIS

`zabbix_pve [option]`

# DESCRIPTION

This command supports the use of the included template in zabbix.

# INSTALLATION

In order for the command to be useful to zabbix, a number of installation steps
need to be done.

## AGENT CONFIGURATION

### ZABBIX_PVE INSTALLATION

The command needs to be installed on one or more zabbix agents. The default location
referenced in all files in this repository is:

`/usr/local/bin/zabbix_pve`

In order for this command to work, the following software needs to be available:

* curl
* jo
* jq
* gawk
* sed

### ZABBIX_PVE CONFIGURATION

There are two configuration files for this command.

#### PVE CONFIGURATION

In order to get information on the VM' and containers, the command uses the
proxmox API to collect information. This API requires a username and password.
These need to be provided in order to connect to the API.

The configuration file is located at:

`/etc/zabbix/zabbix-pve.conf`

The file contains two lines:

`USERNAME=username`

This line defines the username. Just like with all proxmox accounts, the realm
needs to be included.

`PASSWORD=password`

This line defines the password for the user.

Please take care to propperly protect the file and only give the user the minimum
of rights in order to collect information.

An example configuration is included in the repository.

#### PROCESS MONITORING CONFIGURATION

In order to monitor processes in containers, a configuration file needs to be
present. The monitoring is optional and so is the configuration file.

The file is located at:

`/etc/zabbix/zabbix-ctpid.conf`

The configuration file contains one line for each process to be monitored.
Each line contains the following information, seperated by comma's:

* Container ID the process lives in
* Shown name of the process
* Pattern to search the process list for

An example configuration is included in the repository.

### RUNNING

It is best to run the command from cron. The user running the command needs enough
privileges to run fping and read the configuration file.

In order to create statistics the command needs to be run with the getdata option:

`/usr/local/bin/zabbix_pve getdata`

The output data of the command will be stored in:

`/tmp/zabbix-pve`

A useful crontab entry would be:

`* * * * * /usr/local/bin/zabbix_pve getdata`

### CONFIGURING ZABBIX AGENT

Now that the information is begin gathered, the zabbix agent needs to be made aware
of how information can be retrieved. A configuration file is available in the
repository and should in installed at:

`/etc/zabbix/zabbix_agentd.d/zabbix_agent-pve.conf`

After the configuration has been added, reload the agent

`systemctl restart zabbix-agent`

## SERVER CONFIGURATION

The final step should be taken server side. In order to monitor the data a new
template needs to be imported.

* Open the zabbix webinterface
* Go to Configuration -> Templates
* Choose Import
* Choose browse and select the `template-pve.xml` file in the file browser.
* Make any require changes to rules
* Select Import

The template is now available. In order to configure it for a host with a zabbix
agent that has been configuratie with the command follow the steps below.

* Open the zabbix webinterface
* Go to Configuration -> Hosts
* Select the name of the host to configure
* Go to templates
* Choose Template OS PVE at the Link new templates box
* Choose Add
* Choose Update

Zabbix should now start collecting data.

If you want to exclude VM's or containers from generating alerts, a pool can be
defined on the proxmox system. VM's and containers should be placed in that pool.
For example:

* Open the proxmox webinterface
* Go to datacenter -> Permissions -> Pools
* Choose Create Pool and enter a Name and Comment
* Select the newly created pool at the bottom of the list
* Select Members
* Choose Add -> Virtual Machine
* Select the systems to add to the pool

If you choose a name other then test for the testpool, you need to define a macro
on the host level to exclude the pool from generating alerts.

To exclude the pool from generating alerts, do the following.

* Open the zabbix webinterface
* Go to Configuration -> Hosts
* Select the host
* Go to Macros
* Add a macro called {$PVE_TESTPOOL}
* Assign it the value of the Pool Name that was created
* Choose Update

The template sets a time of 1 hour before it alerts if a VM or containers is
locked. If this time needs to be changed, a macro can be defined on the host
level in order to change the value.

* Open the zabbix webinterface
* Go to Configuration -> Hosts
* Select the host
* Go to Macros
* Add a macro called {$PVE_LOCKTIME}
* Assign it the value of the timeout, for exampe 2h for 2 hours or 1m for 1 minute.
* Choose Update

# FILES

`/usr/local/bin/zabbix_pve` Command
`/etc/zabbix/zabbix-pve.conf` Credential configuration file
`/etc/zabbix/zabbix-ctpid.conf` Process monitoring configuration file
`/etc/zabbix/zabbix-agentd.d/zabbix_agent-pve.conf` Agent configuration file

# EXAMPLES

Check the output for discovery of vm's:

`/usr/local/bin/zabbix_pve vm.discover`

Check the output for discovery of processes:

`/usr/local/bin/zabbix_pve ctpid.discover`

# AUTHOR

Written by Mark Verboom

# REPORTING BUGS

Prefferably by opening an issue on the github page.

# COPYRIGHT

Copyright  ©  2014  Free Software Foundation, Inc.  License GPLv3+: GNU
GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free  to  change  and  redistribute  it.
There is NO WARRANTY, to the extent permitted by law.
