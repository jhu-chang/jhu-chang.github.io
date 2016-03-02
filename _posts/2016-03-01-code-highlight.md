---
date: 2016-03-01 10:00:00+00:00
layout: post
title: "Test the code highlight"
categories: Misc
tags: [Code, Highlight]
---

Here is the doc to test code highlight

### C++
```cpp
class A {
    public void f(int i) {
    }
}
```

### Java
```java
class A {
    final public void f(int i) {
       return null;
    }
}
```

### Bash
```bash
#!/bin/bash

if [[ -z "$1" || ! -f $1 ]]; then
    echo "Please provide the correct properties file"
    exit 1
fi

if [[ -z "$VTBA_HOME" ]]; then
    echo "VTBA_HOME is not set"
    exit 1
fi


if [[ ! -f "$VTBA_HOME/jboss/server/vtba/deploy/weatherservice.war" ]]; then
    echo "Could not find the weather service war file under $VTBA_HOME/jboss/server/vtba/deploy"
    exit 1
fi

WTMP_PATH=/tmp/weather_tmp
mkdir -p "$WTMP_PATH/WEB-INF/classes"
cp "$1"  "$WTMP_PATH/WEB-INF/classes/"
jar uvf "$VTBA_HOME/jboss/server/vtba/deploy/weatherservice.war" -C "$WTMP_PATH" .
rm -fr "$WTMP_PATH"

```


