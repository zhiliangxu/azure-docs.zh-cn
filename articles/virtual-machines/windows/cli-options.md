---
title: 在 Windows 上使用 Azure CLI | Microsoft 文档
description: 在 Windows 上使用 Azure CLI
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
ms.openlocfilehash: 6eaad8bc4374cf84ee248e6f4b1be20fd4dfd955
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="using-the-azure-cli-on-windows"></a>在 Windows 上使用 Azure CLI

Azure 命令行接口 (CLI) 提供的命令行和脚本编写环境用于创建和管理 Azure 资源。 Azure CLI 适用于 macOS、Linux 和 Windows 操作系统。 在这些操作系统上，CLI 命令是相同的，但特定于操作系统的脚本语法可能有所不同。

本文档详细介绍可以在 Windows 上安装和运行 Azure CLI 的方法，并详细介绍每种方式的语法注意事项。 若要深入了解 Azure CLI 文档，请参阅 [Azure CLI 文档]( https://docs.microsoft.com/cli/azure)。

## <a name="windows-subsystem-for-linux"></a>适用于 Linux 的 Windows 子系统

适用于 Linux 的 Windows 子系统 (WSL) 在 Windows 10 周年版及更高版本上提供 Ubuntu Linux 环境。 启用后，WSL 提供本机 Bash 体验，可用于创建和运行 Azure CLI 脚本。 由于 WSL 提供本机 Bash 体验，因此无需修改即可在 macOS、Linux 和 Windows 之间共享 Azure CLI 脚本。

若要在 WSL 中使用 Azure CLI，请完成以下步骤。

|任务 | 说明 |
|---|---|
| 启用 WSL | [安装 WSL 文档 ](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| 安装 Azure CLI |[在 WSL/Ubuntu 14.04 上安装 CLI](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

可以在 Windows 中本机运行 Azure CLI。 在此配置中，Azure CLI 程序包将安装在 Windows 操作系统上，并可以从 PowerShell 运行命令。 在此配置中，Azure CLI 命令和脚本可以运行在任何受支持的 Windows 版本上，但需要特定于平台的脚本语法。 因此，脚本在未修改的情况下不一定能在 macOS、Linux 和 Windows 之间共享。

若要在 Windows 上使用 Azure CLI，请使用[在 Windows 上安装 CLI](https://docs.microsoft.com/cli/azure/install-az-cli2#windows) 中的说明安装程序包。

## <a name="docker-image"></a>Docker 映像

使用 Docker for Windows 时，可以启动包含 Azure CLI 的 Docker 映像。 此映像基于 Linux，并且提供本机 Bash 体验。  使用 Docker for Windows 和 Azure CLI 映像时，会在 macOS、Linux 和 Windows 之间共享脚本。 

若要在 Docker for Windows 上使用 Azure CLI，请确保 Docker for Windows 正在运行，并运行以下命令。

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

完成后，将启动使用 Azure CLI 工具预加载的 Bash 会话。

## <a name="next-steps"></a>后续步骤

[用于 Azure 虚拟机的 CLI 示例](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[用于 Azure Web 应用的 CLI 示例](../../app-service/app-service-cli-samples.md)

[用于 Azure SQL 的 CLI 示例](../../sql-database/sql-database-cli-samples.md)
