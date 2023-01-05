### 1、下载Reids源码

https://download.redis.io/releases/

### 2、解压缩

```bash
tar -zxvf redis-6.2.8.tar.gz 
```

### 3、安装C编译器

```bash
yum -y install gcc-c++
```

检查

```bash
gcc -v
```

### 4、编译

```bash
cd /data/redis-6.2.8/

make
```

### 5、安装

```bash
make install PREFIX=/data/redis-6.2.8
```

### 6、Redis配置文件详解

```ini
# 绑定ip
bind 0.0.0.0
# 保护模式
protected-mode yes
# 端口
port 6379
# 此参数确定了TCP连接中已完成队列(完成三次握手之后)的长度
# 此值必须不大于Linux系统定义的/proc/sys/net/core/somaxconn值
# Redis默认是511，而Linux的默认参数值是128。当系统并发量大并且客户端速度缓慢的时候
# 可以将这二个参数一起参考设定，建议修改为 2048
# 
# 该内核参数默认值一般是128，对于负载很大的服务程序来说远远不够。一般会修改为2048或者更大
# 在/etc/sysctl.conf中添加如下
# net.core.somaxconn = 2048
# 然后执行
# sysctl -p
tcp-backlog 511
# 在timeout时间(秒)内如果没有数据交互，redis侧将关闭连接。
# 	1.0代表永不断开(在macOS测试不受内核保活定时器影响)。
#	2.tcp/ip连接、unix socket连接均受timeout影响。
timeout 0
# 定时向client发送tcp_ack包来探测client是否存活，默认300秒
tcp-keepalive 300
# redis采用的是单进程多线程的模式
# daemonize设置成yes时，代表开启守护进程模式，在该模式下，redis会在后台运行，
# 并将进程pid号写入pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程。
# daemonize设置成no时，当前界面将进入redis的命令行界面
daemonize no
# 指定pidfile路径
pidfile /var/run/redis_6379.pid
# 日志等级
#	1）debug：会打印出很多信息，适用于开发和测试阶段
#　　2）verbose（冗长的）：包含很多不太有用的信息，但比debug要清晰一些
#　　3）notice：适用于生产模式
#　　4）warning : 警告信息
loglevel notice
# 日志文件路径
logfile ""
# 数据库数量，默认16个，编号从0到15
databases 16
# 是否总是显示 reids logo 
always-show-logo no
# 设置为yes以proc-title-template的格式显示进程标题
set-proc-title yes
# 设置进程标题和进程标题的格式
proc-title-template "{title} {listen-addr} {server-mode}"
# 如果设置为yes：redis 会创建一个新的后台进程dump rdb。
# 假设：
#		创建快照（硬盘上，产生一个新的rdb文件）需要 20s时间，redis主进程在这20s内，，
#   会继续接受客户端命令，但是就在这20s内，创建快照出错了，比如磁盘满了，那么此时redis
#   会认为，Redis is configured to save RDB snapshots, but is currently not 
#   able to persist on disk. but is currently not able to persist on disk.
#   redis会拒绝新的写入，也就是说，它认为你当下持久化数据出现了问题，你就不能再set了。
stop-writes-on-bgsave-error yes
# 如果设为yes，那么遇到字符串对象并且其长度超过20个字节，生成RDB文件时，会对其进行LZF算法压缩
rdbcompression yes
# 默认开启，存储快照后，redis使用CRC64算法来进行数据校验。这个操作会增加10%的性能消耗
rdbchecksum yes
# 用于生成的快照文件的名字
dbfilename dump.rdb
# 默认是no，主从进行全量同步时，通过传输 RDB 内存快照文件实现
# 没有开启RDB持久化的实例在同步完成后会删除该文件
rdb-del-sync-files no
# dir用于配置生成的快照文件存储的位置，默认为./，即当前目录下。
dir ./
# 当一个slave失去和master的连接，或者同步正在进行中，slave的行为有两种可能：
# 	1) 设置为yes(默认值)，slave会继续响应客户端请求，
#      可能是正常数据，也可能是还没获得值的空数据。
# 	2) 设置为no，除了INFO和SLAVEOF命令，slave会回复
#	   SYNC with master in progress（正在从master同步）
replica-serve-stale-data yes
# salve实例是否只读
# 可写的slave实例可能对存储临时数据比较有用（写入salve的数据在同master同步之后将很容被删除）
# 从Redis2.6默认所有的slave为只读
#
# 注意：只读的slave不是为了暴露给互联网上不可信的客户端而设计的
#	它只是一个防止实例误用的保护层。一个只读的slave支持所有的管
#   理命令比如config,debug等。为了限制你可以用'rename-command'
#   来隐藏所有的管理和危险命令来增强只读slave的安全性。
replica-read-only yes
# 同步策略: 指定否使用socket方式复制数据
# 	redis同步数据的方式有两种模式：disk或socket，默认是disk方式
#	1）socket是指master在做快照时，不将rdb存入磁盘，直接将通过网络发送给slave
#		在多个slave情景中，它是串行复制（第一个slave同步完成后，再同步第二个）
#	2）disk是指master先将rdb保存到磁盘，然后在发送给slave
#		在多个slave情景中，可以共享rdb文件，主节点不用重复生成多个相同的rdb
#		通常只在磁盘速度慢但网络较快时下才使用socket方式，否则都是用disk方式
repl-diskless-sync no
# 指定disk方式同步数据的延迟时间，单位秒
# 0表示关闭延迟，关闭延迟则意味着一旦有同步请求，在同步结束前，master不会再接收新的
# slave的同步请求，直到本次同步完成
repl-diskless-sync-delay 5
# 是否使用无磁盘加载，有三项：
#	disabled：不使用无磁盘加载，先将rdb文件存储到磁盘（默认配置）
#	on-empty-db：只有在完全安全的情况下才使用无磁盘加载
#	swapdb：解析时在RAM中保留当前db内容的副本，直接从套接字获取数据。
repl-diskless-load disabled
# 在slave和master同步后（发送psync/sync），后续的同步是否禁用TCP_NODELAY
#	设成yes，master会合并小的TCP包从而节省带宽，但会增加同步延迟（40ms）
#		造成master与slave数据不一致，适合垮机房部署
#	设成no，master会立即发送同步数据，无延迟，master命令无论大小都会发送给slave
#		会增加带宽消耗，适合主从网络良好的场景
# 前者关注性能，后者关注一致性
repl-disable-tcp-nodelay no
# 选举优先级，默认值为 100
#	当 master 节点无法正常工作后，Sentinel（哨兵模式）通过这个值来决定将谁
#	提升为 master 节点数值越小表示优先级越高，值为0表示永远不被提升为 master 节点
replica-priority 100
# 设置acl日志的最大条数，acl日志是存在内存里的，用于记录跟acl相关的命令失败和认证事件
# 可以用ACL LOG RESET命令清空日志
acllog-max-len 128
# redis内存达到阈值maxmemory时，是否开启lazy free
lazyfree-lazy-eviction no
# 设置了过期时间的键值，当过期之后是否开启lazy free删除
lazyfree-lazy-expire no
# 有些指令在处理已存在的键时，会带有一个隐式的del的操作，比如rename命令，当目标键已存在时
# Redis会先删除目标键，如果这些目标键是一个 big key，就会造成阻塞删除的问题
# 此配置表示在这种场景中是否开启 lazy free 机制删除；
lazyfree-lazy-server-del no
# 全量同步时，slave是否异步flush本地redis
replica-lazy-flush no
# 支持yes或者no。默认是no。
# 	如果设置为yes，那么del命令就等价于unlink，也是非阻塞删除。
lazyfree-lazy-user-del no
# 设置FLUSHDB为异步还是同步，yes为异步，no为同步（默认）
lazyfree-lazy-user-flush no
# KERNEL OOM CONTROL 设置OOM时终止哪些进程
# 启用此功能可使Redis根据其角色主动控制其所有进程的oom_score_adj值
# 默认分数将尝试在所有其他进程之前杀死背景子进程，并在主进程之前杀死从节点进程
#	no：对oom-score-adj不做任何修改（默认值）
#	yes：relative的别名
#	absolute：oom-score-adj-values配置的值将写入内核
#	relative：当服务器启动时，使用相对于oom_score_adj初始值的值
#			  然后将其限制在-1000到1000的范围内，因为初始值通常为0
#			  所以它们通常与绝对值匹配。
# oom-score-adj选项不为no，用于控制主、从和后台子进程的特定值，数值范围为-2000到2000
#（越高意味着死亡的可能性越大）；非特权进程（不是根进程，也没有CAP_SYS_RESOURCE功能）可以
# 自由地增加它们的价值，但不能将其降低到初始设置以下，这意味着将oom-score-adj设置为relative，
# 并将oom-score-adj-values设置为正值
oom-score-adj no
# 分别控制主进程、从进程和后台子进程的值
oom-score-adj-values 0 200 800
# Redis建议关闭Transparent Huge Pages（THP）(透明大页)
#
# 在Linux 64位系统里面,默认内存是以4K的页面（Page）来管理的，如果是一个服务器内存比较大
# 那么管理大内存需要形成的页面列表(相当于索引表)就很大，而Hugepages的默认值page size为2M
# 是4KB的500倍，所以可以大大减小Page Table的大小，通过启用 HugePages使用大页面，可以用一个
# 页表条目代表一个大页面，而不是使用许多条目代表较小的页面，从而可以管理更多内存，减少操作系统对
# 页面状态的维护并提高 CPU中TLB 缓存命中率。Hugepagesize的大小默认为2M，这个也是可以调整的
# 范围为2MB to 256MB
#
# 标准大页管理是预分配的方式，而透明大页管理则是动态分配的方式
# 不要将Huge Page和Transparent Huge Pages混为一谈。目前透明大页与传统HugePages联用
# 会出现一些问题导致性能问题和系统重启。Oracle 建议禁用透明大页（Transparent Huge Pages）
# 在 Oracle Linux 6.5 版中，已删除透明 HugePages。
#
# Redis在持久化AOF过程中都存在创建子进程的情况
# Redis 在AOF持久化过程中会fork一个子进程进行AOF操作。这块又涉及到fork过程的CopyOnWrite机制。
# 在fork出子进程后，子进程与父进程共享内存空间，两者只是虚拟空间不同，但是其对应的物理空间是同一个
# 这里有两个关键地方：
# 	1）fork子进程会拷贝父进程的页面索引列表，如果索引列表小，那么fork拷贝的内存就会小
#     那么fork子进程的速度就会快。
#   2）当fork之后，kernel把父进程中所有的内存页的权限都设为read-only，然后子进程的
#     地址空间指向父进程当父子进程都只读内存时，相安无事。当其中某个进程写内存时，CPU
#     硬件检测到内存页是read-only的，于是触发页异常中断（page-fault），陷入kernel
#     的一个中断例程。中断例程中，kernel就会把触发的异常的页复制一份，于是父子进程各自
#     持有独立的一份。如果使用大页每次有页面要修改，那么就要拷贝一个2MB的大页面会大幅增
#     加重Redis写期间父进程内存消耗。同时每次写命令引起的复制内存页单位为2MB，会拖慢写
#     操作的执行时间，导致大量写操作慢查询，
# 所以除了“透明大页与传统HugePages联用会出现一些问题，导致性能问题和系统重启"外，上面这两点是Redis
# 建议关闭THP的更重要的原因。
disable-thp yes
# 是否开启aof持久化，默认不开启
# aof持久化是追加模式，也就是如果使用的是aof持久化，那么在redis写的所有的命令都会被追加到
# appendonly.aof持久化文件中aof持久化方式不像rdb持久化，因为rdb持久化不会在dump.rdb文件
# 中追加内容，它是先对redis此时16个数据库的状态进行一次快照操作，然后生成一个持久化文件dump.rdb
# 然后用后面生成的这个dump.rdb文件直接替换掉上一个dump.rdb文件
#
# 注意：aof持久化文件appendonly.aof中存储的都是redis数据库中已经执行过
#      的命令但是rdb持久化文件dump.rdb中存储的是redis数据库中的内容。
appendonly no
# aof持久化文件名
appendfilename "appendonly.aof"
# 对redis性能有重要影响
#	always：会极大消弱Redis的性能，每执行一个写命令，就对AOF文件执行一次
#			冲洗操作（fsync，Linux为调用fdatasync）
#	everysec：默认值，每隔1秒钟就对AOF文件执行一次冲洗操作。性能并不是很糟糕，
#			  一般也不会产生毛刺这归功于Redis引入了BIO线程，所有冲洗操作都异
#			  步交给了BIO线程
#	no：不主动对AOF文件执行冲洗操作，由操作系统决定何时对AOF进行冲洗
#
# 这3种不同的冲洗策略不仅会直接影响服务器在停机时丢失的数据量，还会影响服务器在运行时的性能
# 	always：服务器在停机时最多只会丢失一个命令的数据，使用这种方式将使Redis的
#           性能降低至传统关系数据库的水平
#	everysec：服务器在停机时最多只会丢失1s之内产生的命令数据，这是一种兼顾性能和
#             安全性的折中方案。
#	no：服务器在停机时将丢失系统最后一次冲洗AOF文件之后产生的所有命令数据，至于数据量
#       的具体大小则取决于系统冲洗AOF文件的频率。因为no策略给可能丢失的数据量带来了不
#		确定性，而always策略对于安全性的追求又牺牲了服务器的性能所以Redis使用everysec
#	    作为appendfsync选项的默认值
# 除非有明确的需求，否则用户不应该随意修改appendfsync选项的值。
appendfsync everysec
# 主进程在写aof文件的时候出现阻塞的情形，
# 	设置为no：最安全的方式，不会丢失数据，但是会阻塞
# 	设置为yes：相当于将appendfsync设置为no，说明并没有执行磁盘操作，只是写入了缓冲区
#             不会造成阻塞（因为没有竞争磁盘），此时redis挂掉，会丢失数据，linux默认
#			  设置下，最多会丢失30s的数据。
# 如果应用系统无法忍受延迟，而可以容忍少量的数据丢失，则设置为yes
# 如果应用系统无法忍受数据丢失，则设置为no。
no-appendfsync-on-rewrite no
# 指定 Redis 重写 AOF 文件的条件，默认为 100
# 它会对比上次生成的 AOF 文件大小。如果当前 AOF 文件的增长量大于上次 AOF 文件的 100%
# 就会触发重写操作；如果将该选项设置为 0，则不会触发重写操作。
auto-aof-rewrite-percentage 100
# 指定触发重写操作的 AOF 文件的最小值，默认为 64mb
# 若当前AOF文件的大小低于该值，就算当前文件的增量比例达到了
# auto-aof-rewrite-percentage设置的条件也不会触发重写操作，
# 只有同时满足这两个选项所设置的条件，才会触发重写操作。
auto-aof-rewrite-min-size 64mb
# 当 AOF 文件结尾遭到损坏时，Redis 在启动时是否仍加载 AOF 文件
aof-load-truncated yes
# 开启RDB和AOF混合模式，默认打开
# 开启后，当aof触发rewrite后会执行bgsave，同时将生成的rdb文件和rewrite后
# 生成的aof文件合并成新的aof文件并替换旧的aof文件。这时候aof文件分为两部分，
# 一部分为bgsave生成的rdb文件，另一部分后后续追加的aof文件
#
# 优点：结合 RDB 和 AOF 的优点, 更快的重写和恢复。
# 缺点：AOF 文件里面的 RDB 部分不再是 AOF 格式，可读性差。
aof-use-rdb-preamble yes
# lua 脚本的最大运行时间，单位毫秒：
# 如果此值设置为0或负数，既不会有报错也不会有时间限制
lua-time-limit 5000
# 指定慢查询日志时长的阈值，执行命令的时长超过这个阈值时就会被记录下来
# 单位是微秒（1秒 = 1000毫秒 = 1000000微秒），默认是10000微秒
# 若设置为0，将会记录所有命令到日志中
# 若设置小于0，将会不记录任何命令到日志中
# 因为Redis采用单线程响应命令,如果命令执行时间在1000微秒以上，那么Redis
# 最多可支撑OPS不到1000，所以对于高并发场景的Redis建议设置为1000微秒。
slowlog-log-slower-than 10000
# 指定慢查询日志最多存储的条数，Redis使用了一个列表存放慢查询日志，该参数
# 就是这个列表的最大长度。当一个新的命令满足慢查询条件时，被插入这个列表中
# 当慢查询日志列表已经达到最大长度时，最早插入的那条命令将被从列表中移出。
# 记录慢查询时Redis会对长命令进行截断，不会大量占用大量内存。在实际的生产
# 环境中，为了减缓慢查询被移出的可能和更方便地定位慢查询，建议将慢查询日志
# 的长度调整的大一些。比如可以设置为1000以上。
slowlog-max-len 128
# redis延迟监控，默认关闭，单位：毫秒，如果设置为 0，则表示关闭延迟监控。
latency-monitor-threshold 0
# 事件订阅/发布，默认关闭，当redis中某个key产生了某种变动（某个事件），
# Redis会将这个事件推送到指定的事件监听中，接收到的事件的类型只有两种： 
# keyspace 和 keyevent。
# 
# K	键空间通知，所有通知以__keyspace@__ 为前缀
# E	键事件通知，所有通知以 __keyevent@__ 为前缀
# g	DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
# $	字符串命令的通知
# l	列表命令的通知
# s	集合命令的通知
# h	哈希命令的通知
# z	有序集合命令的通知
# x	过期事件，每当有过期键被删除时发送
# e	驱逐事件，每当有键因为maxmemory政策而被删除时发送
# A	参数 g$lshzxe 的别名
# 输入的参数中至少要有一个K或者E，否则其余参数不会有任何的通知生效。
# 如果想订阅所有的通知，直接设置为AKE。
notify-keyspace-events ""
# 默认值512，hash在满足以下条件时会采用ziplist编码
# 	value字节数<= hash-max-ziplist-value && 元素数<= hash-max-ziplist-entries	
# hash的内部编码有两种
# 	ziplist（压缩列表）：使用更加紧凑的结构实现多个元素的连续存储，比hashtable更省内存。
# 	hashtable（哈希表）：当ziplist不能满足要求时，会使用hashtable。
hash-max-ziplist-entries 512
# 配合hash-max-ziplist-entries使用，默认值64字节
hash-max-ziplist-value 64
# list使用了quicklist作为底层实现，不再使用linkedlist和ziplist
# quicklist是ziplist组成的双向链表，它的每个节点都是一个 ziplist。
# quicklist是结合了linkedlist和ziplist优点的产物:
# 	linkedlist：便于进行增删改操作但是内存占用较大
# 	ziplist：内存占用较少，但每次修改都可能触发realloc和memcopy, 且可能导致级联更新。因此修改操作
#          的效率较低，在 ziplist 较长时这个问题更加突出,于是每个节点上 ziplist 的大小变成了一个
#          需要折中的难题:
# ziplist越小，quicklist 越接近于linkedlist。此时存储效率下降，但是修改操作的效率较高。
# ziplist越大，quicklist 越接近于ziplist。此时存储效率上升，但是修改操作的效率降低。
# redis 根据 list-max-ziplist-size 配置项来决定节点上 ziplist 的长度。
#	当为正值时：表示按照数据项个数来限定每个quicklist节点上的 ziplist长度。比如，当这个参
#   		数配置成5的时候，表示每个 quicklist 节点的ziplist 最多包含5个数据项。
#	当为负值时：表示按照占用字节数来限定每个节点上的 ziplist 长度。这时，它只能取 -1 到 -5
#			-5: 每个节点上的 ziplist 大小不能超过64 KB
#			-4: 每个节点上的 ziplist 大小不能超过 32 KB。
#			-3: 每个节点上的 ziplist 大小不能超过16 Kb。
#			-2: 每个节点上的 ziplist 大小不能超过8 Kb。这是默认设置。
#			-1: 每个节点上的 ziplist 大小不能超过4 Kb。
list-max-ziplist-size -2
# 表示list中quicklist两端不被压缩的节点个数，对于一个很长的列表而言，最常使用的是其两端的数据，
# 中间数据被访问的概率较低。因此，quicklist允许将中间的节点使用LZF算法进行压缩以节省内存。
#	0：表示都不压缩，这是默认值。
#	1: 表示quicklist两端各有1个节点不压缩，中间的节点压缩。
#	2: 表示quicklist两端各有2个节点不压缩，中间的节点压缩。
#	以此类推...
list-compress-depth 0
# set中的元素均为整数且元素数小于该参数时（默认512），采用inset存储集合。当插入非整数元素或元素数
# 超过阈值后intset会使用为hashtable进行存储。
set-max-intset-entries 512
# zset在满足以下条件时会采用ziplist编码
# 	value字节数<=zset-max-ziplist-value && 元素数<= zset-max-ziplist-entries
zset-max-ziplist-entries 128
# 配合zset-max-ziplist-entries使用，默认64字节
zset-max-ziplist-value 64
# hyperLoglog的编码设置
#		value字节数<=hll-sparse-max-bytes时使用sparse（稀疏数据结构）
# 		value字节数 >hll-sparse-max-bytes时使用dense（稠密的数据结构）
# 建议3000字节，如果对cpu要求不高，对空间要求较高，建议设置10000字节左右
hll-sparse-max-bytes 3000
# Redis Streams是一些由基数树（Radix Tree）连接在一起的节点经过delta压缩后构成的，
# 这些节点与Stream中的消息条目（Stream Entry）并非一一对应，而是每个节点中都存储着若干
# Stream条目，因此这些节点也被称为宏节点或大节点。这样的数据结构为访问随机元素、访问指定
# 范围内的多个元素、实现定长Stream等操作提供了高效的支持，同时具有极高的内存利用率。
# 宏节点中可储存的Stream条目数可通过stream-node-max-entries自定义，而单个宏节点占用的
# 内存大小则可通过stream-node-max-bytes来限制。
#		stream-node-max-entries：默认值为100，即每个宏节点储存100个Stream条目。
#			取值范围为0~999999999999999，0表示没有限制
#			当一个宏节点中存储的Stream条目数达到上限时，新添加的条目将储存到新的宏节点中
#		stream-node-max-bytes：单位为字节数，默认4096，即每个宏节点占用的内存容量上限
#			取值范围：0~999999999999999，0表示无限制。	
stream-node-max-bytes 4096
# 调整定长消息队列的队列长度误差值
# 例如让队列中只保存5000条消息，1条都不能多，代价会很大，为了尽可能地提高内存利用率，Stream
# 数据其实是由基数树中的多个宏节点组成的，每次删除1条消息都需要检索相应的宏节点，找到目标消息，
# 将其标记为已删除。在高吞吐的Redis服务中，消息的更新频率可能非常高，频繁进行这样的操作将大大
# 提高性能压力。因此，推荐的做法是使用波浪线（~）限定队列的大致长度，例如：
#
#   // 在cappedstream的field1中添加一个新的值value5001，队列长度约为5000
#	XADD cappedstream MAXLEN ~ 5000 * field value1 
#
# 这样一来，队列的实际长度可能是5000、5050或者5060等，Redis根据stream-node-max-entries
# 自动计算得出这个近似值,如果希望误差值较小，可以适当地将参数值变小。
stream-node-max-entries 100
# 是否重置Hash表
#	设置为yes：redis将每100毫秒使用1毫秒CPU时间来对redis的hash表重新hash，可降低内存的使用
#   设置为no：有较为严格的实时性需求，不能接受延迟
activerehashing yes
# Redis 缓存保护机制：
#	1. 大小限制，当某一客户端缓冲区超过设定值后直接关闭连接
#	2. 持续时间限制，某一客户端缓冲区持续一段时间占用过大空间时关闭连接
# 以下为默认设置：
# 	normal（普通客户端）：0不限制，由于采用阻塞式的消息应答模式，通常不会导致输出缓冲区的膨胀
client-output-buffer-limit normal 0 0 0
# 	replica（slave）：
#		大小限制：256mb，当输出缓冲区超过256mb时，会关闭连接
#		持续性限制：当客户端缓冲区大小持续60秒超过64mb，则关闭客户端连接
client-output-buffer-limit replica 256mb 64mb 60
# 	Pub/Sub客户端（发布/订阅）：
#		大小限制：32mb，当输出缓冲区超过32mb时，会关闭连接
#		持续性限制：当客户端缓冲区大小持续60秒超过8mb，则关闭客户端连接；
client-output-buffer-limit pubsub 32mb 8mb 60
# 后台处理频率，单位是1s/hz，默认值1s/10hz，包括过期检测，超时检测等等，hz越大越频繁
hz 10
# 是否根据客户端连接数动态调整hz的值，默认打开 
dynamic-hz yes
# 设置为yes（默认）：
#	重写AOF文件时，按32mb的数据量进行递增冲洗操作（fsync），防止单次刷盘数据过多造成硬盘阻塞
aof-rewrite-incremental-fsync yes
# 设置为yes（默认）：
#	rdb快照持久化时，按32mb的数据量进行递增冲洗操作（fsync），防止单次刷盘数据过多造成硬盘阻塞
rdb-save-incremental-fsync yes
# 设置为yes（默认）：
#	启用用于清除的Jemalloc后台线程
jemalloc-bg-thread yes
```

### 7、主库配置

```bash
vim /data/redis-6.2.8/redis.conf
```

修改配置文件如下

```ini
bind 0.0.0.0
daemonize yes
requirepass 123456
masterauth 123456
tcp-backlog 127
logfile "/data/redis-6.2.8/redis.log"
```

### 8、从库配置

```ini
bind 0.0.0.0
daemonize yes
requirepass 123456
replicaof 192.168.230.200 6379
masterauth 123456
tcp-backlog 127
logfile "/data/redis-6.2.8/redis.log"
```

### 9、redis-sentinel配置详解

```ini
# 监听端口，默认是26379
port 26379
daemonize no
pidfile /var/run/redis-sentinel.pid
logfile ""
dir /tmp
# 配置sentinel监控，格式如下：
# 	sentinel monitor <master-name> <ip> <redis-port> <quorum>
#
# 监听地址为ip:port的一个master
# master-name：可以自定义，只能包含英文字母、数字、“.-_”这类字符
# quorum：是一个数字，指明有多少个sentinel认为master失效时，才算真正失效
sentinel monitor mymaster 192.168.230.200 6379 2
# 设置连接master和slave时的密码
# sentinel不能为master和slave设置不同的密码，因此master和slave的密码应该相同
sentinel auth-pass mymaster 123456
# 指定失效时间，单位是毫秒，默认为30秒
sentinel down-after-milliseconds mymaster 30000
acllog-max-len 128
# 它规定了每次向新的主节点发起复制操作的从节点个数，默认值是1
# 	值越小，从节点完成复制所需的时间越长
# 	值越大，从节点完成复制所需的时间越短，但是对主节点的网络负载、硬盘负载造成的压力也越大
sentinel parallel-syncs mymaster 1
# 故障转移超时时间（单位毫秒，默认180秒），但实际上它作用于故障转移的各个阶段： 
#      1.选出合适从节点
#      2.晋升选出的从节点为主节点
#      3.命令其余从节点复制新的主节点。  
#      4.等待原主节点恢复后命令它去复制新的主节点
# 上面任意一个阶段超过超时时间，则故障转移失败，在该时间内如果故障转移没有成功，
# 则会再发起一次故障转移，下次再对该主节点做故障转移的起始时间是failover-timeout的2倍
# 所以说failover-timeout决定了两次切换之间的最短等待时间，如果对于切换成功率要求较高
# 可以适当缩短 failover-timeout 到秒级保证切换成功
sentinel failover-timeout mymaster 180000
# 默认为yes，禁止命令行runtime修改client-reconfig-script
# 设置为no，允许命令行runtime修改client-reconfig-script
sentinel deny-scripts-reconfig yes
# 是否启用解析主机名，默认no
SENTINEL resolve-hostnames no
# 启用解析主机名时，Sentinel仍使用IP地址向用户、配置文件等发布实例
# 如果需要在公布时保留主机名，请设置为yes
SENTINEL announce-hostnames no
```

### 10、编辑sentinel配置文件

```bash
vim /data/redis-6.2.8/sentinel.conf
```

修改以下配置

```ini
daemonize yes
dir ./
logfile "/data/redis-6.2.8/sentinel.log"
sentinel monitor mymaster 192.168.230.200 6379 2
sentinel auth-pass mymaster 123456
tcp-backlog 127
sentinel down-after-milliseconds mymaster 5000
```

### 11、将Redis添加为系统服务

新建配置文件

```bash
vim /usr/lib/systemd/system/redis.service
```

输入以下内容

```bash
[Unit]
Description=redis

[Service]
Type=forking
User=root
ExecStart=/data/redis-6.2.8/bin/redis-server /data/redis-6.2.8/redis.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

让服务生效

```bash
systemctl daemon-reload
```

启动服务

```bash
systemctl start redis
```

设置开启启动

```bash
systemctl enable redis
```

查看启动日志

```bash
tail -f /data/redis-6.2.8/redis.log
```

### 12、将Redis-sentinel添加为系统服务

新建配置文件

```bash
vim /usr/lib/systemd/system/redis-sentinel.service
```

输入以下内容

```bash
[Unit]
Description=redis-sentinel

[Service]
Type=forking
User=root
ExecStart=/data/redis-6.2.8/bin/redis-sentinel /data/redis-6.2.8/sentinel.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

让服务生效

```bash
systemctl daemon-reload
```

启动服务

```bash
systemctl start redis-sentinel
```

设置开启启动

```bash
systemctl enable redis-sentinel
```

查看启动日志

```bash
tail -f /data/redis-6.2.8/sentinel.log
```

