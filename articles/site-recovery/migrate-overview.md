---
title: Comparar migrações e Site Recovery do Azure para migração para o Azure
description: Resume as vantagens de usar as migrações para migração do Azure, em vez de Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844313"
---
# <a name="migrating-to-azure"></a>Migrar para o Azure

Para a migração, recomendamos que você use o serviço de migrações para Azure para migrar VMs e servidores para o Azure, em vez do serviço de Azure Site Recovery. [Saiba mais](../migrate/migrate-services-overview.md) sobre as migrações para Azure.


## <a name="why-use-azure-migrate"></a>Por que usar Migrações para Azure?

O uso de migrações para migração do Azure fornece várias vantagens:
 
 
- As migrações para Azure fornecem um hub centralizado para descoberta, avaliação e migração para o Azure.
- Usar as migrações para Azure fornece interoperabilidade e extensibilidade futura com as ferramentas de migração do Azure, outros serviços do Azure e ferramentas de terceiros.
- O migrações para Azure: a ferramenta de migração de servidor é desenvolvida especificamente para a migração de servidor para o Azure. Ele é otimizado para migração. Você não precisa aprender sobre os conceitos e cenários que não são diretamente relevantes para a migração. 
- Não há cobranças de uso de ferramentas para migração por 180 dias, a partir do momento em que a replicação é iniciada para uma VM. Isso lhe dá tempo para concluir a migração. Você paga apenas pelos recursos de armazenamento e de rede usados na replicação e pelos encargos de computação consumidos durante as migrações de teste.
- As migrações para Azure dão suporte a todos os cenários de migração com suporte pelo Site Recovery. Além disso, para VMs VMware, as migrações para Azure fornecem uma opção de migração sem agente.
- Estamos priorizando novos recursos de migração para as migrações para Azure: ferramenta de migração de servidor somente. Esses recursos não são destinados a Site Recovery.

## <a name="when-to-use-site-recovery"></a>Quando usar Site Recovery?

Site Recovery deve ser usado:

- Para a recuperação de desastre de computadores locais no Azure.
- Para a recuperação de desastre de VMs do Azure, entre regiões do Azure.

Embora seja recomendável usar as migrações para Azure para migrar servidores locais para o Azure, se você já tiver iniciado sua jornada de migração com o Site Recovery, poderá continuar a usá-lo para concluir a migração.  

## <a name="next-steps"></a>Próximas etapas

> [Veja perguntas comuns](../migrate/resources-faq.md) sobre as Migrações para Azure.
