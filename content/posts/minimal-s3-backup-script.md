---
title: "A Tiny S3 Backup Script"
summary: "The latest discovery in my CLI obsession"
date: 2021-04-14T22:59:14-04:00
tags:
  - Tech
  - Home Lab
---

[I built a home lab]({{< ref "/posts/home-lab-build.md" >}})! And I installed Factorio on it for my friends! More on that process later, but suffice to say that, very quickly, the desire to back up the world automatically arose among the players. Backup solutions for home labs range from RAID arrays (which, admittedly, [are not backups](https://blog.storagecraft.com/5-reasons-raid-not-backup/)) to external, on-site NAS appliances to cloud-hosted off-site services.

For now though, I just needed a simple, cheap, and effective backup solution that would ensure that, if a hard drive died, the Factorio world that my friends had sunk many hours into would persist.

---

They say to stick with what you know, so of course my quick backup solution was to push the game's world file to AWS S3. S3 is a cloud-based, hyper-resilient, hyper-available object storage service - if an object is uploaded there, its safety is nearly guaranteed.

Luckily, my VMs already had access to my AWS account. I just had to create a new S3 bucket with object versioning turned on and start pushing files to it.

The idea of an automatic, recurring operation like this immediately made me think of [cron](https://en.wikipedia.org/wiki/Cron), a time-based scheduler that's baked into nearly every \*nix operating system in the world. So I knew that my backup would somehow use a cron-scheduled job (a "cron job") to trigger. Every morning at 4am sounded good - surely my players wouldn't be online then, which would minimize the risk of corruption or lag during the backup.

Next, I needed to write an upload script. This was the fun part! Since I was casting a wide net and uploading my entire Factorio game directory, I knew I would create a [ZIP](<https://en.wikipedia.org/wiki/ZIP_(file_format)>) archive which would then need to get uploaded to S3. Just zipping the Factorio directory looks like this:

```bash
# Create an archive of /factorio named factorio.zip
zip -r factorio.zip /factorio
```

Meanwhile, uploading that archive to S3 looks like this:

```bash
# Upload factorio.zip to the S3 bucket s3BucketName
/usr/local/bin/aws s3 cp factorio.zip s3://s3BucketName/factorio.zip
```

But wait! I don't want to have to run _two_ commands! I just want to run one! Why bother creating a ZIP file if I'm just pushing it to S3 - I'll have to remember to delete it later!

However, `zip` and the `aws s3 cp` command both play very nicely with the [standard streams](https://en.wikipedia.org/wiki/Standard_streams). The standard streams (STDIN for input, STDOUT for output, and STDERR for error output) are key aspects of the Unix philosophy. They're what allow you to chain terminal commands together into one super-command!

Though the `zip` and `aws s3 cp` commands default to output to and input from files, they also support the standard streams. That is, `zip` can write directly to STDOUT and `aws s3 cp` can read directly from STDIN. So we can chain the two commands together like so:

```bash
# Note the dashes!  Those say "to/from a standard stream!"
zip -r - /factorio | /usr/local/bin/aws s3 cp - s3://s3BucketName/factorio.zip
```

The "pipe" character `|` means, "connect STDOUT of the preceding command to STDIN of the next one!" Therefore, this one line says, "zip the `/factorio` directory and send that data to S3!" All that was left was to add that to my system's list of `cron` jobs using Ansible and the backup was in place. I also did some scripting to ensure that new VMs would pull the latest backup - that can be seen in its entirety in my lab's Ansible playbook, below.

---

As you can see, some common command line tricks allow us to create a tiny, lightweight backup program that can be deployed to nearly any computer. A proper backup plan gives the peace of mind required to experiment with your home lab without risking the loss of any important data.

To see how I use this backup script in my home lab, check out [my lab's Ansible playbook on GitHub](https://github.com/mleone10/home-lab-automation/blob/master/roles/common/templates/backup.j2). Also, keep an eye out for more posts about my home lab adventures in the future.

Until next time,
\- Mario
