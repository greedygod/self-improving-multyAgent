# self-improving-multyAgent
基于 clawhub.ai/ivangdavila/self-improving v1.2.16修订

# Self-Improving 功能对比

## 版本信息


| 项目 | Clawhub原版（1.2.16）| 修改版（1.2.16-tztest）|
|------|----------------------|---------------------|
| 数据路径 | 硬编码 `~/self-improving/` | 通过 `.self-improving.json` 配置 |
| 多 agent 支持 | ❌ 差，所有 agent 共用同一路径 | ✅ 好，每个 agent 独立路径 |
| 初始化方式 | 手动创建目录 | 首次触发时自动检测并初始化 |
| 配置灵活性 | 差 | 好，可自定义 dataPath |

---

## 核心改造点

### 1. 路径配置机制（新增）

**旧版：** 硬编码路径，所有 agent 都往 `~/self-improving/` 写数据。

**新版：** 引入 `{baseDir}/../self-improving/.self-improving.json`
```json
{
"dataPath": "自定义存储路径",
"version": "1.0",
"initializedAt": "2026-03-30T09:48:00+08:00"
}
```

## 原版问题，记忆介质指向~/self-improving/，仅实现用户隔离：

当同一用户运行有多个 agent 时，~/self-improving/存储的是同一份记忆载体，互相干扰，记忆会串。

```
~/self-improving/                    ← 所有 agent 共用
├── memory.md                       ← main agent 的教训
├── corrections.md
└── ...
```



**新版方案：**
每个 agent 初始化时向各自的 `.self-improving.json` 写入自己的路径，天然隔离，互不干扰。

```
/root/.openclaw/
├── workspace/                    ← main agent（Agent A）
│   ├── self-improving/          ← A的记忆数据
│   │   ├── .self-improving.json  ← A的记忆数据配置 dataPath: /root/.openclaw/workspace/self-improving 
│   │   ├── memory.md
│   │   ├── corrections.md
│   │   └── ...
│   └── skills/
│
├── workspace-b/    ← （Agent B）
│    ├── self-improving/           ← B的记忆数据   
│    │   ├── .self-improving.json  ← B的记忆数据配置 dataPath: /root/.openclaw/workspace-b/self-improving 
│    │   ├── memory.md
│    │   ├── corrections.md
│    │   └── ...
│    └── skills/
├── workspace-c/    ← （Agent C）
│   ├── self-improving/      
│   │   └── .self-improving.json    ← C的记忆数据配置 自定义dataPath: /root/.openclaw/anyplace-c/self-improving ，不局限在工作区
│   ├── skills/
│   └── ...
│ 
├── anyplace-c/
    └── self-improving/  ← C的记忆数据
        ├── memory.md
        ├── corrections.md
        └── ...

```




---
### 3. 初始化流程变化

| 步骤 | 旧版 | 新版 |
|------|------|------|
| 1 | 检查 `~/self-improving/` 是否存在 | 检查 `{baseDir}/../self-improving/.self-improving.json` 是否存在 |
| 2 | 存在则跳过，不存在则创建 | 配置文件存在且 dataPath 有效则跳过 |
| 3 | 创建默认文件结构 | 配置文件无效则触发引导流程 |
| 4 | — | 支持用户自定义 dataPath |

---

## 功能差异汇总
| 功能 | 旧版 | 新版 |
|------|------|------|
| 分层存储（HOT/WARM/COLD） | ✅ | ✅ | 无变化 |
| 修正日志（corrections.md） | ✅ | ✅ | 无变化 |
| 自我复盘（reflections） | ✅ | ✅ | 无变化 |
| 心跳维护（heartbeat） | ✅ | ✅ | 无变化 |
| 规则确认流程（3次确认） | ✅ | ✅ | 无变化 |
| 多 agent 路径隔离 | ❌ | ✅ | **新增** |
| 可配置数据路径 | ❌ | ✅ | **新增** |
| 初始化引导 | ❌ | ✅ | **新增** |
---
## 适用场景建议
- **单 agent 环境：** 两者功能等价，无区别。
- **多 agent 环境（必须升级）：** 旧版会串记忆，新版完全隔离。
- **追求最新功能：** 等 1.2.17-test 正式发布后再从 Clawhub 安装，或继续用
