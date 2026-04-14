# 本次优化准确性与 Gap 收敛分析报告

**日期：** 2026-04-14  
**评估对象：** `keycloak-docs` 当前工作区相对上一轮分析后的新增优化  
**对标基线：**
- Red Hat Application Foundations 2024
- Red Hat Single Sign-On 7.6
- Red Hat build of Keycloak 26.2/26.4

---

## 1. 结论摘要

这次优化整体是**有效且准确的**，并且明显是围绕上一轮分析中识别出的高风险点进行定向修正。  
与上一轮相比，这次改动最有价值的地方不是新增了更多章节，而是把若干“已经写出来但不够 Red Hat 官方化”的内容，进一步收敛到了更接近官方支持边界的表述。

### 最终判断

- 本次优化方向：**正确**
- 本次优化是否明显缩小与 Red Hat 的 gap：**是**
- 是否已经完全填平 gap：**否**
- 是否还有明确优化建议：**有，但已明显减少**

---

## 2. 本次优化涉及的文件

本轮主要改动集中在以下 9 个文件：

- `docs/en/architecture.mdx`
- `docs/en/concepts/50-extensibility.mdx`
- `docs/en/functions/20-manage_realms.mdx`
- `docs/en/functions/25-manage_clients.mdx`
- `docs/en/functions/30-manage_users.mdx`
- `docs/en/functions/40-monitor.mdx`
- `docs/en/how_to/25-configure_database.mdx`
- `docs/en/how_to/35-import_export.mdx`
- `docs/en/how_to/50-customize_themes.mdx`

从内容上看，这一轮优化几乎全部命中了上一轮报告中列出的高风险项：

- `RealmImport` 语义
- `client secret rotation`
- `theme/custom image` 推荐顺序
- `unsupported.podTemplate` 风险边界
- `admin role` 的 query 权限和委派边界
- `cluster discovery` 的 Operator 管理语义
- 数据库支持边界
- observability / health / tracing

---

## 3. 与 Red Hat 的对齐情况

### 3.1 对齐改善最明显的点

#### 1. Client Secret Rotation 已从“前后冲突”修正为“区分 regenerate 与 rotation”

当前文档已经明确区分：

- 普通 `Regenerate` 是立即替换
- rotation 是通过 Client Policy 控制的机制
- rotation 有 grace period 和多 active secrets 的概念

这与 Red Hat build of Keycloak 26.2 Server Administration Guide 关于 `Client Secret Rotation` 和 `Creating an OIDC Client Secret Rotation Policy` 的语义基本一致。

对应本地文档位置：
- `docs/en/functions/25-manage_clients.mdx:102`
- `docs/en/functions/25-manage_clients.mdx:108`
- `docs/en/functions/25-manage_clients.mdx:116`

**判断：这一项已从上一轮的高风险问题，降为低风险。**

#### 2. RealmImport 已基本修正为“一次性导入”语义

当前文档已经清楚说明：

- `KeycloakRealmImport` 是 one-time import
- 不是 continuous sync controller
- Admin Console / REST API 改动不会回写到 CR

这与 Red Hat Operator Guide 中“只支持创建新 realm，不更新、不删除、不会回写”的方向一致。

对应本地文档位置：
- `docs/en/functions/20-manage_realms.mdx:11`
- `docs/en/functions/20-manage_realms.mdx:13`
- `docs/en/how_to/35-import_export.mdx:90`

**判断：这一项明显收敛。**

#### 3. Theme 定制的生产推荐路径已经更接近 Red Hat Operator Guide

当前文档已经将：

- custom image 放到生产推荐路径
- ConfigMap + `unsupported.podTemplate` 明确降级为开发/测试方案
- 补入 unsupported API、滚动升级、体积限制等风险

这与 Red Hat Operator Guide 对 `unsupported` 字段的保守态度基本一致。

对应本地文档位置：
- `docs/en/how_to/50-customize_themes.mdx:98`
- `docs/en/how_to/50-customize_themes.mdx:100`
- `docs/en/how_to/50-customize_themes.mdx:164`

**判断：这一项已从中高风险问题下降为低到中风险问题。**

#### 4. Extensibility 页已经不再鼓励 runtime mount provider JAR

当前文档已经把：

- Quarkus build-time provider 机制说明清楚
- custom image 设为必选生产路径
- `unsupported.podTemplate` + init container 路径明确标成不稳定、不建议生产使用

这与 Red Hat build of Keycloak 26.x 在 Operator / Quarkus 模型下的官方姿势更一致。

对应本地文档位置：
- `docs/en/concepts/50-extensibility.mdx:38`
- `docs/en/concepts/50-extensibility.mdx:46`
- `docs/en/concepts/50-extensibility.mdx:74`
- `docs/en/concepts/50-extensibility.mdx:78`

**判断：这一项已基本修正到位。**

#### 5. Admin Roles 的委派边界比上一轮清晰得多

这次新增了：

- `query-users`
- `query-clients`
- `query-realms`
- `query-groups`
- `create-client`
- query vs view 角色说明
- delegated admin 边界说明
- dedicated admin console 提示

这让文档从“只列 RBAC 名称”进步到“更接近可用于生产委派设计”的程度。

对应本地文档位置：
- `docs/en/functions/30-manage_users.mdx:137`
- `docs/en/functions/30-manage_users.mdx:160`
- `docs/en/functions/30-manage_users.mdx:164`
- `docs/en/functions/30-manage_users.mdx:183`

**判断：明显改善。**

#### 6. Cluster Discovery 现在更符合 Operator-managed 的官方表述

当前写法已从“用户可见的 KUBE_PING / DNS_PING 细节”转向：

- Operator 自动处理 cluster discovery
- 具体协议属于内部实现细节
- 用户主要关注端口、网络策略和 Operator 日志

这比上一轮更接近 Red Hat 官方文档的表达方式。

对应本地文档位置：
- `docs/en/architecture.mdx:149`
- `docs/en/architecture.mdx:157`

**判断：这项修正准确。**

---

## 4. 评分结果

### 4.1 本轮优化前后评分

| 维度 | 优化前 | 优化后 | 变化说明 |
|---|---:|---:|---|
| 功能覆盖度 | 86 | 88 | 功能面变化不大，但关键能力域的表达更完整 |
| Red Hat 结构对齐 | 82 | 88 | 与 Operator Guide / Server Administration Guide 的边界更一致 |
| 事实准确性 | 78 | 91 | 本轮提升最大，多个高风险点已被修正 |
| 可操作性 | 84 | 87 | 说明更稳、更不容易误导用户 |
| 综合评分 | 83 | 90 | 从“较高完成度”提升到“接近可对标官方风格” |

### 4.2 Gap 变化

按上一轮报告口径：

- 上一轮剩余 gap：约 `17/100`
- 本轮优化后剩余 gap：约 `10/100`
- 本轮新增弥补的 gap：约 `7` 分

如果只看“上一轮剩余 gap”：

- 本轮约弥补了 **41%** 的剩余 gap

如果从第一次分析开始累计：

- 初始 gap：约 `44/100`
- 当前 gap：约 `10/100`
- 累计已弥补：约 `34` 分
- 累计弥补比例：约 **77%**

---

## 5. 本轮仍然存在的差距

本轮优化后，主要问题已经不多，但还没有完全归零。

### 5.1 RealmImport 仍有一处表述比 Red Hat 官方更宽

文件：`docs/en/how_to/35-import_export.mdx`

虽然 warning 主体已经修正得比较准，但这里仍有一句：

- `It creates or merges Realm configuration at import time.`

对应位置：
- `docs/en/how_to/35-import_export.mdx:94`

而 Red Hat Operator Guide 的表述更严格：

- 如果同名 realm 已存在，不会被覆盖
- Realm Import CR 只支持创建新 realm
- 不更新、不删除、不会回写

因此，“creates or merges” 这个说法仍然偏宽，建议收紧为“creates a new realm at import time; existing realms are not overwritten” 之类的表达。

**判断：这是本轮剩余的最高优先级问题。**

### 5.2 Tracing 配置路径写法与 Red Hat Operator Guide 不完全一致

文件：`docs/en/functions/40-monitor.mdx`

当前文档写到：

- 启用 tracing 和配置 OTLP exporter endpoint 通过 `additionalOptions` 完成

对应位置：
- `docs/en/functions/40-monitor.mdx:108`
- `docs/en/functions/40-monitor.mdx:109`

但 Red Hat Operator Guide 26.2 给出的做法是：

- tracing 主要通过 `spec.tracing.enabled`、`spec.tracing.endpoint`、`spec.tracing.samplerType`、`spec.tracing.samplerRatio` 配置
- 只有个别额外项才通过 `additionalOptions`

所以这部分现在已经比上一轮更接近官方，但仍存在**配置入口写错层级**的问题。

**判断：这是本轮第二个需要修正的准确性问题。**

### 5.3 Observability 仍未完全对齐 Red Hat Observability Guide 的深度

虽然现在已经补入：

- `/health`
- observability integration
- tracing 提示

但与 Red Hat Observability Guide 相比，仍缺以下内容：

- event metrics
- SLI / SLO
- 更系统的 HTTP / DB / cluster / Infinispan metrics
- dashboards
- exemplars

这部分不再是“错误”，而是“深度未完全补齐”。

**判断：属于剩余中风险 gap。**

### 5.4 Custom image 相关 Operator 细节还可以再补一层

文件：

- `docs/en/how_to/50-customize_themes.mdx`
- `docs/en/concepts/50-extensibility.mdx`

当前文档已经把 custom image 提升为推荐路径，这是正确的。  
但与 Red Hat Operator Guide 相比，还有两个可以补强的点：

- optimized custom image 下，`health-enabled` 和 `metrics-enabled` 需要在 Containerfile 中显式设置
- 使用 custom image 时，通过 CR first-class fields 或 `additionalOptions` 传入的 build-time options 会被忽略

这两个点现在没写，虽不至于构成严重错误，但会影响高级用户的预期管理。

**判断：属于低到中风险优化项。**

---

## 6. 本轮优化是否准确

### 准确的部分

本轮以下改动可以认定为准确且有价值：

- `client secret rotation` 的机制划分
- `RealmImport` 一次性导入语义
- `theme` 的生产推荐路径调整
- `unsupported.podTemplate` 的风险降级
- `admin roles` 中 query 系角色与委派边界
- `cluster discovery` 的 Operator-managed 表达
- `database support boundary` 的保守化表达
- `extensibility` 中 build-time provider 模式的强调

### 仍不完全准确的部分

当前仍然存在但已收窄的问题：

- `RealmImport` 的 “creates or merges” 说法过宽
- tracing 配置路径把 `spec.tracing` 写成了 `additionalOptions`

**总体判断：**  
这次优化是**准确的、有效的、显著提升质量的**，但还没有达到“已经完全没有进一步优化空间”的程度。

---

## 7. 后续优化建议

### P0：建议优先修正

1. 将 `docs/en/how_to/35-import_export.mdx:94` 的 “creates or merges Realm configuration” 改为更严格的创建型导入表达，避免与 Red Hat Operator Guide 冲突。  
2. 将 `docs/en/functions/40-monitor.mdx:108-109` 中 tracing 的配置入口，从 `additionalOptions` 改为 `spec.tracing` 主路径，并保留对个别扩展项使用 `additionalOptions` 的说明。  

### P1：建议继续补强

1. 在监控文档中新增 event metrics、SLI/SLO、dashboards、exemplars 的章节或至少增加链接与边界说明。  
2. 在 custom image 相关文档中补充 optimized image 的 `health-enabled` / `metrics-enabled` 说明，以及 build-time options 与 CR 的关系。  

### 如果你只想做最小闭环

如果只打算再做一轮最小修正，优先改这 2 处即可：

1. `RealmImport` 的 “merges” 表述  
2. tracing 的配置入口写法  

这两处修完后，这一轮优化的准确性基本就能达到很高水平。

---

## 8. 最终结论

本轮优化相较上一轮，属于**高质量的校准型改动**。  
它没有大幅增加文档数量，但显著提高了文档与 Red Hat 官方支持边界的一致性。

最终结论如下：

- **本次优化是准确的**
- **本次优化将综合评分从 83/100 提升到 90/100**
- **本次优化将剩余 gap 从 17/100 缩小到 10/100**
- **本轮新增弥补了约 41% 的上一轮剩余 gap**
- **gap 尚未完全弥补，但剩余问题已经收敛到少数具体表述和深度项**

换句话说，这次改动已经从“补内容”进入到“打磨到接近官方质量”的阶段了。

---

## 9. 参考来源

- Red Hat Application Foundations 2024  
  https://docs.redhat.com/en/documentation/red_hat_application_foundations/2024

- Red Hat build of Keycloak 26.2 Operator Guide  
  https://docs.redhat.com/en/documentation/red_hat_build_of_keycloak/26.2/html-single/operator_guide/index

- Red Hat build of Keycloak 26.2 Observability Guide  
  https://docs.redhat.com/en/documentation/red_hat_build_of_keycloak/26.2/html-single/observability_guide/index

- Red Hat build of Keycloak 26.2 Server Administration Guide  
  https://docs.redhat.com/en/documentation/red_hat_build_of_keycloak/26.2/html/server_administration_guide/index

### 本次分析重点参考的 Red Hat 官方语义

- Application Foundations 2024 中，身份认证产品入口对应 `Single Sign-On`
- Realm Import CR 仅支持创建新 realm，不覆盖已有 realm，不更新、不删除、不会回写 CR
- `unsupported.podTemplate` 属于 experimental / Tech Preview 范畴
- custom image 是 Operator 下的重要最佳实践，但会带来 build-time options 处理差异
- health endpoints 包含 `/health/live`、`/health/ready`、`/health/started`、`/health`
- tracing 在 Operator 模型下有 `spec.tracing` 的专用配置入口
- client secret rotation 通过 Client Policies 实现，并允许两个并行有效 secret
