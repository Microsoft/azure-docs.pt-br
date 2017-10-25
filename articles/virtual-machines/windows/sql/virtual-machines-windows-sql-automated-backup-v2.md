---
title: "Backup Automatizado v2 para máquinas virtuais do Azure do SQL Server 2016 | Microsoft Docs"
description: "Explica o recurso de Backup Automatizado para VMs do SQL Server 2016 em execução no Azure. Este artigo é específico para VMs que usam o Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/05/2017
ms.author: jroth
ms.openlocfilehash: e7e14b0243f82c672392d5ab4bb6aca01156465b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automated-backup-v2-for-sql-server-2016-azure-virtual-machines-resource-manager"></a>Backup Automatizado v2 para máquinas virtuais do Azure do SQL Server 2016 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

O Backup Automatizado v2 configura automaticamente o [Backup Gerenciado no Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2016 Standard, Enterprise ou Developer Editions. Isso permite que você configure backups regulares do banco de dados que utilizam o durável armazenamento de Blobs do Azure. O Backup Automatizado v2 depende da [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Backup Automatizado v2, examine os seguintes pré-requisitos:

**Sistema operacional**:

- Windows Server 2012 R2
- Windows Server 2016

**Versão/edição do SQL Server**:

- SQL Server 2016 Standard
- SQL Server 2016 Enterprise
- SQL Server 2016 Developer

> [!IMPORTANT]
> O Backup Automatizado v2 funciona com o SQL Server 2016. Se você estiver usando o SQL Server 2014, poderá usar o Backup Automatizado v1 para fazer backup de seus bancos de dados. Para obter mais informações, veja [Backup Automatizado para máquinas virtuais do Azure no SQL Server 2014](virtual-machines-windows-sql-automated-backup.md).

**Configuração do banco de dados**:

- Os bancos de dados de destino devem usar o modelo de recuperação completa. Para obter mais informações sobre o impacto do modelo de recuperação completa em backups, consulte [Backup com o modelo de recuperação completa](https://technet.microsoft.com/library/ms190217.aspx).
- Os bancos de dados do sistema não precisam usar o modelo de recuperação completa. No entanto, se você precisar que os backups de log sejam considerados para o Modelo ou MSDB, será necessário usar o modelo de recuperação completa.
- Os bancos de dados de destino devem estar na instância padrão do SQL Server. A extensão de IaaS do SQL Server não oferece suporte a instâncias nomeadas.

**Modelo de implantação do Azure**:

- Gerenciador de Recursos

> [!NOTE]
> O Backup Automatizado conta com a **Extensão do agente IaaS do SQL Server**. As imagens atuais da galeria da máquina virtual do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para o Backup Automatizado v2. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

### <a name="basic-settings"></a>Configurações Básicas

| Configuração | Intervalo (Padrão) | Descrição |
| --- | --- | --- |
| **Backup Automatizado** | Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2016 Standard ou Enterprise. |
| **Período de retenção** | Um a 30 dias (30 dias) | O número de dias para manter os backups. |
| **Conta de armazenamento** | Conta de Armazenamento do Azure | Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, a hora e o GUID do banco de dados. |
| **Criptografia** |Habilitar/desabilitar (Desabilitado) | Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificada no mesmo contêiner **automaticbackup** usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores. |
| **Senha** |Texto da senha | Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |

### <a name="advanced-settings"></a>Configurações avançadas

| Configuração | Intervalo (Padrão) | Descrição |
| --- | --- | --- |
| **Backups de Banco de Dados do Sistema** | Habilitar/desabilitar (Desabilitado) | Quando habilitado, esse recurso também fará backup dos bancos de dados do sistema: Mestre, MSDB e Modelo. Para os bancos de dados Modelo e MSDB, verifique se eles estão no modo de recuperação completa se desejar que os backups de log sejam executados. Os backups de log nunca são feitos para o Mestre. E não é feito nenhum backup para o TempDB. |
| **Agendamento de Backup** | Manual/Automatizado (Automatizado) | Por padrão, o agendamento de backup será automaticamente determinado com base no crescimento do log. O agendamento de backup manual permite que o usuário especifique a janela de tempo para backups. Nesse caso, os backups ocorrerão apenas na frequência especificada e durante a janela de tempo especificada de um determinado dia. |
| **Frequência do backup completo** | Diariamente/Semanalmente | Frequência de backups completos. Em ambos os casos, os backups completos serão iniciados durante a próxima janela de tempo agendada. Quando Semanalmente estiver selecionado, os backups poderão abranger vários dias até que todos os bancos de dados tenham o backup realizado com êxito. |
| **Hora de início do backup completo** | 00:00 – 23:00 (01:00) | A hora de início de um determinado dia durante o qual os backups completos podem ocorrer. |
| **Janela de tempo do backup completo** | 1 – 23 horas (1 hora) | A duração da janela de tempo de um determinado dia durante o qual os backups completos podem ocorrer. |
| **Frequência de backup do log** | 5 – 60 minutos (60 minutos) | Frequência de backups de log. |

## <a name="understanding-full-backup-frequency"></a>Noções básicas sobre a frequência do backup completo
É importante compreender a diferença entre backups completos diários e semanais. Nesse esforço, abordaremos dois cenários de exemplo.

### <a name="scenario-1-weekly-backups"></a>Cenário 1: backups semanais
Você tem uma VM do SQL Server que contém um número de bancos de dados muito grandes.

Na segunda-feira, você habilita o Backup Automatizado v2 com as seguintes configurações:

- Agendamento de backup: **Manual**
- Frequência do backup completo: **Semanalmente**
- Hora de início do backup completo: **01:00**
- Janela de tempo do backup completo: **1 hora**

Isso significa que a próxima janela de backup disponível é terça-feira à 1h por 1 hora. Nesse momento, o Backup Automatizado começará a fazer backup de seus bancos de dados um de cada vez. Nesse cenário, os bancos de dados são grandes o suficiente de forma que os backups completos serão concluídos para os primeiros bancos de dados. No entanto, após uma hora nem todos os bancos de dados terão tido o backup realizado.

Quando isso acontecer, o Backup Automatizado começará a fazer o backup dos bancos de dados restantes no dia seguinte, quarta-feira à 1h por 1 hora. Se nem todos os bancos de dados tiverem tido o backup realizado nesse momento, ele tentará novamente no dia seguinte no mesmo horário. Isso continuará até que todos os bancos de dados tenham tido o backup realizado com êxito.

Quando chegar terça-feira novamente, o Backup Automatizado começará a fazer o backup de todos os bancos de dados novamente.

Este cenário mostra que o Backup Automatizado operará apenas dentro da janela de tempo especificada e cada banco de dados terá o backup feito uma vez por semana. Isso também mostra que é possível que os backups abranjam vários dias no caso de não ser possível concluir todos os backups em um único dia.

### <a name="scenario-2-daily-backups"></a>Cenário 2: backups diários
Você tem uma VM do SQL Server que contém um número de bancos de dados muito grandes.

Na segunda-feira, você habilita o Backup Automatizado v2 com as seguintes configurações:

- Agendamento de backup: Manual
- Frequência do backup completo: Diariamente
- Hora de início do backup completo: 22:00
- Janela de tempo do backup completo: 6 horas

Isso significa que a próxima janela de backup disponível é segunda-feira às 22h por 6 horas. Nesse momento, o Backup Automatizado começará a fazer backup de seus bancos de dados um de cada vez.

Em seguida, na terça-feira às 22h por 6 horas, os backups completos de todos os bancos de dados serão iniciados novamente.

> [!IMPORTANT]
> Ao agendar backups diários, é recomendável que você agende uma janela de tempo ampla para garantir que todos os bancos de dados possam ter o backup realizado dentro desse período. Isso é especialmente importante no caso em que você tem uma grande quantidade de dados para backup.

## <a name="configuration-in-the-portal"></a>Configuração no Portal

Você pode usar o Portal do Azure para configurar o Backup Automatizado v2 durante o provisionamento ou para as VMs do SQL Server 2016 existentes.

### <a name="new-vms"></a>Novas VMs

Use o portal do Azure para configurar o Backup Automatizado v2 quando criar uma nova Máquina Virtual do SQL Server 2016 no modelo de implantação do Resource Manager. 

Na folha **Configurações do SQL Server**, selecione **Backup Automatizado**. A captura de tela do portal do Azure a seguir mostra a folha **Backup Automatizado do SQL** .

![Configuração de Backup Automatizado do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> O Backup Automatizado v2 está desabilitado por padrão.

Para ter contexto, consulte o tópico completo sobre [provisionamento de uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

Para máquinas virtuais existentes do SQL Server, selecione sua máquina virtual do SQL Server. Selecione a seção **Configuração do SQL Server** da folha **Configurações**.

![Backup Automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

Na folha **Configuração do SQL Server**, clique no botão **Editar** na seção de Backup Automatizado.

![Configurar o Backup Automatizado do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Quando terminar, clique no botão **OK** na parte inferior da folha **Configuração do SQL Server** para salvar suas alterações.

Se você for habilitar o Backup Automatizado pela primeira vez, o Azure configurará o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que o Backup Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure refletirá as novas configurações.

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell

Você pode usar o PowerShell para configurar o Backup Automatizado v2. Antes de começar, faça o seguinte:

- [Baixe e instale o Azure PowerShell mais recente](http://aka.ms/webpi-azps).
- Abra o Windows PowerShell e associe-o à sua conta. Você pode fazer isso seguindo as etapas na seção [Configurar sua assinatura](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) do tópico de provisionamento.

### <a name="install-the-sql-iaas-extension"></a>Instalar a Extensão IaaS do SQL Server
Se você tiver provisionado uma máquina virtual do SQL Server no Portal do Azure, a Extensão IaaS do SQL Server já deverá estar instalada. Você pode determinar se ele está instalado para a sua VM chamando o comando **Get-AzureRmVM** e examinando a propriedade **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Se a extensão do agente IaaS do SQL Server estiver instalada, você deverá vê-lo listado como “SqlIaaSAgent” ou “SQLIaaSExtension”. **ProvisioningState** para a extensão também deve mostrar "Êxito". 

Se ele não estiver instalado ou o provisionamento tiver falhado, você poderá instalá-lo com o comando a seguir. Além do grupo de recursos e do nome da VM, você também deve especificar a região (**$region**) em que a VM está localizada.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Verificar as configurações atuais
Se você habilitou o backup automatizado durante o provisionamento, poderá usar o PowerShell para verificar a configuração atual. Execute o comando **Get-AzureRmVMSqlServerExtension** e examine a propriedade **AutoBackupSettings**:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Você deve ver um resultado semelhante ao seguinte:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

Se a saída mostrar que **Enable** está definido como **False**, você precisará habilitar o backup automatizado. A boa notícia é que você habilita e configura o Backup Automatizado da mesma maneira. Confira a próxima seção para obter mais informações.

> [!NOTE] 
> Se você verificar as configurações imediatamente depois de fazer uma alteração, será possível que você obtenha os valores de configuração antigos. Aguarde alguns minutos e verifique as configurações novamente para se certificar de que as alterações foram aplicadas.

### <a name="configure-automated-backup-v2"></a>Configurar o Backup Automatizado v2
Você pode usar o PowerShell para habilitar o Backup Automatizado, bem como para modificar sua configuração e comportamento a qualquer momento. 

Primeiro, selecione ou crie uma conta de armazenamento para os arquivos de backup. O script a seguir seleciona uma conta de armazenamento ou a cria se ela não existir.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> O Backup Automatizado não dá suporte ao armazenamento de backups no Armazenamento Premium, mas ele pode fazer backups de discos de VM que usam o Armazenamento Premium.

Em seguida, use o comando **New-AzureRmVMSqlServerAutoBackupConfig** para habilitar e definir as configurações do Backup Automatizado v2 para armazenar backups na conta de armazenamento do Azure. Neste exemplo, os backups são definidos para ser mantidos por 10 dias. Os backups de banco de dados do sistema estão habilitados. Os backups completos são agendados para serem realizados semanalmente com uma janela de tempo com início às 20h por duas horas. Os backups de log estão agendados para a cada 30 minutos. O segundo comando, **Set-AzureRmVMSqlServerExtension**, atualiza a VM do Azure especificada com essas configurações.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server. 

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro **EnableEncryption** e uma senha (cadeia de caracteres segura) para o parâmetro **CertificatePassword**. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Para confirmar que as configurações estão aplicadas, [verifique a configuração do Backup Automatizado](#verifysettings).

### <a name="disable-automated-backup"></a>Desabilitar o Backup automatizado
Para desabilitar o Backup Automatizado, execute o mesmo script sem o parâmetro **-Enable** para o comando **New-AzureRmVMSqlServerAutoBackupConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script de exemplo
O script a seguir fornece um conjunto de variáveis que você pode personalizar para habilitar e configurar o Backup Automatizado para sua VM. No seu caso, convém personalizar o script de acordo com seus requisitos. Por exemplo, você teria que fazer alterações desejasse desabilitar o backup de bancos de dados do sistema ou habilitar a criptografia.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Próximas etapas
O Backup Automatizado v2 configura o Backup Gerenciado em VMs do Azure. Portanto, é importante [ler a documentação do Backup Gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e suas implicações.

Você pode encontrar outras orientações de backup e de restauração para o SQL Server em VMs do Azure no seguinte tópico: [Backup e restauração do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

