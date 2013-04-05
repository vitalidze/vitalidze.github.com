---
layout: post
tags : [linux, raspberry pi]
---
{% include JB/setup %}

Recently I got three Raspberry PIs. Ignoring fails of other PI users I want to set up a simple NAS on tiny PC. So, first all I've thought about storing data on SD card to increase I/O speed. However, some day this will kill both the SD card and as a result entire linux system. I got an external WD drive, which was bought to store wedding video archive. After re-partitioning I got a new 160GB ext4.

Checking raspberry PI write to external USB drive, ext4:

    # dd if=/dev/zero bs=512K count=2048 of=/media/disk-linux/speed.tmp
    2048+0 records in
    2048+0 records out
    1073741824 bytes (1.1 GB) copied, 43.4 s, 24.7 MB/s

    # dd if=/dev/zero bs=1M count=1024 of=/media/disk-linux/speed.tmp
    1024+0 records in
    1024+0 records out
    1073741824 bytes (1.1 GB) copied, 42.7062 s, 25.1 MB/s

    # dd if=/dev/zero bs=16M count=64 of=/media/disk-linux/speed.tmp
    64+0 records in
    64+0 records out
    1073741824 bytes (1.1 GB) copied, 46.4082 s, 23.1 MB/s
