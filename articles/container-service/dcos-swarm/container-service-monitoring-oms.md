---
title: 监视 Azure DC/OS 群集 - 操作管理
description: 使用 Log Analytics 监视 Azure 容器服务 DC/OS 群集。
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: ba76f8480dedb37326505f7ed756eb51a41ee0fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>使用 Log Analytics 监视 Azure 容器服务 DC/OS 群集

Log Analytics 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护本地和云基础结构。 容器解决方案是 Log Analytics 中的一种解决方案，有助于查看单个位置中的容器库存、性能和日志。 通过查看集中位置中的日志，可以审核、排查容器问题，并查找主机上干扰性消耗过多的容器。

![](media/container-service-monitoring-oms/image1.png)

有关容器解决方案的详细信息，请参阅[容器解决方案 Log Analytics](../../log-analytics/log-analytics-containers.md)。

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>从 DC/OS“通用”中设置 Log Analytics


本文假设已设置 DC / OS，且已在群集上部署了简单的 Web 容器应用程序。

### <a name="pre-requisite"></a>先决条件
- [Microsoft Azure 订阅](https://azure.microsoft.com/free/) - 可免费获取。  
- Log Analytics 工作区设置 - 请参阅下面的“步骤 3”
- 安装 [DC/OS CLI](https://dcos.io/docs/1.8/usage/cli/install/)。

1. 在 DC/OS 仪表板中单击“通用”，并搜索“OMS”，如下所示。

![](media/container-service-monitoring-oms/image2.png)

2. 单击“安装”。 将看到包含版本信息并带有“安装包”或“高级安装”按钮的弹出窗口。 单击“高级安装”，可转到 **OMS 特定的配置属性**页面。

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. 需在此页面上输入 `wsid`（Log Analytics 工作区 ID）和 `wskey`（工作区 ID 的主键）。 若要获取 `wsid` 和 `wskey`，需要在 <https://mms.microsoft.com> 创建一个帐户。
请按照步骤创建帐户。 帐户创建完成后，依次单击“设置”、“连接源”和“Linux 服务器”，获取 `wsid` 和 `wskey`，如下所示。

 ![](media/container-service-monitoring-oms/image5.png)

4. 选择所需实例数，并单击“查看并安装”按钮。 通常情况下，所需实例数与代理群集中拥有的 VM 数相等。 适用于 Linux 的 OMS 代理作为单独的容器安装在每个 VM 上，其目的是收集用于监视和记录信息的信息。

## <a name="setting-up-a-simple-oms-dashboard"></a>设置简单的 OMS 仪表板

在 VM 上安装适用于 Linux 的 OMS 代理后，下一步需设置 OMS 仪表板。 可通过两种方法进行此设置：OMS 门户和 Azure 门户。

### <a name="oms-portal"></a>OMS 门户 

登录到 OMS 门户 (<https://mms.microsoft.com>) 并转到“解决方案库”。

![](media/container-service-monitoring-oms/image6.png)

进入“解决方案库”后，选择“容器”。

![](media/container-service-monitoring-oms/image7.png)

选择“容器解决方案”后，可在 OMS 概述仪表板页面上看到该磁贴。 将引入的容器数据编入索引后，可看到使用解决方案视图磁贴上的信息填充的磁贴。

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Azure 门户 

在 <https://portal.microsoft.com/> 登录到 Azure 门户。 转到“Marketplace”，选择“监视 + 管理”，并单击“查看全部”。 然后在搜索中键入 `containers`。 搜索结果中将显示“容器”。 选择“容器”，并单击“创建”。

![](media/container-service-monitoring-oms/image9.png)

单击“创建”后，将要求提供工作区。 选择工作区或创建新的工作区（如果没有）。

![](media/container-service-monitoring-oms/image10.PNG)

选择工作区后，单击“创建”。

![](media/container-service-monitoring-oms/image11.png)

有关 Log Analytics 容器解决方案的详细信息，请参阅[容器解决方案 Log Analytics](../../log-analytics/log-analytics-containers.md)。

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>如何使用 ACS DC/OS 缩放 OMS 代理 

如果安装的 OMS 代理需少于实际节点计数，或者通过添加更多 VM 扩展 VMSS，可通过缩放 `msoms` 服务实现。

可以转到 Marathon 或 DC/OS UI 服务选项卡，增加节点计数。

![](media/container-service-monitoring-oms/image12.PNG)

这会部署到尚未部署 OMS 代理的其他节点。

## <a name="uninstall-ms-oms"></a>卸载 MS OMS

若要卸载 MS OMS，请输入以下命令：

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>请告诉我们！！！
哪些内容有用？ 缺少了什么？ 关于此方面还有哪些内容比较有用？ 请在 <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a> 处告诉我们。

## <a name="next-steps"></a>后续步骤

 现在已完成设置，可使用 Log Analytics 监视容器，请[查看容器仪表板](../../log-analytics/log-analytics-containers.md)。
