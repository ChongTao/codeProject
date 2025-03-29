# 1 jvm调优

JVM 调优的核心目标是 平衡吞吐量、延迟和内存占用，具体需结合应用场景（如高并发 Web 服务、大数据计算等）。

## 1.1 调优前的准备工作
明确目标：高吞吐量（如离线计算）？低延迟（如实时交易）？还是减少内存占用？

命令行工具：jstat、jmap、jstack、jcmd（例：jstat -gcutil <pid> 1000 监控 GC 状态）。

可视化工具：VisualVM、JConsole、MAT（内存分析）、Arthas（在线诊断）。

日志分析：开启 GC 日志（-Xloggc:/path/gc.log -XX:+PrintGCDetails）。

## 1.2 内存区域调优
JVM 内存结构如下（以 HotSpot 为例）：
堆内存（Heap）
├── 新生代（Young Generation）
│   ├── Eden 区
│   ├── Survivor 区（From）
│   └── Survivor 区（To）
└── 老年代（Old Generation）

非堆内存（Non-Heap）
├── 元空间（Metaspace）
└── 直接内存（Direct Memory）

## 1.3 垃圾回收器选择与调优

不同垃圾回收器的适用场景：
- Serial：单线程，STW 时间长，客户端小应用，资源受限环境
- Parallel Scavenge：多线程，高吞吐量	后台计算型应用
- CMS：并发收集，低延迟	Web 服务（已废弃，Java 14+）
- G1：分区域收集，可预测停顿	大内存、低延迟要求
- ZGC：超低停顿（<10ms），大堆	实时交易系统（JDK 15+）
- Shenandoah：低停顿，并发压缩	大堆、高响应场景。