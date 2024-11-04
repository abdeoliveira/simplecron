# simplecron

## A simple cron job scheduler for sparsely running machines.

If you have scripts that need to run periodically, you might rely on tools like crontab or systemd timers. However, if your machine doesn’t run 24/7 -- as is often the case with laptops -- crontab may miss scheduled tasks if the system is off at the designated time. While systemd timers offer a more robust solution, they require a more complex setup and aren’t available on all Linux distributions.

Anacron is a viable option, but it lacks the capability to monitor whether jobs have successfully completed or failed. 

Fcron offers a wide range of features, though setting it up requires some extra time.

Simplecron, on the other hand, provides a straightforward, lightweight alternative that requires only Ruby and Cronie. It reliably manages periodic tasks and handles scheduled jobs differently based on their exit codes, distinguishing between successful and failed executions (see Logging and Error Handling).

# How it works

Simplecron leverages Cronie (crontab) to mimic a service functionality. 
Once enabled (see Usage below), crontab triggers Simplecron every minute, and Simplecron, in turn, manages your scheduled tasks (see Setup below). It’s as simple as that.


# Installation
1. Install `cronie` and enable its service.

2. Install `ruby`.

3. Download the `simplecron` script, make it executable, and
copy it to your `PATH`.

# Setup

1. Create the file `~/.config/simplecron/config` (you will need
to create the enclosing `simplecron` folder as well). 

2. Check the shipped `config-example` in order to create yours. 
Each line is a task (no blank lines allowed) in the format:

```
task_name, period, command
```

Note columns are separated by commas. `task_name`
can be anything since it is just a control name for your tasks. 
Control files (logs and records) are kept in `~/.cache/simplecron/`

3. Lines -- whole lines -- containing hashtags (`#`) anywehre are ignored. 

# Usage

Syntax: `simplecron [COMMAND]`

COMMANDS: `status`, `enable`, `disable`, and `run`.

`status`: Tells if simplecron is enabled or disabled.

`enable` / `disable` : enable / disable `simplecron` *per user*.

`run`: execute all tasks from `~/.config/simplecron/config` serialized. 
Normaly `run` is not intended to be used manually but through a crontab, 
activated by the `simplecron enable` command. 

Running `simplecron` has the same affect of running `simplecron status`.

# Logging and Error Handling

Logs are kept on `~/.cache/simplecron`.

Simplecron monitors the exit codes of tasks to handle failures intelligently. If a task returns a non-zero exit code (indicating failure), Simplecron will retry it every 10 minutes for up to an hour. If the task continues to fail, retries become less frequent, occurring every hour until it completes successfully, i.e., until Simplecron receives an exit code of 0.

