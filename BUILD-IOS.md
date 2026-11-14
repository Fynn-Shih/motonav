# motonav iOS 构建用仓库骨架

这个目录只装 iOS App 的源码和编译工作流，用来在 GitHub 上出一版**未签名 IPA**（Windows 上没法直接
编译 iOS，因为 `build-ios.sh` 要求 macOS + Xcode）。

## 内容

```
.github/workflows/ios-build.yml   # GitHub Actions 工作流：Build iOS app (unsigned)
ios/                              # iOS App 源码（MotoNav 0.16.0 / build 12），2.5 MB
```

`ios/Pods` 不存在也不需要：工作流里会自己跑 `pod install --project-directory=ios`。

## 上传步骤

1. 打开 https://github.com/Fynn-Shih/motonav ；
2. 点 **Add file → Upload files**；
3. 把本目录里的 `.github` 和 `ios` 两个文件夹一起拖进页面（GitHub 网页支持整文件夹拖拽上传）；
4. Commit 到默认分支（一般是 `main`）。

## 出 IPA

1. 进 **Actions** 页，左侧选 **Build iOS app (unsigned)**；
2. 点 **Run workflow** → 分支选默认分支 → 运行（约 10~20 分钟，`macos-26` runner）；
3. 跑完在该次 run 底部 **Artifacts** 下载 `MotoNav-unsigned-ipa`；
4. 解压得到 `MotoNav-<版本>-unsigned.ipa` 与 `SHA256SUMS.txt`，可用
   `certutil -hashfile MotoNav-*.ipa SHA256` 核对。

## 装到 iPhone

用 Sideloadly（或爱思助手）拖入 IPA，填自己的 Apple ID 签名；装完在
设置 → 通用 → VPN 与设备管理 里信任证书。免费账号 7 天有效，到期重签即可。

## 装机后的联调顺序（别跳步）

1. 圆屏**开机两分钟内**才开放配对；
2. iPhone 设置 → 蓝牙 → `MotoNav-XXXX`，按圆屏显示的**六位配对码**完成系统配对
   （固件 RX 特性要求加密+认证，未配对写不进任何命令）；
3. App 里打开 **蓝牙媒体按键**（下发 `#HM,1`）；固件 `hidMusicMode` 默认为 false，
   不开这个开关音乐页永远是空的、媒体键也不发；
4. 以前配对过旧固件的手机要先"忽略此设备"再重配，避免旧 GATT 缓存。

详细的验证点见主工程 `docs/verification/ios-app-sideload.md`。
