+++
title = "An introduction to systemd timers"
date = 2023-09-16T14:25:27+02:00
draft = false
+++


## Scheduling tasks in Linux
If you are a sysadmin or a Linux enthusiast automating the boring stuff is crucial. Systemd timers are a powerful tool Linux provides you to schedule a certain task.

## Systemd timers
Systemd timers are unit files. A unit file is a ini-style text plain file that encodes information about a service.
Units are essentially resources systemd know how to deal with. Whether you want to modify a function or add one you can do it in the /etc/systemd/system directory.
A unit configuration file whose name ends in ".timer" encodes information about a timer controlled and supervised by systemd, for timer-based activation.
Systemd timers can run asynchronously. If the system is being turned off, timers can ensure the task to be executed at expected time.

## Timers instead of cron

- If your distribution adopts systemd you don't have to install another package
- Calendar syntax. You can check your timig by using the command 

![cal](/systemd-cal.png)

- Logging. Since timers are unit files we can monitor them by using journalctl.

## Setting up systemd timers
To start using systemd timers we need two unit files:
- The service that will be started
- The timer that schedule the service

Let's create the service unit and the timer unit

**service unit**

![service](/service_unit.png)



**timer unit**

![timer](/timer_unit.png)

task.sh will be executed every friday starting at 9 am, five minutes after boot then every three minutes. 

## An overview on Unit section

- **Description**: Describe the function of the task

- **Requires**: This directive lists any units upon which this unit essentially depends.

- **Conflicts**: List all units taht cannot be run at the same time as the current unit.

- **Wants**: A weaker version of Requires. Units listed in this option will be started if the configuring unit is. However, if the listed units fail to start or cannot be added to the transaction, this has no impact on the validity of the transaction as a whole. This is the recommended way to hook start-up of one unit to the start-up of another unit.


## An overview on Service section

- **simple**: The main process of the service is specified in the start line.

- **oneshot**: Used for one-off task.

- **ExecStart**: This specifies the full path and the arguments of the command to be executed to start the process. 

- **Restart**: This indicates the circumstances under which systemd will attempt to automatically restart the service. This can be set to values like “always”, “on-success”, “on-failure”, “on-abnormal”, “on-abort”, or “on-watchdog”. These will trigger a restart according to the way that the service was stopped.

## An overview on Timer section

- **OnBootSec**: Specify the amount of time after the system is booted when the associated unit should be activated.

- **Persistent**: If this is set, systemd will trigger the associated unit when the timer becomes active if it would have been triggered during the period in which the timer was inactive.

- **OnCalendar**: Specify the time unit is being performed. Calendar format: **DOW YYYY-MM-DD HH:MM:SS**

- **OnActiveSec**: This defines a timer relative to the moment the timer is activated. 

- **OnStartupSec**: Defines a timer relative to when the service manager was first started. For system timer units this is very similar to OnBootSec= as the system service manager is generally started very early at boot. It's primarily useful when configured in units running in the per-user service manager, as the user service manager is generally started on first login only, not already during boot.

- **OnUnitActiveSec**: Defines a timer relative to when the unit the timer unit is activating was last activated.

- **OnUnitInactiveSec**: Defines a timer relative to when the unit the timer unit is activating was last deactivated.

## Enable service and timer units

Type these systemctl commands to start timer and task

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now task.service
```

To list all the system timers just type

```bash
systemctl list-timers
```


