---
layout: post
title: "Git skills"
category: "Doc"
tags: [git,vcs]
---

### Set proxy for git ###
--------------

- Create the proxy

  ```
  git config --global http.proxy http://server:port
  git config --global http.sslcainfo /path/to/ca.crt
  ```

- Reset the proxy

  ```
  git config --global --unset http.proxy
  git config --global --unset http.sslcainfo
  ```

### Ignore files ###
--------------

Edit the `.gitignore` to suppresses files

```
*.log
build/
target/
temp.*
*.tmp
```

### Git revert ###
---------------------

- Add/change files for last commit

  ```
  git commit --amend
  ```

- Unstaging a Staged File

  ```
  git reset HEAD FILE
  ```

- Unmodifying a Modified File

  ```
  git checkout -- FILE
  ```

- Undo all commits, preserving changes

  ```
  git reset [commit]
  ```

- Discards the changes

  ```
  git reset --hard [commit]
  ```

