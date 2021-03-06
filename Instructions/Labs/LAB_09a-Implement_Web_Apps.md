---
lab:
    title: '09a - 实现 Web 应用'
    module: '模块 09 - 无服务器计算'
---

# 实验室 09a - 实现 Web 应用
# 学生实验室手册

## 实验室场景

你需要评估可否使用 Azure Web 应用来托管 Contoso 的网站，这些网站当前托管在公司的本地数据中心。网站通过 PHP 运行时堆栈在 Windows 服务器上运行。你还需要确定如何利用 Azure Web 应用部署槽位来实施 DevOps 实践。

## 目标

在本实验室中，你将：
  
+ 任务 1：创建 Azure Web 应用
+ 任务 2：创建暂存部署槽位
+ 任务 3： 配置 Web 应用部署设置
+ 任务 4：将代码部署到暂存部署槽位
+ 任务 5：交换过渡槽
+ 任务 6：配置并测试 Azure Web 应用的自动缩放
  
## 预计用时：30 分钟

## 说明

### 练习 1

#### 任务 1：创建 Azure Web 应用

在此任务中，你将创建 Azure Web 应用。 

1. 登录到 [Azure 门户](http://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择 **“应用服务”**，然后在 **“应用服务”** 边栏选项卡上，单击 **“+ 添加”**。

1. 在 **“Web 应用”** 边栏选项卡的 **“基本设置”** 选项卡中，指定以下设置（其他设置则保留为默认值）：

    | 设置 | 值 |
    | --- | ---|
    | 订阅 | 在本实验室中使用的 Azure 订阅的名称 |
    | 资源组 | 新资源组名称 **az104-09a-rg1** |
    | Web 应用名称 | 任何全局唯一名称 |
    | 发布 | **代码** |
    | 运行时堆栈 | **PHP 7.3** |
    | 操作系统 | **Windows** |
    | 区域 | 可以在其中预配 Azure Web 应用的 Azure 区域的名称 |
    | 应用服务计划 | 接受默认配置 |

1. 单击 **“下一步: 监视 >”**，然后在 **“Web 应用”** 边栏选项卡的 **“监视”** 选项卡上，将 **“启用 Application Insights”** 开关设置为 **“否”**，单击 **“查看 + 创建”**，然后单击 **“创建”**。 

    >**注意**：通常需要启用 **Application Insights**，但本实验室未使用其功能。

    >**注意**：请等到 Web 应用创建完成，再继续执行下一个任务。此操作大约需要 1 分钟。 

1. 在部署边栏选项卡上，单击 **“转到资源”**。

#### 任务 2：创建暂存部署槽位

在此任务中，你将创建暂存部署槽位。 

1. 在新部署 Web 应用的边栏选项卡上，单击 **URL** 链接以在新的浏览器选项卡中显示默认网页。

1. 关闭新的浏览器选项卡，然后返回 Azure 门户，在 Web 应用边栏选项卡的 **“部署”** 部分，单击 **“部署槽位”**。 

    >**注意**：此时，Web 应用有一个标记为 **PRODUCTION** 的部署槽位。 

1. 单击 **“+ 添加槽位”**，并使用以下设置添加新槽位： 

    | 设置 | 值 |
    | --- | ---|
    | 名称 | **staging** |
    | 克隆设置来源 | **请勿克隆设置**|

1. 返回 Web 应用的 **“部署槽位”** 边栏选项卡，单击代表新创建过渡槽的条目。 

    >**注意**：这将打开显示过渡槽属性的边栏选项卡。 

1. 查看过渡槽边栏选项卡，注意其 URL 与分配给生产槽的 URL 不同。

#### 任务 3： 配置 Web 应用部署设置

在此任务中，你将配置 Web 应用部署设置。 

1. 在暂存部署槽位边栏选项卡的 **“部署”** 部分，单击 **“部署中心”**。

    >**注意：** 请确保处于过渡槽（而不是生产槽）边栏选项卡上。

1. 在 **“持续部署(CI/CD)”** 部分，选择 **“本地 Git”**，然后单击 **“继续”**。

1. 选择 **“应用服务生成服务”**，单击 **“继续”**，然后单击 **“完成”**。 

1. 将生成的 **“Git 克隆 URL”** 复制到记事本。

    >**注意：** 本实验室的下一个任务将需要“Git 克隆 URL”值。

1. 单击 **“部署凭据”** 工具栏图标以显示 **“部署凭据”** 窗格。 

1. 单击 **“用户凭据”**。

1. 填写所需信息，然后单击 **“保存凭据”**。 

    | 设置 | 值 |
    | --- | ---|
    | 用户名 | 任何唯一名称（不得包含 `@` 字符） |
    | 密码 | 满足复杂性要求的任何密码 |
    
    >**注意：** 密码必须至少包含 8 个字符，并且至少包括以下三种元素中的两种：字母、数字和非字母数字字符。

    >**注意：** 你将在此实验室的下一个任务中用到这些凭据。

#### 任务 4：将代码部署到暂存部署槽位

在此任务中，你会将代码部署到暂存部署槽位。

1. 在 Azure 门户中，单击 Azure 门户右上方的图标，打开 **Azure Cloud Shell**。

1. 提示选择 **Bash** 或 **PowerShell** 时，选择 **PowerShell**。 

    >**注意**：如果这是第一次启动 **Cloud Shell**，且看到 **“未装载任何存储”** 消息，请选择在本实验室中使用的订阅，然后选择 **“创建存储”**。 

1. 在 Cloud Shell 窗格中运行以下命令，以克隆包含 Web 应用代码的远程存储库。

   ```pwsh
   git clone https://github.com/Azure-Samples/php-docs-hello-world
   ```
 
1. 在 Cloud Shell 窗格中运行以下命令，以将当前位置设置为包含示例 Web 应用代码的本地存储库的新创建克隆。

   ```
   Set-Location -Path $HOME/php-docs-hello-world/
   ```

1. 在 Cloud Shell 窗格中运行以下命令，以添加远程 git（确保将 `[deployment_user_name]` 和 `[git_clone_url]` 占位符分别替换为**部署凭据**用户名的值和 **Git 克隆 URL**，二者均由你在上一个任务中确定）：

   ```
   git remote add [deployment_user_name] [git_clone_url]
   ```

    >**注意**： `git remote add` 后面的值不必与**部署凭据**用户名相同，但必须是唯一的

1. 在 Cloud Shell 窗格中运行以下命令，以将示例 Web 应用代码从本地存储库推送到 Azure Web 应用暂存部署槽位（确保将 `[deployment_user_name]` 占位符替换为你在上一个任务中确定的**部署凭据**用户名的值）：
   ```
   git push [deployment_user_name] master
   ```

1. 如果提示进行身份验证，请键入 `[deployment_user_name]` 和相应的密码（你在上一个任务中设置）。

1. 关闭 Cloud Shell 窗格。

1. 在过渡槽边栏选项卡上，单击 **“概览”**，再单击 **URL** 链接，以在新的浏览器选项卡中显示默认网页。

1. 确认浏览器页面显示 **“Hello World!”** 消息，然后关闭新选项卡。

#### 任务 5：交换过渡槽

在此任务中，你会将过渡槽与生产槽交换

1. 导航回到显示 Web 应用生产槽的边栏选项卡。

1. 在 **“部署”** 部分，单击 **“部署槽位”**，然后单击 **“交换”** 工具栏图标。

1. 在 **“交换”** 边栏选项卡上，查看默认设置，然后单击 **“交换”**。 

1. 在 Web 应用的生产槽边栏选项卡上单击 **“概览”**，然后单击 **URL** 链接，以在新的浏览器选项卡中显示网站主页。

1. 验证默认网页是否已替换为 **“Hello World!”** 页面。 

#### 任务 6：配置并测试 Azure Web 应用的自动缩放

>**注意**：你需要确认在已此实验室的订阅中注册 Microsoft.Insights。有关此任务的详细信息，请参阅 https://docs.microsoft.com/zh-cn/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal
    

在此任务中，你将配置和测试 Azure Web 应用的自动缩放。 

1. 在显示 Web 应用生产槽的边栏选项卡上，单击 **“设置”** 部分的 **“横向扩展(应用服务计划)”**。

1. 单击 **“自定义自动缩放”**。 

    >**注意**：你还可以选择手动缩放 Web 应用。

1. 保留选中默认选项 **“基于指标缩放”**，然后单击 **“+ 添加规则”**

1. 在 **“缩放规则”** 边栏选项卡上，指定以下设置（其他设置则保留为默认值）：

    | 设置 | 值 |
    | --- |--- |
    | 指标源 | **当前资源** |
    | 时间聚合 | **最大值** |
    | 指标命名空间 | **应用服务计划标准指标** |
    | 指标名称 | **CPU 百分比** |
    | 运算符 | **大于** |
    | 触发缩放操作的指标阈值 | **10** |
    | 持续时间（分钟） | **1** |
    | 时间粒度统计信息 | **最大值** |
    | 操作 | **计数增加** |
    | 实例计数 | **1** |
    | 冷却（分钟） | **5** |

    >**注意**：显然，这些值并不代表实际配置，因为它们的目的是在不延长等待时间的情况下尽快触发自动缩放。
    
    

1. 单击 **“添加”**，然后回到应用服务计划缩放边栏选项卡，指定以下设置（其他设置则保留为默认值）：

    | 设置 | 值 |
    | --- |--- |
    | 最小实例限制 | **1** |
    | 最大实例限制 | **2** |
    | 默认实例限制 | **1** |

1. 单击 **“保存”**。

    

1. 在 Azure 门户中，单击 Azure 门户右上方的图标，打开 **Azure Cloud Shell**。

1. 提示选择 **Bash** 或 **PowerShell** 时，选择 **PowerShell**。 

1. 在 Cloud Shell 窗格中运行以下命令，以确定 Azure Web 应用的 URL。

   ```pwsh
   $rgName = 'az104-09a-rg1'

   $webapp = Get-AzWebApp -ResourceGroupName $rgName
   ```

1. 在 Cloud Shell 窗格中运行以下命令，以启动将 HTTP 请求发送到 Web 应用的无限循环：

   ```pwsh
   while ($true) { Invoke-WebRequest -Uri $webapp.DefaultHostName }
   ```

1. 最小化 Cloud Shell 窗格（但不要将其关闭），并在 Web 应用边栏选项卡上的 **“监视”** 部分，单击 **“进程资源管理器”**。

    >**注意**：进程资源管理器有助于监视实例数及其资源利用率。 

1. 监视几分钟的利用率和实例数。

    >**注意**：你可能需要**刷新**页面x。

1. 一旦注意到实例数增加到 2，就重新打开 Cloud Shell 窗格并按 **Ctrl+C** 终止脚本。

1. 关闭 Cloud Shell 窗格。

#### 清理资源

   >**注意**：请记得删除任何新创建而不会再使用的 Azure 资源。请删除未使用的资源，确保不产生意外费用。

1. 在 Azure 门户中，在 **Cloud Shell** 窗格中打开 **PowerShell** 会话。

1. 运行以下命令，列出在本模块各实验室中创建的所有资源组：

   ```pwsh
   Get-AzResourceGroup -Name 'az104-09a*'
   ```

1. 运行以下命令，删除在本模块各个实验室中创建的所有资源组：

   ```pwsh
   Get-AzResourceGroup -Name 'az104-09a*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注意**：该命令以异步方式执行（由 -AsJob 参数决定），因此，虽然你随后可在同一 PowerShell 会话中立即运行另一个 PowerShell 命令，但实际上要花几分钟才能删除资源组。

#### 回顾

在本实验室中，你已：

- 创建 Azure Web 应用
- 创建暂存部署槽位
- 配置 Web 应用部署设置
- 将代码部署到了暂存部署槽位
- 交换过渡槽
- 配置并测试 Azure Web 应用的自动缩放
