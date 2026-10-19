# 插件仓库 · jzj2621899857

这是一个自建 DBX 插件仓库（custom repository）。在 DBX 插件中心填一次 catalog 地址，之后本仓库承载的所有插件都能被发现、安装与更新。

```
Catalog 地址（填进 DBX）
https://raw.githubusercontent.com/jzj2621899857/dbx-plugins/main/catalog/index.json
```

## 这里放什么

| 路径 | 内容 |
| --- | --- |
| `catalog/index.json` | 仓库索引。DBX 只认这一个地址 |
| `keys/*.pub` | 仓库签名公钥（32 字节 Ed25519），用于分发 |
| `releases/` | 已签名的可安装 `.dbxp` 与对应 artifact 元数据 |

插件的**源码不在这里**。每个插件的代码在它自己的仓库里：

| 插件 | 源码仓库 |
| --- | --- |
| 棋牌桌（象棋 + 五子棋） | [dbx-board-games](https://github.com/jzj2621899857/dbx-board-games) |

## 为什么源码和目录分开放

DBX 的信任模型把四类东西分开：插件源码、未签名候选包、仓库签名的可安装包、catalog 元数据。它们归属不同、生命周期不同，官方明确不建议塞进同一个 git 仓库。最关键的一条：**仓库签名私钥绝不能进源码仓库、catalog、包内或 CI 日志**。

所以这里是纯粹的「分发容器」：只放索引、公钥和签名产物。

## 在 DBX 里添加这个仓库

1. 打开插件中心 → 设置 → 插件仓库
2. 仓库 ID 填 `jzj2621899857-plugins`，名称随意
3. URL 填上面的 catalog 地址
4. 点「添加仓库」

添加后请在「第三方与开发者选项」里，用与本文档独立的渠道核对仓库公钥——DBX 不会自动信任自建仓库的签名密钥。

## 新增一个插件

1. 在插件自己的仓库里开发、打 tag、发布 GitHub Release，CI 产出**未签名候选包**
2. 在 `releases/` 下发布签名后的 `.dbxp` 与 artifact 元数据
3. 在 `catalog/index.json` 的 `plugins` 数组里追加一条：id、显示名、图标、标签、权限、版本、各平台 artifact 的 url / sha256 / size / signingKeyId
4. 校验后提交

catalog 地址不变，新插件自动出现。

## 签名

```bash
# 仅仓库运维使用，一次性生成
dbx-plugin keygen jzj2621899857-plugins.release

# 给已构建好的候选包签名
dbx-plugin sign candidate.dbxp signed.dbxp \
  --key-id jzj2621899857-plugins.release \
  --artifact-metadata signed.artifact.json \
  --target darwin-arm64 \
  --artifact-url https://.../signed.dbxp
```

私钥只存在于本机受保护的环境与 CI 的加密 secret 中，永不提交。

## 不可变性

已发布的版本不原地重建。源码、元数据或包字节有任何变化，就发新版本、新产物。

## 许可

本仓库的元数据以 Apache-2.0 提供；各插件的许可见其源仓库。
