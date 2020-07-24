### 闭包变量
- isWorking: 是否处于 rendering 或者 commiting 阶段
- isCommiting: 是否处于 commiting 状态
- isRendering: 是否处于 rendering 状态
- nextRenderExpirationTime: 下次 render 任务的 experationTime
- nextUnitOfWork: 下个任务 fiber 节点
- callbackExpirationTime: 调度中 callback 的过期时间, 表示有 callback 在调度中
- currentRendererTime: 当前渲染时间, 由 now() 当前时间转换得来
- firstScheduledRoot: root 链表
- nextFlushedRoot: 下一个执行的 root
- nextFlushedExpirationTime: 下一个节点的过期时间

- nextEffect: 表示下一个有副作用的 fiber 节点, 记录了所有有副作用的 fiber 节点

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
按 firstScheduledRoot (root节点的链表) experationTime 优先级顺序执行 `performWorkOnRoot`
- findHighestPriorityRoot()
- isYieldy: 是否需要判断剩余时间, 同步任务不判断, 异步任务需要 check 剩余时间

##### findHighestPriorityRoot
在 firstScheduledRoot root链表中找到最高优先级的 root 节点, 作为一个执行的 root 节点
会更新 nextFlushedRoot 和 nextFlushedExpirationTime

#### performWorkOnRoot(nextFlushedRoot, nextFlushedExpirationTime, false)
completeRoot or renderRoot
- completeRoot: commitRoot
- renderRoot

#### commitRoot(root: FiberRoot, finishedWork: Fiber)
- finishedWork.effectTag > PerformedWork 时, 表示当前 fiber 有副作用
- 插入到 finishedWork.lastEffect 副作用链表的尾部
- finishedWork.firstEffect: 所有有副作用的 fiber 的链表
- `commitBeforeMutationLifecycles`: 遍历 nextEffect fiber 链表, 根据 fiber.effectTag & Snapshot 位运算结果判断是否执行 `commitBeforeMutationLifeCycles`
- `commitAllHostEffects`: 遍历 nextEffect fiber 链表, 根据 fiber.effectTag & ContentReset 位运算结果判断是否执行 `commitResetTextContent`
- `commitAllLifeCycles`: 根据位运算执行 `commitLifeCycles` 和 `commitAttachRef`
- `onCommitRoot`:
- `onCommit`:

> 总结: commitRoot 会在 fiber 节点有副作用时, 将 fiber 节点插入到 finishedWork.lastEffect 链表尾部, 然后遍历 finishedWork.firstEffect, 根据 effectTag & XXX 的位运算结果执行各种 commitXXX 操作

##### commitBeforeMutationLifeCycles
- 实际是执行每个 fiber 的 fiber.updateQueue (链表) 所有副作用, 执行时使用 fiber.alternate 作为 fiber 主体, 保证出错可回滚

##### commitResetTextContent

##### commitLifeCycles

##### commitAttachRef


##### onCommitRoot

##### onCommit



Work

Commit

Render

Fiber

Root

Update

Effect