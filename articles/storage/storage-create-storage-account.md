<properties
	pageTitle="如何在 Azure 门户中创建、管理或删除存储帐户 | Microsoft Azure"
	description="创建新的存储帐户、管理访问密钥，或删除 Azure 管理门户中的存储帐户。"
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags
	ms.service="storage"
	ms.date="02/14/2016"
	wacn.date=""/>


# 关于 Azure 存储帐户

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../includes/storage-selector-portal-create-storage-account.md)]

## 概述

Azure 存储帐户授予你访问 Azure 存储空间中的 Azure Blob、队列、表和文件服务的权限。你的存储帐户为你的 Azure 存储空间数据对象提供唯一的命名空间。默认情况下，只有你，即帐户所有者，才能使用你的帐户中的数据。

有两种类型的存储帐户：

- 标准存储帐户包括 Blob、表、队列和文件存储。 
- 高级存储帐户当前仅支持 Azure 虚拟机磁盘。有关高级存储的详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。

## 存储帐户计费

我们将根据你的存储帐户，针对你的 Azure 存储空间使用情况收费。存储成本取决于四个因素：存储容量、复制方案、存储事务和数据流出量。

- 存储容量指的是存储帐户中用来存储数据的配额。对数据进行简单存储时，其成本取决于存储的数据量和数据复制方式。
- 复制决定了你某一次的数据副本的保留数量，以及保留位置。
- 事务指的是对 Azure 存储空间的所有读取和写入操作。
- 数据流出量指的是传出某个 Azure 区域的数据。当不在同一区域中的应用程序访问你的存储帐户中的数据时，无论该应用程序是云服务还是某个其他类型的应用程序，都将会针对数据流出量向你收费。（对于 Azure 服务，你可以采取措施将你的数据和服务通过分组分到相同的数据中心内，从而降低或避免数据流出量费用。）  

[Azure 存储空间定价](/home/feature/storage#price)页提供了针对存储容量、复制和事务的详细定价信息。[数据传输定价详细信息](/pricing/details/data-transfers/)页提供了针对数据流出量的详细定价信息。

有关存储帐户容量和性能目标的详细信息，请参阅 [Azure 存储空间可伸缩性和性能目标](/documentation/articles/storage-scalability-targets)。

> [AZURE.NOTE] 当你创建 Azure 虚拟机时，如果在部署位置中还没有存储帐户，则会在该位置自动创建一个存储帐户。因此，没有必要按照下面的步骤来创建虚拟机磁盘的存储帐户。存储帐户名称将基于虚拟机名称。请参阅 [Azure 虚拟机文档](/documentation/services/virtual-machines/)以了解更多详细信息。

## 存储帐户终结点

存储在 Azure 存储空间中的每个对象都有唯一的 URL 地址。存储帐户名称构成该地址的子域。特定于每个服务的子域和域名的组合构成你的存储帐户的终结点。

例如，如果你的存储帐户名为 *mystorageaccount*，则你的存储帐户的默认终结点为：

- Blob 服务：http://*mystorageaccount*.blob.core.chinacloudapi.cn

- 表服务：http://*mystorageaccount*.table.core.chinacloudapi.cn

- 队列服务：http://*mystorageaccount*.queue.core.chinacloudapi.cn

- 文件服务：http://*mystorageaccount*.file.core.chinacloudapi.cn

用于访问存储帐户中某个对象的 URL 是通过将存储帐户中对象的位置附加到终结点而构建的。例如，Blob 地址可能具有以下格式：http://*mystorageaccount*.blob.core.chinacloudapi.cn/*mycontainer*/*myblob*。

此外还可以配置用于存储帐户的自定义域名称。有关经典存储帐户的详细信息，请参阅[为 Blob 存储终结点配置自定义域名](/documentation/articles/storage-custom-domain-name)。对于 ARM 存储帐户，此功能尚未添加到 [Azure 门户](https://manage.windowsazure.cn)，但你可以使用 PowerShell 配置它。有关详细信息，请参阅 [Set-AzureRmStorageAccount](https://msdn.microsoft.com/zh-cn/library/mt607146.aspx) cmdlet。

## 创建存储帐户

1. 登录到 [Azure 门户](https://manage.windowsazure.cn)。

2. 在“中心”菜单上，选择“新建”->“数据 + 存储”->“存储帐户”。

3. 选择部署模型：“资源管理器”或“经典”。建议使用“资源管理器”部署模型。有关详细信息，请参阅[了解资源管理器部署和经典部署](/documentation/articles/resource-manager-deployment-model)。

4. 输入你的存储帐户的名称。

	> [AZURE.NOTE] 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。
	>  
	> 你的存储帐户名称在 Azure 中必须是唯一的。Azure 门户将指出你选择的存储帐户名称是否已经存在。

	请参阅下面的[存储帐户终结点](#storage-account-endpoints)以了解如何使用存储帐户名称来定位 Azure 存储空间中你的对象。

5. 指定要创建的存储帐户的类型。存储帐户类型确定存储帐户的复制方式以及它是标准存储帐户还是高级存储帐户。

	默认存储帐户类型是 **Standard-RAGRS**，它是采用读取访问异地冗余复制的标准存储帐户。此类型的存储帐户复制到距主要区域数百英里的次要区域，并提供对该辅助位置的读取访问权限。

	有关 Azure 存储空间复制选项的详细信息，请参阅 [Azure 存储空间复制](/documentation/articles/storage-redundancy)。有关标准和高级存储帐户的详细信息，请参阅 [Azure 存储空间简介](/documentation/articles/storage-introduction)和[高级存储器：适用于 Azure 虚拟机工作负荷的高性能存储器](/documentation/articles/storage-premium-storage)

6. 指示你是否希望对存储帐户启用诊断。诊断包括存储分析日志记录和度量。

7. 如果你有多个 Azure 订阅，则会显示“订阅”字段。选择想在其中创建新存储帐户的订阅。

8. 指定新资源组或选择现有资源组。有关资源组的详细信息，请参阅[使用 Azure 门户管理 Azure 资源](/documentation/articles/resource-group-portal)。

9. 选择存储帐户的地理区域。

10. 单击“创建”以创建存储帐户。

## 管理存储访问密钥

当你创建存储帐户时，Azure 将生成两个 512 位存储访问密钥，用于在用户访问该存储帐户时对其进行身份验证。通过提供两个存储访问密钥，Azure 使你能够在不中断存储服务的情况下重新生成用于访问该服务的密钥。

> [AZURE.NOTE] 我们建议你避免与其他人共享你的存储访问密钥。若要允许不提供你的访问密钥即可访问存储空间资源，可使用共享访问签名。共享访问签名可用于访问你的帐户中的资源，访问时间间隔由你定义，访问权限由你指定。请参阅[共享访问签名：了解 SAS 模型](/documentation/articles/storage-dotnet-shared-access-signature-part-1)了解有关详细信息。

### 查看和复制存储访问密钥

在 [Azure 门户](https://manage.windowsazure.cn)中，导航到你的存储帐户，然后单击“密钥”图标以查看、复制和重新生成你的帐户访问密钥。“访问密钥”边栏选项卡还包含使用你的主密钥和辅助密钥预配置的连接字符串，可复制到应用程序中使用。

### 重新生成存储访问密钥

我们建议你定期更改存储帐户的访问密钥，以确保存储连接安全。分配了两个访问密钥，以便在你重新生成其中一个访问密钥时，始终能够使用另一个访问密钥连接到存储帐户。

> [AZURE.WARNING] 重新生成访问密钥会影响虚拟机、媒体服务以及任何依赖于存储帐户的应用程序。必须更新使用访问密钥访问存储帐户的所有客户端，以使用新密钥。

**虚拟机** - 如果你的存储帐户包含任何正在运行的虚拟机，则在重新生成访问密钥后必须重新部署所有虚拟机。若要避免重新部署，请在重新生成访问密钥之前关闭虚拟机。

**媒体服务** - 如果你的媒体服务依赖于存储帐户，则必须在重新生成密钥后将访问密钥与媒体服务重新同步。

**应用程序** - 如果你拥有使用存储帐户的 Web 应用程序或云服务，则重新生成密钥将失去连接，除非你滚动使用密钥。

下面是轮换存储访问密钥的过程：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。

2. 为你的存储帐户重新生成主访问密钥。在“访问密钥”边栏选项卡上，单击“重新生成密钥”，然后单击“是”以确认要生成新密钥。

3. 更新代码中的连接字符串以引用新的主访问密钥。

4. 以相同方式重新生成辅助访问密钥。

## 删除存储帐户

若要删除不再使用的存储帐户，请在 [Azure 门户](https://manage.windowsazure.cn)中导航到该存储帐户，然后单击“删除”。删除存储帐户将删除整个帐户，包括该帐户中的所有数据。

> [AZURE.WARNING] 无法恢复已删除的存储帐户，也无法检索删除之前该存储帐户包含的任何内容。请在删除帐户之前务必备份你想要保存的任何内容。对于帐户中的任意资源也是如此 - 一旦删除 Blob、表、队列或文件，则它将被永久删除。

若要删除与 Azure 虚拟机相关联的存储帐户，必须首先确保已删除所有虚拟机磁盘。如果不先删除虚拟机磁盘，则当你尝试删除存储帐户时，将看到如下错误消息：

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

如果存储帐户使用经典部署模型，你可以通过在[经典 Azure 门户](https://manage.windowsazure.cn)中执行以下步骤来删除虚拟机磁盘：

1. 导航到 [Azure 经典门户](https://manage.windowsazure.cn)。
2. 导航到“虚拟机”选项卡。
3. 单击“磁盘”选项卡。
4. 选择你的数据磁盘，然后单击“删除磁盘”。
5. 若要删除磁盘映像，请导航到“映像”选项卡，然后删除存储在帐户中的任何映像。

有关详细信息，请参阅 [Azure 虚拟机文档](/documentation/services/virtual-machines/)。

## 后续步骤

- [Azure 存储空间复制](/documentation/articles/storage-redundancy)
- [配置 Azure 存储空间连接字符串](/documentation/articles/storage-configure-connection-string)
- [使用 AzCopy 命令行实用程序传输数据](/documentation/articles/storage-use-azcopy)
- 访问 [Azure 存储空间团队博客](http://blogs.msdn.com/b/windowsazurestorage/)。

<!---HONumber=Mooncake_0405_2016-->