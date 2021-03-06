---
title: 用于部署 Windows HPC 群集的 PowerShell 脚本 | Microsoft Docs
description: 运行 PowerShell 脚本，以在 Azure 虚拟机中部署 Windows HPC Pack 2012 R2 群集
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: e05562aeac0ea89ec1c3d80d2967c8f59c68d332
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>使用 HPC Pack IaaS 部署脚本创建 Windows 高性能计算 (HPC) 群集
运行 HPC Pack IaaS 部署 PowerShell 脚本，以便在 Azure 虚拟机中部署适用于 Windows 工作负荷的完整 HPC Pack 2012 R2 群集。 群集包含运行 Windows Server 和 Microsoft HPC Pack 的已加入 Active Directory 的头节点以及指定的其他 Windows 计算资源。 如果想要在 Azure 中部署适用于 Linux 工作负荷的 HPC Pack 群集，请参阅[使用 HPC Pack IaaS 部署脚本创建 Linux HPC 群集](../../linux/classic/hpcpack-cluster-powershell-script.md)。 

> [!IMPORTANT] 
> 本文中介绍的 PowerShell 脚本使用经典部署模型在 Azure 中创建 Microsoft HPC Pack 2012 R2 群集。 Microsoft 建议大多数新部署使用资源管理器模型。
> 此外，本文中所述的脚本不支持 HPC Pack 2016。 有关用于 HPC Pack 2012 R2 和 HPC Pack 2016 的资源管理器模板的信息，请参阅 [Azure 中的 HPC Pack 群集部署选项](../hpcpack-cluster-options.md)。

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>示例配置文件
在下面的示例中，将订阅 ID 或名称以及帐户和服务名称替换成自己的值。

### <a name="example-1"></a>示例 1
以下配置文件将部署 1 个 HPC Pack 群集，其中包含 1 个具有本地数据库的头节点和 5 个运行 Windows Server 2012 R2 操作系统的计算节点。 所有云服务直接在“美国西部”位置创建。 头节点充当域林的域控制器。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>示例 2
以下配置文件会在现有域林中部署一个 HPC Pack 群集。 该群集包含 1 个具有本地数据库的头节点和 12 个应用了 BGInfo VM 扩展的计算节点。
对于域林中的所有 VM，禁用了 Windows 更新的自动安装。 所有云服务直接在“亚洲东部”位置创建。 计算节点在 3 个云服务和 3 个存储帐户中创建：*MyHPCCNService01* 和 *mycnstorage01* 中的 *MyHPCCN-0001* 到 *MyHPCCN-0005*；*MyHPCCNService02* 和 *mycnstorage02* 中的 *MyHPCCN-0006* 到 *MyHPCCN0010*；*MyHPCCNService03* 和 *mycnstorage03* 中的 *MyHPCCN-0011* 到 *MyHPCCN-0012*。 计算节点是基于从计算节点捕获的现有专用映像创建的。 已启用自动增长和收缩服务，该服务采用默认的增长和收缩间隔。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>示例 3
以下配置文件会在现有域林中部署一个 HPC Pack 群集。 该群集包含 1 个头节点、 1 个具有 500 GB 数据磁盘的数据库服务器、2 个运行 Windows Server 2012 R2 操作系统的代理节点，以及 5 个运行 Windows Server 2012 R2 操作系统的计算节点。 云服务 MyHPCCNService 是在地缘组 *MyIBAffinityGroup* 中创建的，其他云服务是在地缘组 *MyAffinityGroup* 中创建的。 已在头节点上启用了 HPC 作业计划程序 REST API 和 HPC Web 门户。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>示例 4
以下配置文件会在现有域林中部署一个 HPC Pack 群集。 该群集包含 2 个具有本地数据库的头节点，此外将创建 2 个 Azure 节点模板并为 Azure 节点模板 *AzureTemplate1* 创建 3 个中等大小的 Azure 节点。 配置头节点后，在其上运行脚本文件。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>故障排除
* **“VNet 不存在”错误** - 如果运行脚本以便在 Azure 中的一个订阅下同时部署多个群集，则一个或多个部署可能会失败并显示错误“VNet *VNet\_Name* 不存在”。
  如果发生此错误，请对失败的部署再次运行该脚本。
* **从 Azure 虚拟网络访问 Internet 时出现问题** - 如果使用部署脚本创建具有新域控制器的群集，或将头节点 VM 手动提升为域控制器，则将 VM 连接到 Internet 时可能会遇到问题。 如果已在域控制器上自动配置转发器 DNS 服务器，但此转发器 DNS 服务器未正确解析，则会出现此问题。
  
    若要解决此问题，请登录到域控制器，删除转发器配置设置或配置一个有效的转发器 DNS 服务器。 要配置此设置，请在服务器管理器中单击“工具” >
    “DNS”打开 DNS 管理器，并双击“转发器”。
* **从计算密集型 VM 访问 RDMA 网络时出现问题** - 如果使用支持 RDMA 的大小（例如 A8 或 A9）添加 Windows Server 计算节点 VM 或代理节点 VM，则在将这些 VM 连接到 RDMA 应用程序网络时可能会遇到问题。 出现此问题的原因之一是，在将 VM 添加到群集时未正确安装 HpcVmDrivers 扩展。 例如，该扩展可能会停滞在安装状态。
  
    若要解决此问题，请先检查 VM 中扩展的状态。 如果该扩展未正确安装，请尝试从 HPC 群集中删除节点，再重新添加节点。 例如，可以通过使用 Add-HpcIaaSNode.ps1 脚本添加计算节点 VM。

## <a name="next-steps"></a>后续步骤
* 尝试在群集上运行测试工作负荷。 例如，请参阅 HPC Pack [入门指南](https://technet.microsoft.com/library/jj884144)。
* 有关编写群集部署脚本和运行 HPC 工作负荷的教程，请参阅[开始在 Azure 中使用 HPC Pack 群集运行 Excel 和 SOA 工作负荷](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 尝试使用 HPC Pack 的工具来启动、停止、添加和删除所创建群集中的计算节点。 请参阅[在 Azure 中管理 HPC Pack 群集的计算节点](hpcpack-cluster-node-manage.md)。
* 要完成设置以将本地计算机中的作业提交到群集，请参阅[将本地计算机中的 HPC 作业提交到 Azure 中的 HPC Pack 群集](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

