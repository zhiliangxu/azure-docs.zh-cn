---
title: 使用 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 68f4250c2a2a6bed1a1e21dc444e93cc87b6f59b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 版本配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本 AzureRM PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持特定日期的配置文件版本（例如 **2017-03-09-profile**），而 Azure 则支持**最新的** API 版本配置文件。 安装配置文件时，会安装与指定的配置文件对应的 AzureRM PowerShell 模块。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安装使用 API 版本配置文件所需的 PowerShell 模块

可通过 PowerShell 库获得的 **AzureRM.Bootstrapper** 模块会提供使用 API 版本配置文件所需的 PowerShell cmdlet。 使用以下 cmdlet 安装 AzureRM.Bootstrapper 模块：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
AzureRM.Bootstrapper 模块目前为预览版；详细信息和功能即时可能会更改。 若要从 PowerShell 库下载并安装此模块的最新版本，请运行以下 cmdlet：

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>安装配置文件

使用 **Install-AzureRmProfile** cmdlet 搭配 **2017-03-09-profile** API 版本配置文件，安装 Azure Stack 所需的 AzureRM 模块。 请注意，Azure Stack 操作员模块不会连同此 API 版本配置文件一起安装，应该根据[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 一文的步骤 3 中所述，单独安装这些模块。

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>安装并导入配置文件中的模块

使用 **Use-AzureRmProfile** cmdlet 安装并导入与 API 版本配置文件关联的模块。 在一个 PowerShell 会话中只能导入一个 API 版本配置文件。 若要导入不同的 API 版本配置文件，必须打开新的 PowerShell 会话。 Use-AzureRMProfile cmdlet 运行以下任务：  
1. 检查当前范围中是否已安装与所指定 API 版本配置文件关联的 PowerShell 模块。  
2. 下载并安装这些模块（如果尚未安装）。   
3. 将模块导入到当前的 PowerShell 会话中。 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

若要从某个 API 版本配置文件安装并导入选定的 AzureRM 模块，请搭配 **Module** 参数运行 Use-AzureRMProfile cmdlet：

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>获取已安装的配置文件

使用 **Get-AzureRmProfile** cmdlet 获取可用的 API 版本配置文件列表： 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>更新配置文件

使用 **Update-AzureRmProfile** cmdlet 将 API 版本配置文件中的模块更新为 PSGallery 中可用的最新版模块。 建议在导入模块时，始终在新的 PowerShell 会话中运行 **Update-AzureRmProfile** cmdlet，以避免发生冲突。 Update-AzureRmProfile cmdlet 运行以下任务：

1. 检查当前范围的给定 API 版本配置文件中是否已安装最新版模块。  
2. 提示安装这些模块（如果尚未安装）。  
3. 将已更新的模块安装并导入到当前的 PowerShell 会话中。  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

若要在更新成最新可用版本之前，先删除先前安装的模块版本，请搭配 **-RemovePreviousVersions** 参数使用 Update-AzureRmProfile cmdlet：

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

此 cmdlet 运行以下任务：  

1. 检查当前范围的给定 API 版本配置文件中是否已安装最新版模块。  
2. 从当前的 API 版本配置文件和当前 PowerShell 会话中删除旧版模块。  
4. 提示安装最新版本。  
5. 将已更新的模块安装并导入到当前的 PowerShell 会话中。  
 
## <a name="uninstall-profiles"></a>卸载配置文件

使用 **Uninstall-AzureRmProfile** cmdlet 卸载指定的 API 版本配置文件。

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>后续步骤
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](user/azure-stack-powershell-configure-user.md)  
