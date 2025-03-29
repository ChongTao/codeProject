# 1 jvm调优

JVM 调优的核心目标是 平衡吞吐量、延迟和内存占用，具体需结合应用场景（如高并发 Web 服务、大数据计算等）。以下是调优的核心步骤和关键参数说明：

一、调优前的准备工作
明确目标

高吞吐量（如离线计算）？低延迟（如实时交易）？还是减少内存占用？

监控工具

命令行工具：jstat、jmap、jstack、jcmd（例：jstat -gcutil <pid> 1000 监控 GC 状态）。

可视化工具：VisualVM、JConsole、MAT（内存分析）、Arthas（在线诊断）。

日志分析：开启 GC 日志（-Xloggc:/path/gc.log -XX:+PrintGCDetails）。

基准测试

使用 JMH 或业务压测工具，记录调优前后的性能对比。

二、内存区域调优
JVM 内存结构如下（以 HotSpot 为例）：

复制
堆内存（Heap）
├── 新生代（Young Generation）
│   ├── Eden 区
│   ├── Survivor 区（From）
│   └── Survivor 区（To）
└── 老年代（Old Generation）

非堆内存（Non-Heap）
├── 元空间（Metaspace）
└── 直接内存（Direct Memory）
1. 堆内存分配
关键参数：

bash
复制
-Xms512m  # 初始堆大小（建议与 -Xmx 相同，避免动态扩容抖动）
-Xmx4g     # 最大堆大小（不超过物理内存的 70%）
-Xmn2g     # 新生代大小（老年代 = -Xmx - -Xmn）
-XX:NewRatio=2  # 老年代/新生代比例（-Xmn 优先级更高）
-XX:SurvivorRatio=8  # Eden/Survivor 比例（默认 8:1:1）
调优建议：

避免频繁 Full GC：老年代应能容纳应用 长期存活对象。

减少 Young GC 频率：适当增加 Eden 区大小（但过大会导致单次 GC 停顿时间增加）。

2. 元空间（Metaspace）
关键参数：

bash
复制
-XX:MetaspaceSize=128m      # 初始大小
-XX:MaxMetaspaceSize=512m   # 最大大小（默认无限制）
调优场景：

动态生成类（如 CGLIB 代理）过多时，需限制 MaxMetaspaceSize 防止内存泄漏。

3. 直接内存（Direct Memory）
关键参数：

bash
复制
-XX:MaxDirectMemorySize=1g  # NIO Direct Buffer 的最大大小
监控工具：

jcmd <pid> VM.native_memory 查看 Native Memory 使用情况。

三、垃圾回收器选择与调优
不同垃圾回收器的适用场景：

回收器	特点	适用场景
Serial	单线程，STW 时间长	客户端小应用，资源受限环境
Parallel Scavenge	多线程，高吞吐量	后台计算型应用
CMS	并发收集，低延迟	Web 服务（已废弃，Java 14+）
G1	分区域收集，可预测停顿	大内存、低延迟要求
ZGC	超低停顿（<10ms），大堆	实时交易系统（JDK 15+）
Shenandoah	低停顿，并发压缩	大堆、高响应场景
1. G1 调优示例
bash
复制
-XX:+UseG1GC 
-XX:MaxGCPauseMillis=200  # 目标最大停顿时间（JVM 尽力实现）
-XX:InitiatingHeapOccupancyPercent=45  # 老年代占用阈值触发 Mixed GC
-XX:G1NewSizePercent=5    # 新生代最小占比
-XX:G1MaxNewSizePercent=60 # 新生代最大占比
2. ZGC 调优示例
bash
复制
-XX:+UseZGC 
-Xmx16g 
-XX:ConcGCThreads=4       # 并发 GC 线程数（默认≈总CPU的1/8）
3. 通用 GC 参数
bash
复制
-XX:+PrintGCDetails       # 打印详细 GC 日志
-XX:+PrintGCDateStamps    # 记录 GC 时间
-XX:+UseGCLogFileRotation # 开启日志轮转
-XX:NumberOfGCLogFiles=5 
-XX:GCLogFileSize=10M
四、常见问题与调优策略
1. Young GC 频繁
现象：每秒多次 Young GC，Eden 区过小。

解决：增大 -Xmn（新生代）或 -XX:SurvivorRatio（增大 Eden）。

2. Full GC 频繁
可能原因：

内存泄漏：老年代对象无法回收（用 MAT 分析堆转储）。

元空间不足：调整 -XX:MaxMetaspaceSize。

大对象分配：避免过大的数组或未分页查询（调整 -XX:PretenureSizeThreshold）。

3. GC 停顿时间过长
策略：

切换低延迟回收器（如 G1、ZGC）。

减少存活对象数量（优化代码，减少内存分配）。

4. 高 CPU 占用
排查：

top -Hp <pid> 找到高 CPU 线程，jstack <pid> 转换线程 ID 查看堆栈。

可能原因：频繁 GC、代码死循环、锁竞争。

五、调优禁忌与最佳实践
避免过早优化：先通过 Profiling（如 JProfiler）找到瓶颈。

逐步调整：每次只修改一个参数，观察效果。

监控告警：生产环境配置 GC 日志监控和堆内存报警。

代码优化：减少对象分配（如重用对象、使用基本类型）。

总结
JVM 调优需要结合 监控数据 + 业务场景 + 垃圾回收器特性，核心步骤：

设定性能目标（吞吐量、延迟）。

分析 GC 日志和内存使用。

调整堆内存分区和回收器参数。

验证优化效果并持续监控。

最终目标：在有限的硬件资源下，让 JVM 以最稳定的状态支撑业务运行。

开启新对话
