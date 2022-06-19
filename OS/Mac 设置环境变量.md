### 打开.bash_profile

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export M="/Users/cuifeng/apache-maven-3.6.3"
export PATH="$M/bin:$PATH"
```

### 打开.zshrc

```
vim .zshrc
source .bash_profile
```
