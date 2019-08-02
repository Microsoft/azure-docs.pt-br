---
title: Modelos do Azure Resource Manager para o Backup do Azure
description: Amostras do PowerShell do Backup do Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 0675d511fefba000ee6db4b45202bd1c72522cfe
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639611"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modelos do Azure Resource Manager para o Backup do Azure

A tabela a seguir contém links para modelos do Azure Resource Manager para uso nos cofres dos Serviços de Recuperação e nos recursos do Backup do Azure. Para saber mais sobre a sintaxe JSON e as propriedades, confira [Tipos de recurso de Microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Cofre dos Serviços de Recuperação** | |
| [Criar um cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser usado no Backup do Azure e no Azure Site Recovery. |
|**Backup de máquinas virtuais**| |
| [Fazer back de VMs do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Use o cofre dos Serviços de Recuperação e a política de backup existentes para fazer o backup de máquinas virtuais do Gerenciador de Recursos no mesmo grupo de recursos.|
| [Fazer backup de VMs IaaS no cofre dos Serviços de Recuperação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modelo de backup clássico e máquinas virtuais do Gerenciador de Recursos. |
| [Criar política de backup semanal para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | O modelo cria um cofre dos Serviços de Recuperação e uma política de backup semanal que são usados para fazer backup de máquinas virtuais do Resource Manager e clássicas.|
| [Criar política de backup diário para VMs IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | O modelo cria um cofre dos Serviços de Recuperação e uma política de backup diário que são usados para fazer backup de máquinas virtuais do Resource Manager e clássicas.|
| [Implantar VM do Windows Server com backup habilitado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | O modelo cria uma VM do Windows Server e um cofre dos Serviços de Recuperação com a política de backup padrão habilitada.|
|**Monitorar trabalhos de backup** |  |
| [Use os logs do Azure Monitor com o Backup do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | O modelo implanta os logs do Azure Monitor com o Backup do Azure, que permite que você monitore os trabalhos de backup e restauração, alertas de backup e armazenamento em Nuvem usado em seus cofres dos Serviços de Recuperação.|  
|**Fazer backup do SQL Server na VM do Azure** |  |
| [Fazer backup do SQL Server na VM do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | O modelo cria um cofre dos Serviços de Recuperação e uma carga de trabalho específica da Política de Backup. Ele registra a VM com o serviço de Backup do Azure e configura a proteção na VM. Atualmente, ele só funciona para imagens da Galeria do SQL. |
|   |   |
