# 雪花算法

## 什么是雪花算法

### 雪花算法（Snowflake Algorithm）是一种用于生成全局唯一标识符（Globally Unique Identifiers，简称 GUID）的算法

它最初由 Twitter 开发，用于在分布式系统中生成唯一的 ID。雪花算法的设计目标是在大规模分布式系统中高效生成 ID，并保证生成的 ID 是递增有序的

#### 雪花算法生成的 ID 是 64 位二进制的整数，可以分解成以下几个部分

1. **时间戳（Timestamp）：** 占用 42 位，记录生成 ID 的时间戳，精确到毫秒级别。由于使用了 42 位，所以雪花算法可以支持约 69 年的时间戳。
2. **节点 ID（Worker ID）：** 占用 10 位，用于标识不同的节点或机器。在分布式系统中，每个节点或机器需要有唯一的节点 ID。
3. **序列号（Sequence Number）：** 占用 12 位，表示在同一毫秒内生成的 ID 的序列号。如果同一毫秒内生成的 ID 超过了 4096 个（2 的 12 次方），则会等待下一毫秒再生成。

通过将时间戳、节点 ID 和序列号组合在一起，雪花算法可以生成全局唯一的 ID，且在一定程度上保持了递增有序性，这对于分布式系统的排序和索引非常有用。

需要注意的是，雪花算法对节点 ID 的分配和生成 ID 的调用需要进行合理的管理和控制，以确保生成的 ID 是唯一且正确的。

## 如何实现雪花算法呢

要实现雪花算法，可以按照以下步骤进行

### 定义相关参数

- 时间戳位数：41 位（+ 1 个前零位）
- 节点 ID 位数：10 位
- 序列号位数：12 位

### 初始化相关变量

- 上次生成 ID 的时间戳 `lastTimestamp` 初始化为 0
- 序列号 `sequence` 初始化为 0

### 实现生成 ID 的函数 generateID()

1. 获取当前时间戳 `currentTimestamp`

2. 如果当前时间戳小于上次生成 ID 的时间戳 `lastTimestamp`，说明时钟回拨（clock drift）发生，需要等待时钟追赶到上次时间戳，避免生成重复的 ID。在这种情况下，可以选择等待一段时间或抛出异常，具体处理方式根据需求而定。

3. 如果当前时间戳等于上次生成 ID 的时间戳 `lastTimestamp`，表示在同一毫秒内生成 ID，需要增加序列号 `sequence`。

4. 判断序列号 `sequence` 是否已达到最大值（2 的 12 次方减 1），如果是，则等待下一毫秒再生成 ID，将序列号 `sequence` 重置为 0。

5. 如果序列号 `sequence` 未达到最大值，递增序列号 `sequence`。

6. 如果当前时间戳大于上次生成 ID 的时间戳 `lastTimestamp`，表示进入下一毫秒，将序列号 `sequence` 重置为 0。

7. 更新上次生成 ID 的时间戳 `lastTimestamp` 为当前时间戳 `currentTimestamp`。

### 将各部分的值进行位移和逻辑运算，组合成 64 位的 ID

- 时间戳部分左移 22 位（64 - 42）：`timestampShifted = currentTimestamp << 22`
- 节点 ID 部分左移 12 位（64 - 42 - 10）：`workerIdShifted = workerId << 12`
- 组合时间戳、节点 ID 和序列号：`snowflakeId = timestampShifted | workerIdShifted | sequence`

返回生成的唯一 ID `snowflakeId`。

需要注意的是，在实际应用中，节点 ID 可以根据具体情况分配，可以使用机器的 MAC 地址、IP 地址或其他唯一标识符来标识不同的节点。另外，时钟回拨问题需要根据具体的编程语言和环境来处理，确保生成的 ID 是唯一且正确的。

```javascript
// 雪花算法实现
class Snowflake {
  constructor(workerId) {
    this.workerIdBits = 10;
    this.sequenceBits = 12;
    this.maxWorkerId = -1 ^ (-1 << this.workerIdBits);
    this.sequenceMask = -1 ^ (-1 << this.sequenceBits);
    this.workerId = workerId;

    this.lastTimestamp = -1;
    this.sequence = 0;
  }

  generateID() {
    let timestamp = this.currentTimestamp();

    if (timestamp < this.lastTimestamp) {
      throw new Error("Clock moved backwards. Refusing to generate ID.");
    }

    if (timestamp === this.lastTimestamp) {
      this.sequence = (this.sequence + 1) & this.sequenceMask;

      if (this.sequence === 0) {
        timestamp = this.nextMillis(this.lastTimestamp);
      }
    } else {
      this.sequence = 0;
    }

    this.lastTimestamp = timestamp;

    const id =
      (BigInt(timestamp) << BigInt(this.workerIdBits + this.sequenceBits)) |
      (BigInt(this.workerId) << BigInt(this.sequenceBits)) |
      BigInt(this.sequence);

    return id.toString();
  }

  currentTimestamp() {
    return Date.now();
  }

  nextMillis(lastTimestamp) {
    let timestamp = this.currentTimestamp();
    while (timestamp <= lastTimestamp) {
      timestamp = this.currentTimestamp();
    }
    return timestamp;
  }
}

// 使用示例
const workerId = 1; // 假设节点ID为1
const snowflake = new Snowflake(workerId);

// 生成ID
const id = snowflake.generateID();
console.log(id);
```

在上述代码中，我们定义了一个 `Snowflake` 类，构造函数中传入节点 ID `workerId`。然后，通过调用 `generateID` 方法即可生成唯一的 ID。注意，这里使用 `BigInt` 类型来处理大整数，确保在 JavaScript 中能够正确处理 64 位的 ID。

## 雪花算法的优缺点

**优点：**

- 生成单调自增的唯一 ID，在 InnoDB 的 B+树表中顺序插入不会造成页的分裂，性能高。（UUID 的话每个 ID 是随机的，大量的随机 IO 效率不但低，还会使 InnoDB 页造成分裂和合并，使得插入效率低）
- 生成 64 位 ID，只占用 8 个字节节省存储空间。

**缺点：**

- 每台数据库的本地时间都要设置相同，否则会导致全局不递增
- 如果时钟回拨，会产生重复 ID。

**时钟回拨的解决方案：**

存储每一毫秒产生的最后一个 ID 的序列值，回拨到当前毫秒从序列值 +1 开始继续产生 ID 即可。

以上代码和解释可以帮助理解和实现雪花算法。实际使用时，可能需要根据具体情况进行适当调整和优化。
