---
layout: post
title: "Simple login using ssh config file"
category: "Utils"
tags: [linux,ssh,utils]
---

### Problem ###
---------------------
If you need to login in dozens of unix machines, do you suffer the pain to input the password? Do the long parameters of ssh make you crazy? 

```
$ ssh user@mydev.domain.com -p 22000
```

Of couse, you can use shell alias and  public/private key to reduce the pain, however, there exists one more elegant way to solve this: **the ssh config file**.

### The SSH configuration file ###
---------------------
Enter this to ssh config (~/.ssh/config)

```
Host dev
    HostName mydev.domain.com
    Port 1022
    User user
    Identityfile ~/.ssh/your_id_file
```

Now you can just type few words to login in to that machine, very cool!

```
ssh dev
```

If you has many users in one domain with different password (identity), like the github, you can use alias:

```
Host github-project1
    User git1
    HostName github.com
    IdentityFile ~/.ssh/github.project1.key

Host github-org
    User git2
    HostName github.com
    IdentityFile ~/.ssh/github.org.key

Host github.com
    User git3
    IdentityFile ~/.ssh/github.key
```

### Multiplexing ###
--------------------
To reuse the TCP (a.k.a. SSH Multiplexing), add the following lines to configuration file:

```
ControlMaster auto
ControlPath ~/.ssh/multiplex/%r@%h:%p
ControlPersist 3h
```

- Unit if `` could be:
    - `NONE`: seconds
    - `S` \| `s`: seconds
    - `M` \| `m`: minutes
    - `H` \| `h`: hours
    - `W` \| `w`: weeks 
    
  Time format examples:

    - `600`: 600 seconds (10 minutes)
    - `10m`: 10 minutes
    - `1h30m`: 1 hour 30 minutes (90 minutes)
- Makesure the `~/.ssh/multiplex` exists

### Server timeout ###
------------------------
You can set the server timeout in the conf file to avoid long time wait after the connection broken:

```
ServerAliveInterval 60
ServerAliveCountMax 3
```

- `ServerAliveInterval`: A timeout interval in seconds after which if no data has been received from	the server, ssh will send a message through the encrypted channel to request a response from the server. The default is 0, indicating that these messages will not be sent to the server. 
- `ServerAliveCountMax`: Sets the number of	server alive messages (see `ServerAliveInterval`) which may be sent without ssh receiving any messages back from the server. If this threshold is reached while	server alive messages are being sent, ssh will disconnect from the server, terminating the session.

### Tunnel ###
------------------------
You can also configure the port forward in the config file

```
Host tunnel
    HostName mydev.domain.com
    IdentityFile ~/.ssh/your_id_file
    LocalForward 9906 127.0.0.1:3306
    User user
```

Then use `ssh -f -N tunnel` to set the forward, which is equals to the following commands

```
ssh -f -N -L 9906:127.0.0.1:3306 -i ~/.ssh/your_id_file user@mydev.domain.com
```

You can check other options in [here](http://linux.die.net/man/5/ssh_config)



 
