---
title: Migrar VMs do AWS para o Azure com Migrações para Azure
description: Este artigo descreve as opções para migrar instâncias do AWS para o Azure e recomenda Migrações para Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009051"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrar das VMs do AWS (Amazon Web Services) para o Azure

Este artigo descreve as opções para migrar instâncias de AWS (Amazon Web Services) para o Azure.

## <a name="migrate-with-azure-migrate"></a>Migrar com as Migrações para Azure

Recomendamos que você migre instâncias do EC2 do AWS para o Azure usando o serviço [Migrações para Azure](../migrate/migrate-services-overview.md). O recurso Migrações para Azure é criado especificamente para a migração do servidor. As Migrações para Azure oferecem um hub central para descoberta, avaliação e migração de computadores locais para o Azure.

[Saiba como](../migrate/tutorial-migrate-aws-virtual-machines.md) migrar instâncias da AWS com as Migrações para Azure. 


## <a name="migrate-with-site-recovery"></a>Migrar com o Site Recovery

O Site Recovery deve ser usado somente para recuperação de desastre, e não para migração.

Se já estiver usando o Azure Site Recovery e quiser dar continuidade a seu uso para a migração da AWS, siga as mesmas etapas que você usaria para configurar a [recuperação de desastre de computadores físicos](physical-azure-disaster-recovery.md).


> [!NOTE]
> Quando você executa um failover para recuperação de desastre, a última etapa é confirmar o failover. Quando você migra instâncias da AWS, a opção **Confirmar** não é relevante. Em vez disso, selecione a opção **Concluir Migração**. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Veja perguntas comuns](../migrate/resources-faq.md) sobre as Migrações para Azure.
