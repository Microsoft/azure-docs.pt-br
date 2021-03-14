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
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720521"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modelos de Azure Resource Manager para Azure Site Recovery

A tabela a seguir inclui links para modelos de Azure Resource Manager para uso de Azure Site Recovery recursos.

| Modelo | Descrição |
|---|---|
|**Azure para Azure** | |
| [Criar um cofre dos Serviços de Recuperação](./quickstart-create-vault-template.md)| Crie um cofre dos Serviços de Recuperação. O cofre pode ser usado no Backup do Azure e no Azure Site Recovery. |
| [Habilitar a replicação para VMs do Azure](https://aka.ms/asr-arm-enable-replication) | Habilite a replicação para VMs do Azure usando o cofre existente e as configurações de destino personalizadas.|
| [Disparar failover e proteger novamente](https://aka.ms/asr-arm-failover-reprotect) | Disparar uma operação de failover e proteger novamente para um conjunto de VMs do Azure. |
| [Executar um fluxo de recuperação de desastre de ponta a ponta para VMs do Azure](https://aka.ms/asr-arm-e2e-flow) | Inicie um fluxo completo de recuperação de desastres de ponta a ponta (habilite a replicação + failover e proteja novamente + failback e proteja novamente) para VMs do Azure, também chamado de fluxo de 540 °.|
|   |   |