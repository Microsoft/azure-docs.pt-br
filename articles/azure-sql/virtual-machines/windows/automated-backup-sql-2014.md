---
title: Backup Automatizado para máquinas virtuais do SQL Server 2014 no Azure
description: Explica o recurso de Backup Automatizado para VMs do SQL Server 2014 em execução no Azure. Este artigo é específico para VMs que usam o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41add54ce767413982ab0503f7263c58aed4d4e2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359278"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup Automatizado para máquinas virtuais do SQL Server 2014 (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016/2017](automated-backup.md)

O backup automatizado configura automaticamente o [Backup Gerenciado do Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups normais de banco de dados que utilizam o armazenamento durável de Blobs do Azure. O Backup Automatizado depende da [Extensão SQL Server IaaS (infraestrutura como serviço) Agent](sql-server-iaas-agent-extension-automate-management.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Backup Automatizado, considere os seguintes pré-requisitos:


**Sistema operacional**:

- Windows Server 2012 e superior 

**Versão/edição do SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!NOTE]
> Para o SQL 2016 e superior, consulte [backup automatizado para SQL Server 2016](automated-backup.md).

**Configuração do banco de dados**:

- Os bancos de dados de _usuário_ de destino devem usar o modelo de recuperação completa. Os bancos de dados do sistema não precisam usar o modelo de recuperação completa. No entanto, se você precisar que os backups de log sejam considerados para o Modelo ou MSDB, será necessário usar o modelo de recuperação completa. Para obter mais informações sobre o impacto do modelo de recuperação completa em backups, veja [Backup com o modelo de recuperação completa](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105)). 
- A VM SQL Server foi registrada com a extensão do agente IaaS do SQL no [modo de gerenciamento completo](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full). 
-  O backup automatizado depende da extensão completa do [agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md). Assim, o backup automatizado só tem suporte em bancos de dados de destino da instância padrão ou em uma única instância nomeada. Se não houver nenhuma instância padrão e várias instâncias nomeadas, a extensão de IaaS do SQL falhará e o backup automatizado não funcionará. 

## <a name="settings"></a>Configurações

A tabela a seguir descreve as opções que podem ser configuradas para Backup Automatizado. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

| Configuração | Intervalo (Padrão) | Descrição |
| --- | --- | --- |
| **Backup Automatizado** | Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de Retenção** | Um a 30 dias (30 dias) | O número de dias para manter um backup. |
| **Conta de armazenamento** | Conta de Armazenamento do Azure | Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, hora e nome do computador. |
| **Criptografia** | Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner `automaticbackup` usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores. |
| **Senha** | Texto da senha | Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |


## <a name="configure-new-vms"></a>Configurar novas VMs

Use o portal do Azure para configurar o Backup Automatizado ao criar uma máquina virtual do SQL Server 2014 no modelo de implantação do Resource Manager.

Na guia **Configurações do SQL Server**, role a página para baixo até **Backup automatizado** e selecione **Habilitar**. A captura de tela do portal do Azure a seguir mostra as configurações do **Backup Automatizado do SQL**.

![Configuração do Backup Automatizado do SQL no portal do Azure](./media/automated-backup-sql-2014/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Configurar VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as VMs existentes do SQL Server, você pode habilitar e desabilitar backups automatizados, alterar o período de retenção, especificar a conta de armazenamento e habilitar a criptografia no portal do Azure. 

Procure o [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) da sua máquina virtual do SQL Server 2014 e selecione **Backups**. 

![Backup Automatizado do SQL para VMs existentes](./media/automated-backup-sql-2014/azure-sql-rm-autobackup-existing-vms.png)

Quando terminar, selecione o botão **Aplicar** na parte inferior da página **Backups** para salvar as alterações.

Se você for habilitar o Backup Automatizado pela primeira vez, o Azure configurará o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure refletirá as novas configurações.

> [!NOTE]
> Você também pode configurar o Backup Automatizado usando um modelo. Para obter mais informações, consulte o [Modelo de início rápido do Azure para o Backup Automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Configurar com o PowerShell

Você pode usar o PowerShell para configurar o Backup Automatizado. Antes de começar, faça o seguinte:

- [Baixe e instale o Azure PowerShell mais recente](https://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta com o comando **Connect-AzAccount**. 

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>Instalar a Extensão IaaS do SQL Server
Se você tiver provisionado uma VM do SQL Server no portal do Azure, a Extensão IaaS do SQL Server já deverá estar instalada. Você pode determinar se ela está instalada para a sua VM chamando o comando **Get-AzVM** e examinando a propriedade **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se a extensão do agente IaaS do SQL Server estiver instalada, você deverá vê-la listada como "SqlIaaSAgent" ou "SQLIaaSExtension". **ProvisioningState** para a extensão também deve mostrar "Êxito".

Se ela não estiver instalada ou o provisionamento tiver falhado, você poderá instalá-la com o comando a seguir. Além do grupo de recursos e do nome da VM, você também deve especificar a região ( **$region**) em que a VM está localizada. Especifique o tipo de licença para a sua VM do SQL Server escolhendo entre pagamento conforme o uso ou traga sua própria licença por meio do [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Para obter mais informações sobre licenciamento, confira [Modelo de licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md). 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reiniciará o SQL Server.

### <a name="verify-current-settings"></a><a id="verifysettings"></a> Verificar as configurações atuais

Se você habilitou o backup automatizado durante o provisionamento, poderá usar o PowerShell para verificar a configuração atual. Execute o comando **Get-AzVMSqlServerExtension** e examine a propriedade **AutoBackupSettings**:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Você deve ver um resultado semelhante ao seguinte:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Se a saída mostrar que **Enable** está definido como **False**, você precisará habilitar o backup automatizado. A boa notícia é que você habilita e configura o Backup Automatizado da mesma maneira. Confira a próxima seção para obter mais informações.

> [!NOTE] 
> Se você verificar as configurações imediatamente depois de fazer uma alteração, será possível que você obtenha os valores de configuração antigos. Aguarde alguns minutos e verifique as configurações novamente para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup"></a>Configurar o Backup Automatizado
Você pode usar o PowerShell para habilitar o Backup Automatizado, bem como para modificar sua configuração e comportamento a qualquer momento.

Primeiro, selecione ou crie uma conta de armazenamento para os arquivos de backup. O script a seguir seleciona uma conta de armazenamento ou a cria se ela não existir.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> O Backup Automatizado não dá suporte ao armazenamento de backups no Armazenamento Premium, mas ele pode fazer backups de discos de VM que usam o Armazenamento Premium.

Em seguida, use o comando **New-AzVMSqlServerAutoBackupConfig** para habilitar e definir as configurações do Backup Automatizado e armazenar backups na conta de armazenamento do Azure. Neste exemplo, os backups são retidos para ser mantidos por 10 dias. O segundo comando, **Set-AzVMSqlServerExtension**, atualiza a VM do Azure especificada com essas configurações.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

> [!NOTE]
> Há outras configurações para **New-AzVMSqlServerAutoBackupConfig** que se aplicam somente ao SQL Server 2016 e ao Backup Automatizado v2. O SQL Server 2014 não dá suporte às seguintes configurações: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** e **LogBackupFrequencyInMinutes**. Se você tentar definir essas configurações em uma máquina virtual do SQL Server 2014, não haverá erro, mas as configurações não serão aplicadas. Caso deseje usar essas configurações em uma máquina virtual do SQL Server 2016, confira [Backup Automatizado v2 para máquinas virtuais do SQL Server 2016 no Azure](automated-backup.md).

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro **EnableEncryption** e uma senha (cadeia de caracteres segura) para o parâmetro **CertificatePassword**. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar que as configurações estão aplicadas, [verifique a configuração do Backup Automatizado](#verifysettings).

### <a name="disable-automated-backup"></a>Desabilitar o Backup automatizado

Para desabilitar o Backup Automatizado, execute o mesmo script sem o parâmetro **-Enable** para o comando **New-AzVMSqlServerAutoBackupConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo

O script a seguir fornece um conjunto de variáveis que você pode personalizar para habilitar e configurar o Backup Automatizado para sua VM. No seu caso, convém personalizar o script de acordo com seus requisitos. Por exemplo, você teria que fazer alterações desejasse desabilitar o backup de bancos de dados do sistema ou habilitar a criptografia.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Monitoramento

Para monitorar o Backup Automatizado no SQL Server 2014, há duas opções principais. Como o Backup Automatizado usa o recurso de Backup gerenciado do SQL Server, as mesmas técnicas de monitoramentos se aplicam a ambos.

Primeiro, você poderá sondar o status chamando [msdb.smart_admin.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Ou consultar a função [msdb.smart_admin.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) com valor de tabela.

> [!NOTE]
> O esquema para o Backup Gerenciado no SQL Server 2014 é **msdb.smart_admin**. No SQL Server 2016 isso alterou para **msdb.managed_backup** e os tópicos de referência utilizam este esquema mais recente. Mas para o SQL Server 2014, você deve continuar a usar o esquema **smart_admin** para todos os Objetos de Backup gerenciados.

Outra opção é aproveitar o recurso integrado Database Mail para notificações.

1. Chame o [msdb.smart_admin.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) procedimento armazenado para atribuir um endereço de email para parâmetro **SSMBackup2WANotificationEmailIds**. 
1. Habilitar [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) para enviar os emails da VM do Azure.
1. Use o nome de usuário e o servidor SMTP para configurar o Database Mail. Você pode configurar o Database Mail no SQL Server Management Studio ou com comandos Transact-SQL. Para obter mais informações, consulte [Database Mail](/sql/relational-databases/database-mail/database-mail).
1. [Configurar o SQL Server Agent para usar o Database Mail](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Verifique se a porta SMTP é permitida por meio do firewall da VM local e o grupo de segurança de rede para a VM.

## <a name="next-steps"></a>Próximas etapas

O Backup Automatizado configura o Backup Gerenciado em VMs do Azure. Portanto, é importante [ler a documentação do Backup Gerenciado no SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure).

Você pode encontrar outras orientações de backup e de restauração para o SQL Server em VMs do Azure no seguinte artigo: [Backup e restauração de máquinas virtuais SQL Server no Azure](backup-restore.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, veja [Visão geral do SQL Server em máquinas virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).