<properties
	pageTitle="使用 PowerShell 在本地 VMM 站点与 Azure 之间自动提供保护"
	description="使用 PowerShell 自动部署 Azure Site Recovery。"
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.date="10/07/2015"
	wacn.date=""/>

#  使用 PowerShell 部署 Azure Site Recovery
Windows PowerShell® 是一种基于任务的命令行 shell 和脚本语言，专门用于系统管理。支持在 VMM 管理的 Hyper-V 站点与 Azure 之间为 Azure Site Recovery 使用 PowerShell cmdlet。

## 概述

Azure Site Recovery 可在许多部署方案中安排虚拟机的复制、故障转移和恢复，为业务连续性和灾难恢复 (BCDR) 策略发挥作用。有关部署方案的完整列表，请参阅 [Azure Site Recovery 概述](/documentation/articles/site-recovery-overview)。

本文说明如何使用 PowerShell 来自动完成有关部署 Azure Site Recovery 的常见任务，包括针对在位于 VMM 私有云中 Hyper-V 主机服务器上的虚拟机上运行的工作负载协调和自动实施保护。此方案使用 Hyper-V 副本将虚拟机从主 VMM 站点复制到 Azure。

本文将会介绍方案的先决条件，并说明如何设置 Site Recovery 保管库，在源 VMM 服务器上安装 Azure Site Recovery 提供程序，在保管库中注册服务器，添加 Azure 存储帐户，在 Hyper-V 主机服务器上安装 Azure 恢复服务代理，为 VMM 云配置将应用于所有受保护虚拟机的保护设置，然后为这些虚拟机启用保护。最后将测试故障转移以确保一切都正常工作。

如果在设置本方案时遇到问题，请将你的问题发布到 [Azure 恢复服务论坛](http://go.microsoft.com/fwlink/?LinkId=313628)。


## 开始之前

确保已满足以下先决条件：
### Azure 先决条件

- 需要一个 [Microsoft Azure](http://www.windowsazure.cn) 帐户。如果没有，请先使用[试用帐户](/price/1rmb-trial)。此外，你可以阅读 [Azure Site Recovery Manager 定价](/home/features/site-recovery/#price)。
- 你将需要一个 Azure 存储帐户来存储复制到 Azure 的数据。需要为帐户启用地域复制。该帐户应位于 Azure Site Recovery 服务所在的同一区域，并与同一订阅相关联。若要了解有关设置 Azure 存储的更多信息，请参阅 [Microsoft Azure 存储空间简介](/documentation/articles/storage-introduction)。
- 你将需要确保你要保护的虚拟机符合 Azure 要求。有关详细信息，请参阅[虚拟机支持](https://msdn.microsoft.com/zh-cn/library/azure/dn469078.aspx#BKMK_E2A)。

### VMM 先决条件
- 你需要具有运行 System Center 2012 R2 的 VMM 服务器。
- 包含你要保护的虚拟机的任何 VMM 服务器必须运行有 Azure Site Recovery 提供程序。这是在部署 Azure Site Recovery 期间安装的。
- 在 VMM 服务器上需要至少有一个你要保护的云。云应当包含：
	- 一个或多个 VMM 主机组。
	- 每个主机组中有一个或多个 Hyper-V 主机服务器或群集。
	- 源 Hyper-V 服务器上有一个或多个虚拟机。
- 了解有关设置 VMM 云的更多信息：
	- 在 [System Center 2012 R2 VMM 中私有云的新增功能](http://go.microsoft.com/fwlink/?LinkId=324952)及 [VMM 2012 和云](http://go.microsoft.com/fwlink/?LinkId=324956)中阅读有关私有 VMM 云的详细信息。
	- 了解有关[配置 VMM 云结构](https://msdn.microsoft.com/zh-cn/library/azure/dn469075.aspx#BKMK_Fabric)的更多信息
	- 在你的云结构元素就位后，通过[在 VMM 中创建私有云](https://technet.microsoft.com/zh-cn/library/jj860425.aspx)和[演练：使用 System Center 2012 SP1 VMM 创建私有云](http://go.microsoft.com/fwlink/?LinkId=324954)了解有关创建私有云的更多信息。

### Hyper-V 先决条件

- Hyper-V 主机服务器必须至少运行具有 Hyper-V 角色且安装了最新更新的 Windows Server 2012。
- 如果你在群集中运行 Hyper-V，请注意，如果你具有基于静态 IP 地址的群集，则不会自动创建群集代理。你需要手动配置群集代理。有关说明，请参阅[配置 Hyper-V 副本代理](https://technet.microsoft.com/zh-cn/library/jj134153.aspx#BKMK_1_4)。
- 你要为其管理保护的任何 Hyper-V 主机服务器或群集必须包括在 VMM 云中。

### 网络映射先决条件
当在 Azure 中保护虚拟机时，网络映射会在源 VMM 服务器上的 VM 网络与目标 Azure 网络之间进行映射以实现以下功能：

- 在同一网络上进行故障转移的所有计算机都可以彼此连接到对方，不管它们位于哪个恢复计划中。
- 如果在目标 Azure 网络上设置了网络网关，则虚拟机可以连接到其他本地虚拟机。
- 如果没有配置网络映射，则只有在同一恢复计划中进行故障转移的虚拟机能够在故障转移到 Azure 后彼此连接到对方。

如果希望部署网络映射，需要满足下列条件：

- 源 VMM 服务器上你要保护的虚拟机应当连接到某个 VM 网络。该网络应当该链接到与该云相关联的逻辑网络。
- 具有在故障转移后复制的虚拟机可以连接到的 Azure 网络。你将在故障转移时选择此网络。此网络应当与你的 Azure Site Recovery 订阅位于同一区域中。
- 了解有关网络映射的更多信息：
	- [在 VMM 中配置逻辑网络](https://technet.microsoft.com/zh-cn/library/jj721568.aspx)
	- [在 VMM 中配置 VM 网络和网关](https://technet.microsoft.com/zh-cn/library/jj721575.aspx)
	- [在 Azure 中配置和监视虚拟网络](/documentation/services/networking)

###PowerShell 必决条件
确保已将 Azure PowerShell 准备就绪。如果你已使用 PowerShell，则升级到 0.8.10 或更高版本。如需设置 PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)。安装并配置 PowerShell 后，可在[此处](https://msdn.microsoft.com/zh-cn/library/dn850420.aspx)查看该服务的所有可用 cmdlet。

若要了解可帮助你使用 cmdlet 的提示（如在 Azure PowerShell 中通常如何处理参数值、输入和输出），请参阅 [Azure Cmdlet 入门](https://msdn.microsoft.com/zh-cn/library/azure/jj554332.aspx)。

## 步骤 1：设置订阅 

在 PowerShell 中运行以下 cmdlet：



			$UserName = "<user@live.com>"
	$Password = "<password>"
	$AzureSubscriptionName = "prod_sub1"

	$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
	$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
	Add-AzureAccount -Credential $Cred;
	$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName


将“< >”中的元素替换为你的特定信息。

## 步骤 2：创建 Site Recovery 保管库

在 PowerShell 中，将“< >”中的元素替换为你的特定信息，然后运行以下命令：

```

	$VaultName = "<testvault123>"
	$VaultGeo  = "<China North>"
	$OutputPathForSettingsFile = "<c:>"

```


```
	New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
	$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## 步骤 3：生成保管库注册密钥

在保管库中生成一个注册密钥。在下载 Azure Site Recovery 提供程序并将其安装到 VMM 服务器上后，你将使用此密钥在保管库中注册 VMM 服务器。

1.	获取保管库设置文件并设置上下文：
	
	```
	
		$VaultName = "<testvault123>"
		$VaultGeo  = "<China North>"
		$OutputPathForSettingsFile = "<c:>"
	
		$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	通过运行以下命令设置保管库上下文：
	
	```	
		$VaultSettingFilePath = $vaultSetingsFile.FilePath 
		$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
```

## 步骤 4：安装 Azure Site Recovery 提供者

1.	在 VMM 计算机上，通过运行以下命令创建一个目录：
	
	```
	
		pushd C:\ASR\
	
	```
	
2. 通过运行以下命令，使用下载的提供者提取文件
	
	```
	
		AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3. 使用以下命令安装提供者：
	
	```
	
		.\SetupDr.exe /i
	
	```
	
	```
	
		$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
		do
		{
	                $isNotInstalled = $true;
	                if(Test-Path $installationRegPath)
	                {
	                                $isNotInstalled = $false;
	                }
		}While($isNotInstalled)
	
	```
	
	等待安装完成。
	
4. 使用以下命令在保管库中注册服务器：
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## 步骤 5：创建 Azure 存储帐户

如果你没有 Azure 存储帐户，请运行以下命令来创建启用异地复制的帐户：

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "China North"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

请注意，存储帐户必须位于 Azure Site Recovery 服务所在的同一区域，并与同一订阅相关联。


## 步骤 6：安装 Azure 恢复服务代理

通过 Azure 门户，在 VMM 云中要保护的每个 Hyper-V 主机服务器上安装 Azure 恢复服务代理。

在所有 VMM 主机上运行以下命令：

```

	marsagentinstaller.exe /q /nu

```


## 步骤 7：配置云保护设置

1.	通过运行以下命令在 Azure 中创建云保护配置文件：
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider 	HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName `
	-RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	通过运行以下命令获取保护容器：
	
	```
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	开始将保护容器与云相关联：
	
	```
	
		$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -	ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	在作业完成后，运行以下命令：

			$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
			if($job -eq $null -or $job.StateDescription -ne "Completed")
			{
				$isJobLeftForProcessing = $true;
			}
5. 在作业完成处理后，运行以下命令：

		if($isJobLeftForProcessing)
			{
			Start-Sleep -Seconds 60
			}
				}While($isJobLeftForProcessing)
	
	
若要检查作业是否完成，请遵循[监视活动](#monitor)中的步骤。

## 步骤 8：配置网络映射
在开始网络映射之前，请验证源 VMM 服务器上的虚拟机是否已连接到一个 VM 网络。此外，请创建一个或多个 Azure 虚拟机。注意，可以将多个 VM 网络映射到单个 Azure 网络。

请注意，如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。

第一条命令将获取当前 Azure Site Recovery 保管库的服务器。该命令将 Microsoft Azure Site Recovery 服务器存储在 $Servers 数组变量中。



	$Servers = Get-AzureSiteRecoveryServer


第二条命令将获取 $Servers 数组中第一个服务器的站点恢复网络。该命令在 $Networks 变量中存储网络。

```

	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

第三条命令使用 Get-AzuresubScription cmdlet 获取 Azure 订阅，然后将该值存储在 $Subscriptions 变量中。

```

	$Subscriptions = Get-AzureSubscription

```

第四条命令使用 Get-AzureVNetSite cmdlet 获取 Azure 虚拟网络，然后将该值存储在 $AzureVmNetworks 变量中。

```

	$AzureVmNetworks = Get-AzureVNetSite

```

最后一个 cmdlet 将在主网络与 Azure 虚拟机网络之间创建映射。该 cmdlet 将主网络指定为 $Networks 的第一个元素。该 cmdlet 使用虚拟机网络的 ID 将该网络指定为 $AzureVmNetworks 的第一个元素。该命令包含你的 Azure 订阅 ID。

```

PS C:\> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## 步骤 9：为虚拟机启用保护

在正确配置服务器、云和网络后，可以在云中为虚拟机启用保护。注意以下事项：

虚拟机必须满足 Azure 要求。可以在规划指南中的<a href="https://msdn.microsoft.com/zh-cn/library/dn469078.aspx">先决条件和支持</a>中查看这些要求。

若要启用保护，必须为虚拟机设置操作系统和操作系统磁盘属性。当你使用虚拟机模板在 VMM 中创建虚拟机时，可以设置属性。也可以在虚拟机属性的“常规”和“硬件配置”选项卡中为现有虚拟机设置这些属性。如果未在 VMM 中设置这些属性，可以在 Azure Site Recovery 门户中配置它们。


	
1.	若要启用保护，请运行以下命令以获取保护容器：
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. 通过运行以下命令获取保护实体 (VM)：
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
		```
			
3. 通过运行以下命令为 VM 启用 DR：

	
		$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity 	-Protection Enable -Force
	

## 测试你的部署

若要测试你的部署，可针对一台虚拟机运行测试故障转移，或者创建一个包括多个虚拟机的恢复计划并针对该计划运行测试故障转移。测试故障转移在隔离的网络中模拟你的故障转移和恢复机制。请注意：

- 如果想要在故障转移之后使用远程桌面连接到 Azure 中的虚拟机，请在虚拟机上启用远程桌面连接，然后运行测试故障转移。
- 故障转移后，你将要使用公共 IP 地址通过远程桌面连接到 Azure 中的虚拟机。如果要执行此操作，请确保没有任何域策略阻止你使用公共地址连接到虚拟机。

若要检查作业是否完成，请遵循[监视活动](#monitor)中的步骤。

### 创建恢复计划

1. 使用以下数据创建 .xml 文件作为恢复计划模板，然后将它保存为“C:\\RPTemplatePath.xml”。
2. 更改 RecoveryPlan 节点 ID、Name、PrimaryServerId 和 SecondaryServerId。
3. 更改 ProtectionEntity 节点 PrimaryProtectionEntityId（来自 VMM 的 vmid）。
4. 可以通过添加更多 ProtectionEntity 节点来添加更多 VM。
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
		#>
	
	```
	
5. 在模板中填充数据：
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
6. 创建 RecoveryPlan：
		
	```
		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;

	```
	
### 运行测试故障转移

1. 通过运行以下命令获取 RecoveryPlan 对象：
	
	```
	
		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2. 通过运行以下命令来启动测试故障转移：
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a>监视活动

使用以下命令来监视活动。请注意，必须在执行不同的作业之前等待处理完成。

```

Do
{
                $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
                Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
                if($job -eq $null -or $job.StateDescription -ne "Completed")
                {
                                $isJobLeftForProcessing = $true;
                }

```

```

if($isJobLeftForProcessing)
                {
                                Start-Sleep -Seconds 60
                }
}While($isJobLeftForProcessing)

```


##<a id="next" name="next" href="#next"></a>后续步骤
<UL>

<LI>有关 Azure Site Recovery PowerShell cmdlet 的详细信息，请参阅<a href="https://msdn.microsoft.com/zh-cn/library/dn850420.aspx">此文</a>。

<LI>若要在完全的生产环境中规划和部署 Azure Site Recovery，请参阅 <a href="https://msdn.microsoft.com/zh-cn/library/azure/dn469074.aspx">Azure Site Recovery 规划指南</a>和 <a href="https://msdn.microsoft.com/zh-cn/library/azure/dn168841.aspx">Azure Site Recovery 部署指南</a>。</LI>

<LI>如有问题，请访问 <a href="https://social.msdn.microsoft.com/Forums/azure/zh-CN/home?forum=windowsazurezhchs">Azure 恢复服务论坛</a>。</LI></UL>

<!---HONumber=79-->