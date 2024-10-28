# simplecron

## A simple cron job scheduler for sparsely running machines.

If you have scripts which need run periodically you probably rely on 
crontab or systemd timers. For crontab, if your machine is not
running 24/7 which is the case of most notebooks the script may not run on
specified time or day. Systemd timers are good alternatives but they have a more 
involving setup. Yet, systemd despite ubiquitous is not present on
all distros. 

Simplecron is easy to setup, have minimal depencies (ruby and cronie) and
just works.

# How it works

Simplecron relies on cronie (crontab) to work as it would be a service. 
Once enabled (see Usage below), crontab will run `simplecron run` every minute
and `simplecron` on its turn will take care of your tasks (see Setup below). 
Simple(cron) like that :-).


# Installation
1. Install `cronie` and enable its service.

2. Install `ruby`.

3. Download the `simplecron` script, make it executable, and
copy it to your `PATH`.

# Setup

1. Create the file `~/.config/simplecron/config` (you will need
to create the enclosing `simplecron` folder too). 

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

`enable` / `disable` : enable / disable `simplecron`

`run`: execute all tasks from `~/.config/simplecron/config` in serial. 
Normaly this is not intended to be used manually but through a crontab 
activated by the `simplecron enable` command. 

Running `simplecron` has the same affect of running `simplecron status`.

# Note

Simplecron monitors the exit codes of tasks. If exit code is non-zero 
(indicating a task failed to complete) it will retry the specific 
task every 10 minutes for the next hour. Then (if keep failing) 
attemps become sparser, to every 1h until successfully completion
i.e. simplecron receives an exit code equals 0.


