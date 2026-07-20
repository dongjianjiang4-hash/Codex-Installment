# 通过离线 MSIX 文件安装 Codex 的新手教程

更新时间：2026-07-20

本文面向第一次接触 Windows 离线安装的新手，说明如何通过官方 MSIX 文件安装 Codex 所在的 Windows 桌面应用。

## 先说清楚：安装的是哪个应用？

Windows 上的 Codex 体验在 **ChatGPT 桌面应用**中使用。官方 Windows 应用支持 Codex、项目、工作树、定时任务、Git 功能、内置浏览器、文件预览、插件和技能等能力。

所以这份教程里下载到的文件名会是：

- `ChatGPT-x64.msix`
- `ChatGPT-arm64.msix`
- `ChatGPT-License.xml`

这不是下错了。安装后在开始菜单里看到的通常是 ChatGPT，登录后可以在其中使用 Codex。

## 重要提醒

- “离线安装”指的是不用打开 Microsoft Store，也可以用 MSIX 文件部署应用。
- “离线安装”不等于“离线使用”。Codex/ChatGPT 登录、模型调用、同步和很多功能仍然需要网络。
- 不建议从第三方下载站下载安装包。尽量使用官方链接或公司内部软件库。
- 如果是公司电脑，先确认 IT 政策是否允许安装 MSIX 应用。
- 如果是批量给很多电脑安装，建议让 IT 用 MDM、Intune 或软件分发平台统一部署。

## 你需要准备什么

准备一台可以上网的电脑，用来下载文件；再准备目标 Windows 电脑，用来安装。

需要的东西：

- Windows 10 或 Windows 11 电脑。
- 一个 ChatGPT/OpenAI 账号。
- 目标电脑的管理员权限，至少在遇到权限限制时能请求管理员帮忙。
- U 盘、移动硬盘、内网共享盘，或其他能把文件拷到目标电脑的方式。
- 根据电脑架构选择正确的 MSIX 文件。

## 第一步：确认电脑是 x64 还是 Arm64

大多数普通 Intel/AMD 电脑都是 x64。少数高通骁龙、Windows on ARM 设备是 Arm64。

图形界面查看方法：

1. 按 `Win + I` 打开设置。
2. 进入 `系统`。
3. 进入 `关于`。
4. 查看 `系统类型`。

PowerShell 查看方法：

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object OSArchitecture
```

判断方式：

- 显示 `64-bit`、`x64`、`AMD64`：下载 x64 包。
- 显示 `ARM64`：下载 Arm64 包。
- 如果是 32 位系统，官方离线部署页只列出 x64 和 Arm64 包，不建议继续尝试。

## 第二步：在有网电脑上下载官方文件

官方离线部署页提供了稳定链接，这些链接指向当前最新发布的 Store 签名包。

### x64 电脑下载

适合大多数 Intel/AMD Windows 电脑。

```powershell
New-Item -ItemType Directory -Force -Path "C:\CodexOffline"

Invoke-WebRequest `
  -Uri "https://persistent.oaistatic.com/codex-app-prod/ChatGPT-x64.msix" `
  -OutFile "C:\CodexOffline\ChatGPT-x64.msix"

Invoke-WebRequest `
  -Uri "https://persistent.oaistatic.com/codex-app-prod/ChatGPT-License.xml" `
  -OutFile "C:\CodexOffline\ChatGPT-License.xml"
```

### Arm64 电脑下载

适合 Windows on ARM 设备。

```powershell
New-Item -ItemType Directory -Force -Path "C:\CodexOffline"

Invoke-WebRequest `
  -Uri "https://persistent.oaistatic.com/codex-app-prod/ChatGPT-arm64.msix" `
  -OutFile "C:\CodexOffline\ChatGPT-arm64.msix"

Invoke-WebRequest `
  -Uri "https://persistent.oaistatic.com/codex-app-prod/ChatGPT-License.xml" `
  -OutFile "C:\CodexOffline\ChatGPT-License.xml"
```

如果不熟悉 PowerShell，也可以直接在浏览器中打开对应链接下载。

## 第三步：记录文件哈希，方便以后核对

因为官方链接会指向“最新包”，以后同一个链接下载到的版本可能会变。建议下载后记录一次哈希值，方便确认文件有没有损坏或被替换。

以 x64 为例：

```powershell
Get-FileHash "C:\CodexOffline\ChatGPT-x64.msix" -Algorithm SHA256
Get-FileHash "C:\CodexOffline\ChatGPT-License.xml" -Algorithm SHA256
```

以 Arm64 为例：

```powershell
Get-FileHash "C:\CodexOffline\ChatGPT-arm64.msix" -Algorithm SHA256
Get-FileHash "C:\CodexOffline\ChatGPT-License.xml" -Algorithm SHA256
```

把输出结果保存到一个文本文件里，例如：

```text
download-date: 2026-07-20
package: ChatGPT-x64.msix
sha256: 这里粘贴 Get-FileHash 输出的哈希
```

## 第四步：把文件拷到目标电脑

把下面文件拷到目标电脑，比如放到：

```text
C:\CodexOffline
```

x64 电脑需要：

```text
C:\CodexOffline\ChatGPT-x64.msix
C:\CodexOffline\ChatGPT-License.xml
```

Arm64 电脑需要：

```text
C:\CodexOffline\ChatGPT-arm64.msix
C:\CodexOffline\ChatGPT-License.xml
```

单机手动安装通常只需要 `.msix` 文件。`ChatGPT-License.xml` 主要用于某些离线部署、MDM 或企业软件分发流程。为了省事，建议一起保存。

## 第五步：安装方式 A，双击安装

这是最适合新手的方法。

1. 打开 `C:\CodexOffline`。
2. 双击对应的 `.msix` 文件。
3. Windows 会打开“应用安装程序”窗口。
4. 点击 `安装`。
5. 安装完成后点击 `启动`，或者从开始菜单搜索 `ChatGPT` 打开。

如果双击没有反应、提示找不到打开方式，或者公司电脑禁用了应用安装程序，请使用下一种 PowerShell 方法。

## 第六步：安装方式 B，用 PowerShell 安装

右键开始菜单，选择 `终端` 或 `Windows PowerShell`。如果普通窗口安装失败，再选择 `以管理员身份运行`。

x64 安装命令：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-x64.msix" -ForceApplicationShutdown
```

Arm64 安装命令：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-arm64.msix" -ForceApplicationShutdown
```

等待命令执行完成。如果没有报错，通常就是安装成功。

微软的 `Add-AppxPackage` 命令用于把签名的 `.msix` 或 `.appx` 应用包添加到当前用户账号。一般个人电脑安装到当前用户就够了。

## 第七步：验证是否安装成功

方法一：从开始菜单查找。

1. 按 `Win`。
2. 输入 `ChatGPT`。
3. 能看到并打开应用，就说明安装成功。

方法二：用 PowerShell 查看。

```powershell
Get-AppxPackage *ChatGPT* | Select-Object Name, Version, PackageFullName
```

如果能看到名称和版本号，说明系统里已经有这个应用包。

## 第八步：首次打开和登录

1. 打开开始菜单里的 `ChatGPT`。
2. 按提示登录 ChatGPT/OpenAI 账号。
3. 登录后进入应用。
4. 找到 Codex 相关入口或项目入口，按提示添加本地项目。

如果目标电脑完全没有网络，应用可能可以安装，但无法完成登录和使用 Codex。

## 更新方法

离线 MSIX 部署不会让用户自己自动更新。以后要更新，需要重新下载新版 MSIX，再重新部署。

推荐做法：

1. 定期在有网电脑上重新下载官方 MSIX。
2. 记录下载日期和 SHA256 哈希。
3. 拷到目标电脑。
4. 关闭正在运行的 ChatGPT/Codex。
5. 再次运行安装命令。

x64 更新命令：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-x64.msix" -ForceApplicationShutdown
```

Arm64 更新命令：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-arm64.msix" -ForceApplicationShutdown
```

官方建议：如果不能每次发布都更新，至少每周检查一次是否有新包。延迟更新会带来安全性和稳定性方面的取舍。

## 卸载方法

图形界面卸载：

1. 打开 `设置`。
2. 进入 `应用`。
3. 进入 `已安装的应用`。
4. 搜索 `ChatGPT`。
5. 点击卸载。

PowerShell 卸载当前用户版本：

```powershell
Get-AppxPackage *ChatGPT* | Remove-AppxPackage
```

如果是公司统一部署的版本，请优先找 IT 通过管理平台卸载。

## 企业或多用户部署说明

如果只是给自己电脑安装，用双击或 `Add-AppxPackage` 就够了。

如果是公司批量部署，建议使用：

- Microsoft Intune
- MDM 平台
- 公司已有的软件分发平台
- IT 管理脚本或镜像流程

官方推荐在可以使用 Microsoft Store 应用部署和更新端点的环境里，通过 Intune 或其他管理工具部署 Microsoft Store 应用。产品 ID 是：

```text
9PLM9XGG6VKS
```

如果网络阻止 Microsoft 应用分发服务，则使用 Store 签名的 MSIX 包，并在需要时连同 `ChatGPT-License.xml` 一起导入到 MDM 或软件部署平台。

对于 Windows 镜像预装或给新用户预配应用，微软提供了 `Add-AppxProvisionedPackage`。这是 IT 管理场景，不建议新手随便在个人电脑上尝试。

示例，仅供 IT 参考：

```powershell
Add-AppxProvisionedPackage `
  -Online `
  -PackagePath "C:\CodexOffline\ChatGPT-x64.msix" `
  -LicensePath "C:\CodexOffline\ChatGPT-License.xml"
```

注意：预配应用会影响当前系统和新建用户，操作前应先测试，并确认公司策略允许。

## 常见问题

### 1. 双击 MSIX 没反应怎么办？

先尝试 PowerShell：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-x64.msix"
```

如果仍然失败，可能是应用安装程序、系统策略或公司安全软件限制。需要找管理员确认。

### 2. 提示架构不匹配怎么办？

大概率是 x64 和 Arm64 包选错了。

重新确认系统架构：

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object OSArchitecture
```

然后下载对应版本。

### 3. 安装成功但打不开 Codex 怎么办？

先确认打开的是 `ChatGPT` 桌面应用。然后检查：

- 是否已经登录 ChatGPT/OpenAI 账号。
- 当前网络是否能访问 ChatGPT/Codex 服务。
- 当前账号是否有相应功能权限。
- 公司网络是否拦截登录或模型服务。

### 4. 安装包需要证书吗？

官方离线部署页说明这些包是 Store 签名的 MSIX。正常情况下不需要你额外安装证书。不要随便使用来源不明的证书或非官方修改包。

### 5. 能不能不用 Microsoft Store？

可以。官方离线部署路径就是在网络不能使用 Microsoft 应用分发服务时，下载 Store 签名的 MSIX 包进行部署。但这种方式不提供独立 MSI 或非 Store EXE，也不会让用户自己自动更新。

### 6. 完全断网能用吗？

通常不能。完全断网可能只能完成安装，无法登录、同步或调用模型。Codex 的实际使用仍需要网络和账号权限。

### 7. PowerShell 提示需要管理员怎么办？

关闭当前窗口，右键开始菜单，选择：

```text
终端（管理员）
```

或：

```text
Windows PowerShell（管理员）
```

再重新运行安装命令。

## 推荐给新手的最短流程

1. 查看电脑是 x64 还是 Arm64。
2. 在有网电脑下载对应 MSIX。
3. 一起下载 `ChatGPT-License.xml` 备用。
4. 把文件拷到目标电脑的 `C:\CodexOffline`。
5. 双击 `.msix` 安装。
6. 双击失败就用 PowerShell：

```powershell
Add-AppxPackage -Path "C:\CodexOffline\ChatGPT-x64.msix" -ForceApplicationShutdown
```

7. 从开始菜单打开 `ChatGPT`。
8. 登录账号，进入 Codex 使用。

## 官方资料

- OpenAI / ChatGPT Learn：Windows 应用部署说明  
  https://learn.chatgpt.com/docs/enterprise/windows-deployment

- OpenAI / ChatGPT Learn：ChatGPT Windows 桌面应用说明  
  https://learn.chatgpt.com/docs/windows/windows-app

- x64 官方 MSIX 包  
  https://persistent.oaistatic.com/codex-app-prod/ChatGPT-x64.msix

- Arm64 官方 MSIX 包  
  https://persistent.oaistatic.com/codex-app-prod/ChatGPT-arm64.msix

- 离线 license 文件  
  https://persistent.oaistatic.com/codex-app-prod/ChatGPT-License.xml

- Microsoft Learn：`Add-AppxPackage`  
  https://learn.microsoft.com/en-us/powershell/module/appx/add-appxpackage

- Microsoft Learn：安装和更新 App Installer  
  https://learn.microsoft.com/en-us/windows/msix/app-installer/install-update-app-installer
