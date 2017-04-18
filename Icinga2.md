# Icinga2
## Important file locations
```
/usr/lib/nagios/plugins/
/usr/share/icinga2/include
/etc/icinga2
/var/run/icinga2/cmd
/usr/share/icingaweb2/modules
/etc/icingaweb2
```
## Passive service checks
A passsive service check can be defined by using the `passive` check_command.
Actually this will only create a service that uses the special `passive` command
that comes with some nice defaults. All services accept passive commands by
default. This can be verified by checking `enable_passive_checks` variable on
the service object.

The special socket file `/var/run/icinga2/cmd/icinga2.cmd` can be sent output in
below format: [icinga source](https://docs.icinga.com/latest/en/passivechecks.html#servicecheckresults)
```
 [<timestamp>] PROCESS_SERVICE_CHECK_RESULT;<host_name>;<svc_description>;<return_code>;<plugin_output>
```
where...

* __timestamp__ is the time in time_t format (seconds since the UNIX epoch)
  that the service check was perfomed (or submitted). Please note the single
space after the right bracket.
* __host_name__ is the short name of the host associated with the service in
  the service definition
* __svc_description__ is the description of the service as specified in the
  service definition
* __return_code__ is the return code of the check (0=OK, 1=WARNING, 2=CRITICAL,
  3=UNKNOWN)
* __plugin_output__ is the text output of the service check (i.e. the plugin
  output)

In other words:
```
printf "[$(date +%s)] PROCESS_SERVICE_CHECK_RESULT;Test VM;passive-test;0;This is the Plugin output|dewpoint=21.5\n" >> /var/run/icinga2/cmd/icinga2.cmd
```
should do the trick.

## Thoughts
* Try to avoid using hyphens in service names and in particular custom command
  names. The check_command name is used by InfluxDb as the measurement name, and
hyphens need special quoting. Underscores are better for this as they do not
require quoting in InfluxDb.
* Be aware that check_command is used by default as the measurement name in
  InfluxDb. This is controlled by the InfluxDb template in Icinga, and so can be
changed. But different metrics using the same command; for example `snmp` will
have all the metrics in the same "snmp" measurement table. This may not be
ideal. I have been thinking about changing the template to the service name
instead, but we also have to consider the benefits of multiple values in fewer
tables as a good thing performance-wise instead of multiple tables with only few
values in each table.
