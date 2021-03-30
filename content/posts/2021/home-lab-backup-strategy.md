---
title: "Minimal Home Lab Backup Strategy"
summary: "Until I configure a NAS, external storage, and long-term backup strategy, I needed something to ensure our precious game data was safe."
date: 2021-03-30T00:17:01-04:00
draft: true
tags:
  - Home Lab
  - Ansible
  - Tech
  - AWS
  - Systemd
---

# Motivation

- Don't want to lose things!
- Many hours of progress in Factorio, already lost data once when experimenting D:

# General Backup

- Offsite backup = data is preserved if something happens to my lab
- Lots of services for this, but I know S3
- Common requirement across all VMs, so codified it into common Ansible role
- Backup directory on every host gets zipped and uploaded to S3 every night
- S3 bucket configured to keep history, roll off to infrequent access (cheaper) after 30 days
- Cron job to execute every night at 3 (was 2, but we had a few late nights playing...)
- On VM init, search S3 for existing backup and reload it. Managed by Ansible, since init is always automated and only happens once.

# Backup on Shutdown

- Already had a working backup script, but what if someone was playing at noon and I wanted to rebuild the VM at 5p? Data loss! Must backup on shutdown.
- Systemd to the rescue. Services = long running background processes. ExecStop is usually used to tell processes to start shutting down before the kill signal. If the "process" is essentially nothing, ExecStop is just a random command that runs while a computer shuts down.
- Basics were working! Writing a date to a file ("echo $(date) > /root/test.txt") proved that command was running, but ZIP/upload wasn't producing results. Worse, logs were ephemeral (deleted on shutdown). Not helpful!
- Enabled debug logging on AWS, wrote to file. Ah! Networking stack was shutting down before the backup command, so upload had no connection to AWS!
- Next challenge, hunting for correct combination of service dependencies. "Wants" and "After" still confusing, but lots of guess-and-check later it works.
- Can safely start/stop/destroy instances without losing data!
