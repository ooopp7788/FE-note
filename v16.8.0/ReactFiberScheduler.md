### 闭包变量
- isWorking: 是否处于 rendering 或者 commiting 阶段
- isCommiting: 是否处于 commiting 状态
- isRendering: 是否处于 rendering 状态
- nextRenderExpirationTime: 下次 render 任务的 experationTime
- nextUnitOfWork: 下个任务 fiber 节点
- callbackExpirationTime: 调度中 callback 的过期时间, 表示有 callback 在调度中
- currentRendererTime: 

### scheduleWork
- `scheduleWorkToRoot`
- isWorking: false 且 experationTime 大于下次渲染的 nextRenderExpirationTime
- 表示本次任务是优先级更高的任务, 此时会调用 `resetStack` 中断当前任务
- <?> !isWorking || isCommiting 阶段时, `requestWork`

#### resetStack
- 移除下个任务节点 nextUnitOfWork 前的所有节点, 并清空任务 nextUnitOfWork = null

#### scheduleWorkToRoot
- 从当前 fiber 开始到 root 节点, 更新 exprationTime
- 更新时间时, 新的时间比当前时间大时更新
- exprationTime 越大优先级越高

#### requestWork
- <?> `addRootToSchedule`
- expirationTime === Sync, 同步任务, `performSyncWork`
- 否则 `scheduleCallbackWithExpirationTime`

##### addRootToSchedule

##### scheduleCallbackWithExpirationTime(root, exprationTime)
- callbackExpirationTime 表示已经在调度中的旧 callback 过期时间, 如果存在时
    - 比较 callbackExpirationTime 和 exprationTime
    - callbackExpirationTime 小, 表示旧 callback 优先级低, 取消旧 callback
    - callbackExpirationTime 大, 直接返回
- 更新 callbackExpirationTime = exprationTime
- callbackID = scheduleDeferredCallback(performAsyncWork, {timeout}): 交给调度器执行, 更新 callbackID, 用于取消任务

##### scheduleDeferredCallback & cancelDeferredCallback
Schedule 调度器提供的 api, 用于任务调度
Schedule 是时间切片和任务调度


#### performWork(minExpirationTime, isYieldy)
- isYieldy: 是否需要判断剩余时间, 同步任务不判断, 异步任务需要 check 剩余时间




Work

Commit

Render

Fiber

Root

Update

Effect