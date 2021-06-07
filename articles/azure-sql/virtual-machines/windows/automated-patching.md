---
title: Aplicação de Patch automatizada VMs do SQL Server (Resource Manager) | Microsoft Docs
description: Este artigo descreve o recurso de Aplicação de Patch Automatizada para as máquinas virtuais do SQL Server em execução no Azure que usam o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 429fe39f84a54c22fa97178b85f417d76dc84a8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359465"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Aplicação de Patch Automatizada para máquinas virtuais do SQL Server no Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A Aplicação de Patch Automatizada estabelece uma janela de manutenção para uma máquina virtual do Azure que executa o SQL Server. Atualizações automáticas só podem ser instaladas durante esta janela de manutenção. Para o SQL Server, essa restrição garante que as atualizações do sistema e qualquer reinicialização associada ocorram no melhor momento possível para o banco de dados. 

> [!IMPORTANT]
> Somente as atualizações do Windows e do SQL Server marcadas como **Importantes** ou **Críticas** são instaladas. Outras atualizações do SQL Server, como service packs e atualizações cumulativas que não estão marcadas como **Importantes** ou **Críticas** precisam ser instaladas manualmente. 

A Aplicação de Patch Automatizada depende da [Extensão SQL Server IaaS (infraestrutura como serviço) Agent](sql-server-iaas-agent-extension-automate-management.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar a Aplicação de Patch Automatizada, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

**Versão do SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* Microsoft SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Instalar os comandos mais recentes do Azure PowerShell](/powershell/azure/) se você planeja configurar a Aplicação de Patch Automatizada com o PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> A aplicação de Patch automatizada depende da Extensão do Agente IaaS do SQL Server. As imagens atuais da galeria da máquina virtual do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [Extensão do agente IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md).
> 
> 

## <a name="settings"></a>Configurações
A tabela a seguir descreve as opções que podem ser configuradas para Aplicação de Patch Automatizada. As etapas de configuração reais variam dependendo de se você usar os comandos do Portal do Azure ou do Azure Windows PowerShell.

| Configuração | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de patch automatizada** |Habilitar/desabilitar (Desabilitado) |Habilita ou desabilita a Aplicação de Patch Automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira, sábado e domingo |A agenda para baixar e instalar atualizações do Windows, do SQL Server e do Microsoft para sua máquina virtual. |
| **Hora de início da manutenção** |0h a 24h |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para concluir o download e a instalação de atualizações. |
| **Categoria de patch** |Importante | A categoria de atualizações do Windows a ser baixada e instalada.|

## <a name="configure-in-the-azure-portal"></a>Configuração no portal do Azure
Você pode usar o portal do Azure para configurar a aplicação de Patch automatizada durante o provisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Use o portal do Azure para configurar a aplicação de patch automatizada ao criar uma máquina virtual do SQL Server no modelo de implantação do Resource Manager.

Na guia **Configurações do SQL Server**, selecione **Alterar configuração** em **Aplicação de patch automatizada**. As capturas de tela do portal do Azure a seguir mostram a folha **Aplicação de Patch Automatizada** .

![Aplicação de patch automatizada do SQL no portal do Azure](./media/automated-patching/azure-sql-arm-patching.png)

Para obter mais informações, confira [Provisionar uma máquina virtual do SQL Server no Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais existentes do SQL Server, abra seu [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selecione **Aplicação de patch** em **Configurações**. 

![Aplicação de Patch Automática do SQL para VMs existentes](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


Quando terminar, clique no botão **OK** na parte inferior da folha **Configuração do SQL Server** para salvar as alterações.

Se você for habilitar a Aplicação de Patch Automatizada pela primeira vez, o Azure configurará o Agente IaaS do SQL Server em segundo plano. Durante esse tempo, o portal do Azure não mostrará que a Aplicação de Patch Automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal do Azure reflete as novas configurações.

## <a name="configure-with-powershell"></a>Configurar com o PowerShell
Depois de provisionar sua VM do SQL, use o PowerShell para configurar a Aplicação de Patch Automatizada.

No exemplo a seguir, o PowerShell é usado para configurar a Aplicação de Patch Automatizada em uma VM existente do SQL Server. O comando **New-AzVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

```azurepowershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"
$aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s
Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação dela reiniciará o SQL Server.

Com base neste exemplo, a tabela a seguir descreve o efeito prático sobre a VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados toda quinta-feira. |
| **MaintenanceWindowStartingHour** |Inicia as atualizações às 11h. |
| **MaintenanceWindowsDuration** |Os patches devem ser instalados dentro de 120 minutos. Com base na hora de início, eles devem estar concluídos até 13h. |
| **PatchCategory** |A única configuração possível para esse parâmetro é **Important**. Isso instala as atualizações do Windows marcadas como Importantes; não instala as atualizações do SQL Server que não estão incluídas nessa categoria. |

Pode demorar vários minutos para instalar e configurar o Agente IaaS do SQL Server.

Para desabilitar a aplicação de patch automatizada, execute o mesmo script sem o parâmetro **-Enable** para **New-AzVMSqlServerAutoPatchingConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre outras tarefas de automação disponíveis, consulte [Extensão do agente IaaS do SQL Server](sql-server-iaas-agent-extension-automate-management.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, veja [Visão geral do SQL Server em máquinas virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

