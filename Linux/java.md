
java 环境变量配置 `/etc/profile`

```bash
export JAVA_HOME=/usr/java/jdk1.8.0_141/
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
```
