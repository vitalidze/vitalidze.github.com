---
layout: post
tags : [linux, raspberry pi]
---
{% include JB/setup %}

Recently I got three Raspberry PIs. Ignoring fails of other PI users I want to set up a simple NAS on tiny PC. So, first all I've thought about storing data on SD card to increase I/O speed. However, some day this will kill both the SD card and as a result entire linux system. I got an external WD drive, which was bought to store wedding video archive. After re-partitioning I got a new 160GB ext4.

Checking raspberry PI write to external USB drive, ext4:

    # dd if=/dev/zero bs=512K count=2048 of=/media/disk-linux/speed.tmp
    1073741824 bytes (1.1 GB) copied, 43.4 s, 24.7 MB/s

    # dd if=/dev/zero bs=1M count=1024 of=/media/disk-linux/speed.tmp
    1073741824 bytes (1.1 GB) copied, 42.7062 s, 25.1 MB/s

    # dd if=/dev/zero bs=16M count=64 of=/media/disk-linux/speed.tmp
    1073741824 bytes (1.1 GB) copied, 46.4082 s, 23.1 MB/s

Checking read from same ext4 external USB drive:

    # dd if=/media/disk-linux/speed.tmp bs=512k count=2048 of=/dev/null
    1073741824 bytes (1.1 GB) copied, 38.1892 s, 28.1 MB/s

    # dd if=/media/disk-linux/speed.tmp bs=1M of=/dev/null
    1073741824 bytes (1.1 GB) copied, 38.2423 s, 28.1 MB/s
    
    # dd if=/media/disk-linux/speed.tmp bs=16M of=/dev/null
    1073741824 bytes (1.1 GB) copied, 38.3897 s, 28.0 MB/s
    
Similar tests for SD card:

    # dd if=/dev/zero bs=512K count=2048 of=/usr/data/speed.tmp
    1073741824 bytes (1.1 GB) copied, 62.1034 s, 17.3 MB/s
    
    # dd if=/dev/zero bs=1M count=1024 of=/usr/data/speed.tmp
    1073741824 bytes (1.1 GB) copied, 64.2532 s, 16.7 MB/s
    
    # sudo dd if=/usr/data/speed.tmp bs=512k of=/dev/null
    1073741824 bytes (1.1 GB) copied, 50.2513 s, 21.4 MB/s
    
Conclusion: just use the external drive to store data on raspberry PI. Don't forget to buy good power source for both raspberry PI and external drive. (I use USB hub with separate power adapter)
