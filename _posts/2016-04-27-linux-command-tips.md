---
layout: post
title: "Linux Command Tips"
category: "Utils"
tags: [linux,utils,shell]
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
smbclient -u user -W WORKGROUP "//IP_DOMAIN/path/to/share" "PWD"
```

### Sync time ###

```bash
/usr/sbin/ntpdate 1.asia.pool.ntp.org
```

### Copy change svn files to folder ###

```bash
for i in $(svn st|grep '^[ADMR]' |cut -b 8-); do 
  x="b_/$(dirname $i)"
  mkdir -p $x
  cp "$i" "$x"
done
```

or

```bash
 for i in $(svn st | grep '^[ADMR]' | cut -b 8-); do x="b_/$(dirname $i)";mkdir -p $x;cp "$i" "$x";done
```

### Sync two folders ###

- Sync with remote machine

  ```bash
  rsync -avzh -P --stats --timeout=60 --delete-after . "user@10.0.0.0:~/target"
  ```

- Sync with two folders

  ```bash
  rsync -avzh -P --stats --timeout=60 --delete-after . "/path/to/target"
  ```


### Add user and change pwd ###

```
useradd -m user
echo "user:pwd" | chpasswd
```

### Edit users crontab ###

```
crontab -e
```

### Others ###
