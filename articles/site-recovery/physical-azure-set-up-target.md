---
title: Configurar o ambiente de destino para a recuperação de desastre de servidores físicos locais no Azure | Microsoft Docs
description: Este artigo descreve como configurar o ambiente de destino do Azure para recuperação de desastre de servidores físicos usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 41220ccdca945610d7d8ca87af0857114e2cef85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949057"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar destino (VMware para Azure)

Este artigo descreve como preparar seu ambiente do Azure para iniciar a replicação de servidores físicos (x64) executando Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que:
- Você criou um Cofre dos Serviços de Recuperação para proteger seus servidores físicos. Você pode criar um Cofre dos Serviços de Recuperação no [Portal do Azure](https://portal.azure.com "Portal do Azure").
- Você [configurou seu ambiente local](physical-azure-disaster-recovery.md) para replicar servidores físicos no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir a **Etapa 1: Selecionar meta de proteção** e a **Etapa 2: Preparar origem**, você segue para a **Etapa 3: Destino**

![Preparar o destino](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Assinatura:** Mo menu suspenso, escolha a assinatura na qual você quer replicar seus servidores físicos.
2. **Modelo de Implantação:** Selecione o modelo de implantação (Clássico ou Resource Manager)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma conta de armazenamento compatível e a rede virtual na assinatura de destino na qual replicar e fazer failover dos servidores físicos.

Depois que as validações são concluídas com êxito, clique em OK de modo a passar para a próxima etapa.

Caso não tenha uma rede virtual ou conta de armazenamento do Resource Manager compatível, crie uma clicando nos botões **+ Conta de Armazenamento** ou **+ Rede** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas
[Definir configurações de replicação](vmware-azure-set-up-replication.md).
