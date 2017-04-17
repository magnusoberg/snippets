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
Actually this will only create a service that run the special `passive` command
that comes with some nice defaults. All service accept passive commands by
default. This can be verified by checking `enable_passive_checks`

The special socket file `/var/run/icinga2/cmd/icinga2.cmd` can be sent output in
below format: [icinga source](https://docs.icinga.com/latest/en/passivechecks.html)
```
 [<timestamp>] PROCESS_SERVICE_CHECK_RESULT;<host_name>;<svc_description>;<return_code>;<plugin_output>
```
In other words:
```
printf "[$(date +%s)] PROCESS_SERVICE_CHECK_RESULT;Test VM;passive-test;0;This is the Plugin output|dewpoint=21.5\n" >> /var/run/icinga2/cmd/icinga2.cmd
```
This should do the trick.
