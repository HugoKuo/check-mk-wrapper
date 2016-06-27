# Generating local check scripts 

* The path for storing check_mk local checks is `/usr/share/check-mk-agent/local/`
* The gen_checks command generates all daemons and sweep checks. 
* No disk usage and mount checks for check_mk local script since check_mk already has the native checks for disks.

## Files 

This repo includes two templates, a local check script generator and pre-generated check scripts for each series workers.

* The sweep time provides the many time ago of last worker cycle completed.  
* The worker checks the many time ago of latest log from particular worker. 

```
├── acc_group
│   ├── swiftstack-sweep-account-auditor
│   ├── swiftstack-sweep-account-reaper
│   ├── swiftstack-sweep-account-replicator
│   ├── swiftstack-worker-account-auditor
│   ├── swiftstack-worker-account-reaper
│   └── swiftstack-worker-account-replicator
├── con_group
│   ├── swiftstack-sweep-container-auditor
│   ├── swiftstack-sweep-container-replicator
│   ├── swiftstack-sweep-container-updater
│   ├── swiftstack-worker-container-auditor
│   ├── swiftstack-worker-container-replicator
│   └── swiftstack-worker-container-updater
├── gen_checks
├── obj_group
│   ├── swiftstack-sweep-object-auditor
│   ├── swiftstack-sweep-object-replicator
│   ├── swiftstack-sweep-object-updater
│   ├── swiftstack-worker-object-auditor
│   ├── swiftstack-worker-object-replicator
│   └── swiftstack-worker-object-updater
├── swiftstack-sweep.template
└── swiftstack-worker.template
```

* acc_group - Includes all checks of account-* workers
* con_group - Includes all checks of container-* workers
* obj_group - Includes all checks of object-* workers

### Default thresholds in the pre-generated scripts

You can copy the needed check scripts to `/usr/share/check-mk-agent/local/`. If the default threshold doesn't fit your case. 
To run `$gen_checks` to recreate check scripts with the custom thresholds. 

#### Daemon alive 

WARNING = 24 

CRITICAL = 50

#### Sweep time

WARNING = 24

CRITICAL = 100


## Usage : 

```
$./gen_checks <WARNING_daemon> <CRITICAL_daemon> <WARNING_sweep> <CRITICAL_sweep>
```

or 

```
$./gen_checks --interact 
```

The prompts ask for the threshold of each checks. The recommend value as below. 

```
Warning threshold for daemon alive in hours: 24
Critical threshold for daemon alive in hours: 50

Warning threshold for sweep time in hours: 24
Critical threshold for sweep time in hours: 100
```

You'll get the output as below. It simply means the new scripts are created. New created files overwrite old ones in 
each path.

```
Generating daemon status for acc_group
Generating daemon status for con_group
Generating daemon status for obj_group
Generating sweep time for acc_group
Generating sweep time for con_group
Generating sweep time for obj_group
```



## On the Alert server (Nagios host)

The following steps are reinventory and update nagios conf. Check_mk generates the nagios hosts and services profiles in `/etc/nagios/conf.d/check_mk_objects.cfg` automatically. 

### Performing an inventory 

```
$check_mk -v -I $host
```

### Reinventorize 

```
$check_mk -v -II $host
```

### Updating the Nagios configuration

You need to update nagios configuration by this command or the new added local checks will not show up in Nagios GUI.

```
$check_mk -R 
```

REF: https://mathias-kettner.de/checkmk_inventory.html
