---
title: Modelos do Azure Resource Manager
description: Azure Resource Manager modelos para usar Azure Site Recovery recursos.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: 0477045ac48ee2746e3d6a1dd95051673412ffee
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551193"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modelos de Azure Resource Manager para Azure Site Recovery

A tabela a seguir inclui links para modelos de Azure Resource Manager para uso de Azure Site Recovery recursos.

| Modelo | Descrição |
|---|---|
|**Azure para Azure** | |
| [Criar um cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/site-recovery/quickstart-create-vault-template)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser usado no Backup do Azure e no Azure Site Recovery. |
| [Habilitar a replicação para VMs do Azure](https://aka.ms/asr-arm-enable-replication) | Habilite a replicação para VMs do Azure usando o cofre existente e as configurações de destino personalizadas.|
| [Disparar failover e proteger novamente](https://aka.ms/asr-arm-failover-reprotect) | Disparar uma operação de failover e proteger novamente para um conjunto de VMs do Azure. |
| [Executar um fluxo de recuperação de desastre de ponta a ponta para VMs do Azure](https://aka.ms/asr-arm-e2e-flow) | Inicie um fluxo completo de recuperação de desastres de ponta a ponta (habilite a replicação + failover e proteja novamente + failback e proteja novamente) para VMs do Azure, também chamado de fluxo de 540 °.|
|   |   |