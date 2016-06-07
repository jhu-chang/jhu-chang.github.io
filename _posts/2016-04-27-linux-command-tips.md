---
layout: post
title: "Linux Command Tips"
category: "Doc"
tags: [linux,utils]
---

### Find the latest 10 days file and archieve it

```bash
# Create an empty archieve file
tar cfT app.tar /dev/null
# find the logs the latest 10 days and added to the archieve file
find . -name app.log* -mtime -10 -exec tar --append --file=app.tar '{}' \;
tar --list --file=app.tar
# compress the archieve file
gzip app.tar
```

### SMB ###

```bash
smbclient -U user -W WORKGROUP "//IP_DOMAIN/path/to/share" "PWD"
```

### Sync time ###

```bash
/usr/sbin/ntpdate 1.asia.pool.ntp.org
```

### Others ###