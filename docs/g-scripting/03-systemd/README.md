# Process Management

## Process ID 1 

Systemd is a system that is designed specifically for the Linux kernel. It replaces the sysvinit process to become the first process with PID = 1, which gets executed in user space during the Linux start-up process.  

Why systemd?

It is one of the first questions that came up to mind when discussing systemd. To find the answer, we have to first know a bit about the sysvinit. If we forget about systemd and the other similar systems, then it’s safe to say that sysvinit is the first process started by the kernel when you boot up any Linux or Unix computer. This means that all the other processes are their child in one way or the other.

Once the system is successfully booted, the sysvinit process continues to run and waits for special commands like `shutdown`, which are used to shut down a Linux system. That means now the task of the sysvinit process is to gracefully shutdown the system. For many years, the sysvinit remained a perfect system to bring up and shutdown Linux-based systems. But as time passed by, the system became slow and inflexible, especially for modern-day computers.

Check Current systemd Version:
```bash
systemctl --version
```

## Systemctl

System services play a crucial role in the functioning of a Linux system, handling various tasks and processes in the background. systemctl is a powerful command-line tool that allows users to manage these services effectively. In this article, we will explore the basics of using systemctl to start, stop, restart, enable, disable and display status of services in a Linux environment.

Before diving into service management, it’s essential to understand the basics of `systemctl`. This command is used to control the systemd system and service manager, which is a central component in modern Linux distributions.

```bash
systemctl [command] [unit]
```

Here,

- command: Action to be performed (e.g., start, stop, restart, enable, disable).
- unit: The service or unit to be affected.

Systemctl is a controller or utility of Systemd(is an init system with compost for a set of programs executed in the background), with auxiliary in manage services, these commands are executed in mode root if you aren’t mode root the system, requesting the password of root.

### Common commands with systemctl

To list available systems units or to List all Services in Linux we use the following command:
Syntax:

```bash
systemctl list-unit-files --type service -all
```

The command start serves for starting (activate) one or more units specified on the command line.
Syntax:

```bash
sudo systemctl start service.service
```

The command stop serves for stopping the service or (deactivate) one or more units specified on the command line.
Syntax:

```bash
sudo systemctl stop service.service
```

The command status serves to check the status of the service. Show terse runtime status information about one or more units, followed by the most recent log data from the journal. If no units are specified, show system status.
Syntax:

```bash
sudo systemctl status service.service
```

The command restart serves for restarting the service in execution. Stop and then start one or more units specified on the command line. If the units are not running yet, they will be started.
Syntax:

```bash
sudo systemctl restart service.service
```

The enable command serves for executing the service since the initialization if consists of one or more units or unit instances. This will create a set of symlinks, as encoded in the [Install] sections of the indicated unit files. the system manager configuration is reloaded (in a way equivalent to daemon-reload), in order to ensure the changes are taken into account immediately. 
Syntax:

```bash
sudo systemctl enable name_service.service
```

The disable command serves for withdrawing the service since the initialization of one or more units. This removes all symlinks to the unit files backing the specified units from the unit configuration directory and hence undoes any changes made by enabling or link.
Syntax:

```bash
sudo systemctl disable name_service.service
```

##More process management commands
There are different process commands in Linux mainly 5 commands are widely used which are `ps`, `wait`, `sleep`, `kill`, `exit`.

`ps` is an acronym for process status. It displays information about the active processes. 
`wait` command will suspend execution of the calling thread until one of its children terminate. It will return the exit status of that command. 
`sleep` command is used to delay the execution of the next command for a given number of seconds, hours, minutes, days. 
`kill` is used to terminate a background running process. It sends a terminate signal to the process and then processes halts. It takes process ID as an argument. 
`exit` command is used to exit from the current shell environment.

##Shutdown

The `shutdown` command in Linux is used to shutdown the system in a safe way. You can shutdown the machine immediately, or schedule a shutdown using 24 hour format.It brings the system down in a secure way. When the shutdown is initiated, all logged-in users and processes are notified that the system is going down, and no further logins are allowed.
Only root user can execute shutdown command.

Syntax of shutdown Command

```bash
shutdown [OPTIONS] [TIME] [MESSAGE]
```

options – Shutdown options such as halt, power-off (the default option) or reboot the system.
time – The time argument specifies when to perform the shutdown process.
message – The message argument specifies a message which will be broadcast to all users.
Options
-r : Requests that the system be rebooted after it has been brought down.
-h : Requests that the system be either halted or powered off after it has been brought down, with the choice as to which left up to the system.
-c : Cancels a running shutdown. TIME is not specified with this option, the first argument is MESSAGE.
-k : Only send out the warning messages and disable logins, do not actually bring the system down.

How to use shutdown
In it’s simplest form when used without any argument, shutdown will power off the machine.
```bash
sudo shutdown
```
How to shutdown the system at a specified time
The time argument can have two different formats. It can be an absolute time in the format hh:mm and relative time in the format +m where m is the number of minutes from now.

The following example will schedule a system shutdown at 05 A.M:
```bash
sudo shutdown 05:00
```
The following example will schedule a system shutdown in 20 minutes from now:
```bash
sudo shutdown +20
```
How to shutdown the system immediately
To shutdown your system immediately you can use +0 or its alias now:
```bash
sudo shutdown now
```
