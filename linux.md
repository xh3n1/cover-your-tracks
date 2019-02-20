# Clearing user's history

Typically, a shell will have some kind of history logging enabled. If you want
to cover your tracks, obviously it should be removed.

Most popular shell used on Linux is `bash`.

## bash

```
history -c                    #Clear current session history
kill -9 $$                    #Kills current session history
export HISTFILESIZE=0         #Set history max lines to 0
unset HISTFILE		      #Disable history logging
echo """ > ~/.bash_history    #Delete current user history bash
rm -rf ~/.bash_history 	      #Delete .bash_history file
```

# Clearing syslog log files

When clearing log files, syslog configuration should be consulted, as exact
location of log files are dependent on the used distribution. To make matters
more complicated, there are multiple implementation of syslog available. 
Currently, the most popular implementation is `rsyslog`, usually configured via
`/etc/rsyslog.conf` file.

Most security-related events are logged using `authpriv` facility.

```
# grep ^authpriv /etc/rsyslog.conf
authpriv.*                                              /var/log/secure
```

For example on Debian-based distributions, the default location of file
holding `authpriv` events is `/var/log/auth.log`, while on RedHat-based
distributions it is '/var/log/secure'.

```
echo "" > /var/log/auth.log   #Clear auth.log file on Debian-based systems
echo "" > /var/log/secure   #Clear auth.log file on RedHat-based systems
```

Note that on modern Linux distributions, syslog is a completely optional
component. While most distros keep it to maintain plaintext log files, it can
be safely disabled and completely replaced by `systemd-journal`. For example
the default minimal installation of Fedora does not use syslog at all. See below for more details on `systemd-journal`.

# lastlog and wtmp

TBD

# Clearing systemd journal

TBD: run-time journal, persistent journal

# Clearing the audit log

The audit subsystem, and `auditd` daemon is deployed by default on many distros.
Audit logs should be removed, as they contain a lot of useful information that
can be used for forensics.

To determine the log location, `auditd` configuration should be consulted first.
Most distributions use the `/var/log/audit` location.

```
$ grep ^log_file /etc/audit/auditd.conf
log_file = /var/log/audit/audit.log
$ rm -rf /var/log/audit/
```

Depending on circumstances, it may be also necessary to kill the `auditd`.
Often it is not possible to stop `auditd` via systemd (since the unit
is configured not to allow that). Also, note that depending on kernel
configuration, when auditd is killed, its logs may appear in the kernel message
buffer (`dmesg`).

