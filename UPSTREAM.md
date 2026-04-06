# UPSTREAM Sync Log

## 基本信息

- **上游仓库 (iOS)**: https://github.com/horizontalsystems/unstoppable-wallet-ios
- **上游仓库 (Android)**: https://github.com/horizontalsystems/unstoppable-wallet-android
- **本仓库 (iOS)**: https://github.com/bitsmileycom/bitsmiley-wallet-ios
- **本仓库 (Android)**: https://github.com/bitsmileycom/bitsmiley-wallet-android
- **Fork 日期**: 2026-04-06
- **License**: MIT (上游与本仓库一致)
- **Upstream sync 负责人**: @cryptomike1024

## 同步策略

- 每月 **1 号**固定从 upstream 同步一次
- 上游发布安全公告时,**24 小时内**紧急 sync
- 合并策略: `git merge upstream/master` (保留 merge commit 便于追溯)
- 冲突优先级: 品牌/配置类保留我方,核心逻辑优先 upstream

## 首次配置 upstream 远程 (只做一次)

```bash
# iOS 仓库
cd bitsmiley-wallet-ios
git remote add upstream https://github.com/horizontalsystems/unstoppable-wallet-ios.git
git remote -v   # 确认看到 origin 和 upstream

# Android 仓库
cd bitsmiley-wallet-android
git remote add upstream https://github.com/horizontalsystems/unstoppable-wallet-android.git
git remote -v
```

## 每月同步流程

```bash
# 1. 确保本地干净
git status
git checkout master

# 2. 拉上游
git fetch upstream

# 3. 新建 sync 分支
git checkout -b sync/2026-05-01

# 4. 合并
git merge upstream/master

# 5. 如有冲突,按下面的"冲突解决优先级"处理
# 6. 解决后跑编译和冒烟测试
# 7. 推送并开 PR 到 master
git push origin sync/2026-05-01
```

## 冲突解决优先级

1. **品牌类冲突** (app name / icon / color): 保留我方改动
2. **配置类冲突** (API key / RPC endpoint): 保留我方改动
3. **核心逻辑冲突** (Core / Modules): 优先采用 upstream,然后评估我方补丁是否仍需要
4. **无法判断**: 暂停 merge,开 issue 讨论

## 红线清单 (禁止直接修改的文件/目录)

- `UnstoppableWallet/UnstoppableWallet/Core/**`
- `UnstoppableWallet/UnstoppableWallet/Models/**`
- `Podfile` (只允许在文件末尾追加 bitsmiley 专属依赖)
- `settings.gradle.kts` (只允许在文件末尾追加 bitsmiley 专属模块)
- 上游的 `Localizable.strings` (用 BitsmileyOverrides.strings 覆盖)
- 上游的 `AppConfig.swift` (用 AppConfig+Bitsmiley.swift extension)

## 覆盖层文件 (我方独立维护,零冲突)

**iOS**:
- `UnstoppableWallet/UnstoppableWallet/*.lproj/BitsmileyOverrides.strings`
- `UnstoppableWallet/UnstoppableWallet/Extensions/BitsmileyLocalizer.swift`
- `UnstoppableWallet/UnstoppableWallet/Configuration/AppConfig+Bitsmiley.swift`
- `UnstoppableWallet/UnstoppableWallet/Modules/Bitsmiley*/`
- `bitsmiley.xcconfig` (不进 Git)

**Android**:
- `app/src/main/res/values*/bitsmiley_overrides.xml`
- `app/src/main/java/io/bitsmiley/wallet/**`
- `local.properties` (不进 Git)

## 同步历史

| 日期 | upstream SHA | 本地 commit | 解决人 | 冲突数 | 备注 |
|------|--------------|-------------|--------|--------|------|
| 2026-04-06 | (初始 SHA) | (初始 SHA) | @cryptomike1024 | 0 | 初始 fork |
