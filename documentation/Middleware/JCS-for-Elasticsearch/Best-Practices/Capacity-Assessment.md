## 规格容量评估

云搜索Elasticsearch是分布式多节点形式的集群，在使用之前，需要先进行资源容量的评估，我们根据一些实际经验，提供了相对通用的配置建议，可以供您参考。在您的业务规模增大时，可以在线实时弹性扩容集群以满足使用需求。

### 存储容量评估

京东云搜索Elasticsearch服务存储空间大小影响因素如下：

- 副本数量：副本有利于增加数据的可靠性，但同时会增加存储成本。默认和建议的副本数量为 1。</br>
- 索引开销：除原始数据外，ES 需要存储索引等数据，一般情况下数据膨胀为10%。</br>
- 内部任务开销：ES 自身会占用约 20% 的磁盘空间用于段合并、日志等，因此要预留20%的此部分空间。</br>
- 操作系统预留：操作系统也会占用5%的磁盘空间，用于关键流程处理、防止磁盘碎片化问题等。</br>

因此，ES的实际空间可通过下面公式估算：

```
实际空间 = 源数据 * (1 + 副本数量) * (1 + 索引开销) / （1-操作系统预留）/(1 - 内部任务开销) 
        = 源数据 * (1 + 副本数量) * 1.45
        = 源数据 *2.9
```

为了保证服务能稳定运行，建议在上述评估的基础上至少预留50%的存储空间，因此建议申请的存储容量为：

```	
存储容量 = 源数据 * (1 + 副本数量) * 1.45 * （1 + 0.5）
        = 源数据 * 4.35
```

### 实例规格评估

实例规格评估节点规格和节点数量两方面，选择实例规格的建议如下：

- 我们建议您至少选择 3 个节点，保证 ES 实例具有较高的节点故障容错能力。</br>
- 如果您有非常大的存储容量需求，我们建议您选择高规格节点，避免选择大量低规格节点，有助于提升集群性能和稳定性。</br>
- 在完成实例规格类型的选择后，可以通过观察 CPU 使用率、集群查询QPS、集群写入QPS等监控指标，进一步确认实例规格是否合适。</br>

### 分片数量评估

每个 ES 索引被分为多个分片，索引分片的数量会影响集群稳定性和性能，且通常确定后无法轻松更改，需要提前规划：

- 建议单个分片大小在小规格节点下不超过30GB，在高规格节点下不超过50GB。分片过大会导致ES故障的恢复速度慢，分片过小会导致内存不足等问题。</br>
- 分片数量要尽量匹配节点数，分片数可以等于节点数，也可以是节点数的整数倍，方便分片在所有数据节点均匀分布。</br>