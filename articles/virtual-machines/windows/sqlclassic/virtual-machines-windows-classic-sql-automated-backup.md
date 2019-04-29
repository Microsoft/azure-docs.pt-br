---
title: Backup Automatizado para Máquinas Virtuais do SQL Server (Clássico) | Microsoft Docs
description: 'Explica o recurso de Backup Automatizado para o SQL Server com Máquinas Virtuais do Azure usando o Resource Manager. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aeb97d661d330ed6afb3ca5e5e1eb924dacc4024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607708"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatizado para SQL Server em Máquinas Virtuais do Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Clássico](../classic/sql-automated-backup.md)
> 
> 

O backup automatizado configura automaticamente o [Backup Gerenciado do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os bancos de dados novos e existentes em uma VM do Azure executando o SQL Server 2014 Standard ou Enterprise. Isso permite que você configure backups regulares do banco de dados que utilizam o durável armazenamento de Blobs do Azure. O Backup Automatizado depende da [Extensão do agente IaaS do SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para exibir a versão do Resource Manager deste artigo, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure do Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o Backup Automatizado, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão/edição do SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> O Backup Automatizado para SQL Server 2016 é compatível com máquinas virtuais do Resource Manager. Para obter mais informações, consulte [Backup Automatizado v2 para SQL Server 2016 em Máquinas Virtuais do Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Configuração do banco de dados**:

* Os bancos de dados de destino devem usar o modelo de recuperação completa.

**Azure PowerShell**:

* [Instale os comandos mais recentes do Azure PowerShell](/powershell/azure/overview).

**Extensão IaaS do SQL Server**:

* [Instale a Extensão IaaS do SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para Backup Automatizado. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

| Configuração | Intervalo (Padrão) | DESCRIÇÃO |
| --- | --- | --- |
| **Backup Automatizado** |Habilitar/desabilitar (Desabilitado) |Habilita ou desabilita o Backup Automatizado de uma VM do Azure que executa o SQL Server 2014 Standard ou Enterprise. |
| **Período de retenção** |Um a 30 dias (30 dias) |O número de dias para manter um backup. |
| **Conta de armazenamento** |Conta de armazenamento do Azure (a conta de armazenamento criada para a VM especificada) |Uma conta de armazenamento do Azure a ser usada para armazenar arquivos de Backup Automatizado no armazenamento de blobs. Um contêiner é criado neste local para armazenar todos os arquivos de backup. A convenção de nomenclatura do arquivo de backup inclui a data, hora e nome do computador. |
| **Criptografia** |Habilitar/desabilitar (Desabilitado) |Habilita ou desabilita a criptografia. Quando a criptografia está habilitada, os certificados usados para restaurar o backup estão localizados na conta de armazenamento especificado no mesmo contêiner automaticbackup usando a mesma convenção de nomenclatura. Se a senha for alterada, um novo certificado será gerado com essa senha, mas o certificado antigo permanece para restaurar backups anteriores. |
| **Senha** |Texto da senha (nenhum) |Uma senha para as chaves de criptografia. Isso só é necessário se a criptografia estiver habilitada. Para restaurar um backup criptografado, você deverá ter a senha correta e o certificado relacionado que foi usado no momento em que o backup foi feito. |
| **Bancos de dados do sistema de backup** | Habilitar/desabilitar (Desabilitado) | Fazer backups completos do Mestre, Modelo e MSDB |
| **Configurar agendamento de backup** | Manual/Automatizado (Automatizado) | Selecione **Automatizado** para fazer backups e registrar logs completos com base no crescimento de log automaticamente. Selecione **Manual** para especificar o cronograma para backups e registros de log completos. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo do PowerShell a seguir, o Backup Automatizado é configurado para uma VM existente do SQL Server 2014. O comando **New-AzureVMSqlServerAutoBackupConfig** define as configurações de Backup Automatizado para armazenar backups na conta de armazenamento do Azure especificada pela variável $storageaccount. Esses backups serão mantidos por 10 dias. O comando **Set-AzureVMSqlServerExtension** atualiza a VM do Azure especificada com essas configurações.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para habilitar a criptografia, modifique o script anterior para passar o parâmetro EnableEncryption e uma senha (cadeia de caracteres segura) para o parâmetro CertificatePassword. O script a seguir habilita as configurações de Backup Automatizado no exemplo anterior e adiciona a criptografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desabilitar o backup automático, execute o mesmo script sem o parâmetro **-Enable** para **New-AzureVMSqlServerAutoBackupConfig**. Assim como acontece com a instalação, pode demorar vários minutos para desabilitar o Backup Automatizado.

> [!NOTE]
> Desabilitar e desinstalar o Agente IaaS do SQL Server não remove as configurações de Backup Gerenciado previamente configuradas. Você deve desabilitar o Backup Automatizado antes de desabilitar ou desinstalar o Agente IaaS do SQL Server.
> 
> 

## <a name="next-steps"></a>Próximas etapas
O Backup Automatizado configura o Backup Gerenciado em VMs do Azure. Portanto, é importante [ler a documentação do Backup Gerenciado](https://msdn.microsoft.com/library/dn449496.aspx) para entender o comportamento e suas implicações.

Você pode encontrar outras orientações de backup e de restauração para o SQL Server em VMs do Azure no seguinte tópico: [Backup e restauração para o SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral do SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

