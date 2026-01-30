`kubectl rollout` 是 Kubernetes 中非常核心的命令，专门用于管理资源的**更新过程**（通常针对 Deployment, StatefulSet 和 DaemonSet）。

### 常用子命令拆解

我们将语法拆解为：`kubectl rollout [sub-command] [TYPE]/[NAME]`

| **子命令**     | **功能描述**           | **语法示例**                                |
| ----------- | ------------------ | --------------------------------------- |
| **status**  | 查看更新的实时状态          | `kubectl rollout status deploy/my-app`  |
| **history** | 查看修订版本（Revision）记录 | `kubectl rollout history deploy/my-app` |
| **undo**    | 回滚到上一个版本（或指定版本）    | `kubectl rollout undo deploy/my-app`    |
| **pause**   | 暂停当前的更新流程          | `kubectl rollout pause deploy/my-app`   |
| **resume**  | 恢复暂停中的更新           | `kubectl rollout resume deploy/my-app`  |
| **restart** | 强制触发一次滚动更新（重启服务）   | `kubectl rollout restart deploy/my-app` |

### 实验

```sh
# 第一步：创建一个基础部署
~ > kubectl create deployment demo-app --image=nginx:1.21
deployment.apps/demo-app created
~ > kubectl rollout history deployment demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
1         <none>

# 第二步：更新镜像并记录原因 (关键步骤)
# 使用 `--record` 参数来记录我们做了什么。
~ > kubectl set image deployment/demo-app nginx=nginx:1.23 --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/demo-app image updated
~ > kubectl rollout history deployment demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true

# 第三步：模拟一个“错误”的更新
# 假设我们不小心输入了一个不存在的镜像版本，这会导致滚动更新卡住。
~ > kubectl set image deployment/demo-app nginx=nginx:999.999 --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/demo-app image updated
~ > kubectl rollout status deployment demo-app
Waiting for deployment "demo-app" rollout to finish: 1 old replicas are pending termination...
# 按 `Ctrl+C` 退出，
^C~ >
# 用 `kubectl get pods` 看到 `ImagePullBackOff` 错误
~ > kubectl get pod
NAME                        READY   STATUS
demo-app-5ddf47b556-8r5jg   0/1     ImagePullBackOff
demo-app-d6496ff66-z2klk    1/1     Running

# 第四步：紧急回滚
# 发现错误后，我们需要迅速回到上一个正常的版本（Revision 2）。
~ > kubectl rollout undo deployment demo-app
deployment.apps/demo-app rolled back
~ > kubectl rollout history deployment demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
1         <none>
3         kubectl set image deployment/demo-app nginx=nginx:999.999 --record=true
4         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true

# 第五步：强制重启
# 假设程序运行异常，尝试重启
# 即使配置没变，重启也会导致新的 REVISION 产生
~ > kubectl rollout restart deployment demo-app
deployment.apps/demo-app restarted
~ > kubectl rollout history deployment demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
1         <none>
3         kubectl set image deployment/demo-app nginx=nginx:999.999 --record=true
4         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true
5         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true

# 第六步：直接撤销到指定的 revision
~ > kubectl rollout undo deployment/demo-app --to-revision=1
deployment.apps/demo-app rolled back
~ > kubectl rollout history deployment/demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
3         kubectl set image deployment/demo-app nginx=nginx:999.999 --record=true
4         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true
5         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true
6         <none>

# 第七步：手动“修复”历史记录
~ > kubectl annotate deployment demo-app kubernetes.io/change-cause="回滚到最初的稳定版本 1.21"
deployment.apps/demo-app annotated
~ > kubectl rollout history deployment/demo-app
deployment.apps/demo-app
REVISION  CHANGE-CAUSE
3         kubectl set image deployment/demo-app nginx=nginx:999.999 --record=true
4         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true
5         kubectl set image deployment/demo-app nginx=nginx:1.23 --record=true
6         回滚到最初的稳定版本 1.21
```

### `rollout` 的逻辑结构

`rollout` 的本质是对**控制器（Controller）**版本记录的操作。当你执行 `set image` 时，系统会自动创建一个新的 `Revision`（修订版），而 `rollout` 系列命令就是用来管理这些修订版的“时光机”。

### Revision 产生原理

除了 `set image` 外，只要涉及到「模板元数据」发生变化的操作，都会产生新的历史记录。

由于重启命令 `rollout restart` 本质上是往 Pod 模板里注入了一个“重启时间戳”的注解，因此也将产生新的历史。

| **修改内容**                     | **是否产生** |
| -------------------------------- | ------------ |
| Container Image (镜像)           | **是**       |
| Environment Variables (环境变量) | **是**       |
| Resource Limits (资源限制)       | **是**       |
| ConfigMap/Secret 引用            | **是**       |
| Replicas (副本数量)              | ❌           |
| Service / Ingress 配置           | ❌           |
