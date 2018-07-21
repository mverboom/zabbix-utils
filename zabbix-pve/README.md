# Zabbix PVE

This template can be use for systems running Proxmox Virtual Environment. The
template aims to give insight into the VM's and containers running on the system,
without having to install agents on every system.

This template requires at least Zabbix version 3.4.

The template collects:
* CPU usage
* Memory usage
* Disk usage
* Network usage
* Uptime
* VM state

Next to the information about the VM's and containers, it is also possible to
monitor processes in containers (this does not work for VM's). If configured
it monitors:

* Process restart
* Process stopped

The information is used to create graphs for trend analysis.

![Zabbix graph](graph.png)

Based on events, a number of triggers are available.

For the VM and container information:

* CPU over 80%
* Disk space critical
* Memory critical
* Restart of container or VM
* Stopped container or VM
* Locked container or VM

For the process monitoring:

* Process restart
* Process stopped

It is possible to define a macro to define a PVE Pool that is used for test
systems. When a VM or container is in this pool, the triggers will not be used.

For more information see:

[zabbix_pve](ZABBIX_PVE.md)
