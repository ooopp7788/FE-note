### 模块变量
- timerQueue: 以 startTime 排序的最小堆, 表示延时任务队列
- taskQueue: 以 expirationTime 排序的最小堆, 表示正在或即将执行的任务队列

### unstable_scheduleCallback(priorityLevel, callback, options)
#### 参数 & 变量
- priorityLevel: 表示任务优先级, 会用于计算 timeout
- callback: 任务执行函数
- options:
    - delay: 计算开始时间时会加上延时
    - timeoout: 传入 timeout 时, 不再用 priorityLevel 计算 timeout, timeout 会用于计算 expirationTime
- startTime: 任务开始时间, startTime = timeout + currentTime
- expirationTime: 任务过期时间
> priorityLevel 和 delay、timeout 配置都会影响 expirationTime 的计算, 也就能从更细粒度控制任务执行顺序

#### 执行过程
- 按参数计算出 startTime, expirationTime, 创建 newTask
- delay > 0, 表明是延迟任务, 直接加入到 timerQueue, 等到 delay 时间到了再执行
    - taskQueue 无任务时, 设置 timer, 到期时顺序执行 timerQueue
- 非延时任务, 加入到 taskQueue, 如果没有任务在执行, 初始执行一次requestHostCallback(flushWork)

### flushWork
调用 workLoop 按顺序执行 taskQueue

### workLoop(hasTimeRemaining, initialTime) 执行过程
- 每次进入时, advanceTimers(currentTime) 检查 timerQueue 是否有过期任务, 将过期任务加入到 taskQueue
- while 循环判断依次执行 taskQueue
    - 从当前 taskQueue 取出下一个要执行的 currentTask, 仅执行超时的任务, 且要求当前帧有剩余时间
    - currentTask = peek(taskQueue), 进入下个循环判断
- 如果 currentTask===null 任务执行完毕, 会调用 requestHostTimeout 从 timerQueue 中取出最近的 task, 设置 timer, timer 到期后继续执行任务

### 其他 api 介绍

### 总结
- Scheduler 提供 unstable_scheduleCallback 用于添加任务, 根据不同任务类型, 存储 task 至 taskQueue or timerQueue
- taskQueue 执行时, 会判断 task 是否过期 && 当前帧是否有剩余时间, 会在帧剩余时间还有的情况下, 尽可能多的执行已过期的 task
- timerQueue 表示 delay task 队列, taskQueue 执行完毕时, 设置 timer 保障 timerQeue 按时执行