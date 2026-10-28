# zjhqgx — DSH 插件紧急安全公告

**只在出现共同漏洞、需要紧急重大安全补丁时更新这个仓库。**

三个插件（`hdd-console-boot` / `dsh-cjgl` / `dsh-gd`）的自动检查会读本仓库根目录的
[`security.json`](./security.json)。命中的公告会被**强制静默更新**——不问用户、不弹窗，
后台直接下载修复版本并覆盖自身，重启应用后生效。

## 两条通道

| 通道 | 来源 | 行为 |
|---|---|---|
| 常规更新 | 各插件自己仓库的 `package.json` | 提示 / 询问，用户决定 |
| **安全补丁** | **本仓库 `security.json`** | **强制静默更新**，不打扰用户 |

普通版本更新不会写这里——只有需要立刻让所有人升级时才加一条 advisory。

## 格式

```jsonc
{
  "schema": 2,
  "updated": "2026-10-28",
  "advisories": [
    {
      "id": "ZJHQGX-2026-001",
      "plugin": "dsh-cjgl",        // 包名
      "severity": "high",          // low | medium | high | critical
      "summary": "…",
      "affected": "<3.0.2",        // 受影响的版本区间
      "fixed": "3.0.2",            // 修复版本
      "url": "…"                   // 详情 / 安装包所在仓库
    }
  ]
}
```

插件侧判定：

```
命中 = advisories 里某条
        && advisory.plugin === 自己的包名
        && fixed > 当前版本
        && 当前版本落在 affected 区间内
```

命中后**自动执行**：

1. 从该插件仓库的 `dist/<包名>-<fixed>.tgz` 下载安装包
2. 解压覆盖 `node_modules/<包名>`
3. 把 `dependencies` 指向新包，清理 `pnpm-lock.yaml`
4. 记录「已自动应用」，设置页可见
5. 重启应用生效

失败不重试、不弹窗，只在设置页留下失败原因；下一次检查（最多 24 小时一次）会再试。

## 维护流程

1. 发现共同漏洞 → 修在某个插件的新版本里，发布到该仓库的 `dist/`
2. 在这里加一条 advisory（`affected` 写受影响区间，`fixed` 写修复版本）
3. 提交。三个插件在下一次检查（最多 24 小时一次）就会静默升级

## License

MIT
