---
title: Matriz de suporte a migrações do Azure
description: Fornece um resumo das configurações de suporte e limitações para o serviço migrações para Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811551"
---
# <a name="azure-migrate-support-matrix"></a>Matriz de suporte a migrações do Azure

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar as máquinas para a nuvem do Microsoft Azure. Este artigo resume as configurações gerais de suporte e limitações para cenários de migrações para Azure e implantações.


## <a name="azure-migrate-versions"></a>Versões de migrações do Azure

Há duas versões do serviço migrações para Azure:

- **Versão atual**: Usando esta versão, que você pode criar novos projetos de migrações para Azure, descubra local avalia e orquestrar as avaliações e migrações. [Saiba mais](whats-new.md#azure-migrate-new-version).
- **Versão anterior**: Para o cliente usando a versão anterior das migrações para Azure (somente avaliação de VMs do VMware local tinha suporte), agora você deve usar a versão atual. Na versão anterior, você não pode criar novos projetos do migrações para Azure ou executar novas descobertas.

## <a name="supported-migration-scenarios"></a>Cenários de migração com suporte

A tabela resume os cenários de migração com suporte.

**Implantação** | **Detalhes*** 
--- | --- 
**Avaliação de locais** | Avalie cargas de trabalho local e dados em execução em VMs VMware e VMs do Hyper-V. Avalie usando o Microsoft Data Migration Assistant (DMA) e avaliação de servidor de migrar do Azure, bem como ferramentas de terceiros que incluem a Cloudamize, Tech Corent e Turbonomic Server.
**Migração do local para o Azure** | Migre cargas de trabalho e os dados em execução em servidores físicos, máquinas virtuais VMware, VMs do Hyper-V e em instâncias do AWS/GCP, para o Azure. Migrar usando o serviço de migração de banco de dados do Azure (DMS) e de avaliação de servidor de migrar do Azure e, bem como as ferramentas de terceiros que incluem Carbonite e CorentTech.

Suporte de ferramenta específica é resumido da seguinte maneira.

**Ferramenta** | **Avaliação/migração** | **Detalhes**
--- | --- | ---
Avaliação de servidor migrações para Azure | Avaliação | Experimente a avaliação de servidor para [Hyper-V](tutorial-prepare-hyper-v.md) e [VMware](tutorial-prepare-vmware.md).
Cloudamize | Avaliação | [Saiba mais](https://www.cloudamize.com/platform#tab-0).
CorentTech | Avaliação | [Saiba mais](https://www.corenttech.com/).
Turbonomic | Avaliação | [Saiba mais](https://turbonomic.com/solutions/technologies/azure-cloud/).
Migração do servidor migrações para Azure | Migração | Experimente a migração do servidor para [Hyper-V](tutorial-migrate-hyper-v.md) e [VMware](tutorial-migrate-vmware.md).
Carbonite | Migração | [Saiba mais](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure).
CorentTech | Migração | [Saiba mais](https://www.corenttech.com/).


## <a name="azure-migrate-projects"></a>Projetos de migração do Azure

**Suporte** | **Detalhes**
--- | ---
Assinatura | Você pode ter um único projeto de migrações para Azure em uma assinatura.
Permissões do Azure | Você precisa de permissões de Colaborador ou proprietário na assinatura para criar um projeto de migrações para Azure.
VMs VMware  | Avalie até 35.000 VMs do VMware em um único projeto.
VMs Hyper-V | Avalie até 10.000 VMs Hyper-V em um único projeto.

Um projeto pode incluir VMs VMware e VMs do Hyper-V, até os limites de avaliação.


## <a name="vmware-assessment-and-migration"></a>Migração e avaliação do VMware

[Revisão](migrate-support-matrix-vmware.md) a avaliação de servidor de migrar do Azure e a migração de servidor de matriz de suporte para VMs do VMware.

## <a name="hyper-v-assessment-and-migration"></a>Migração e avaliação do Hyper-V

[Revisão](migrate-support-matrix-hyper-v.md) a avaliação de servidor de migrar do Azure e a migração de servidor de matriz de suporte para VMs do Hyper-V.


## <a name="next-steps"></a>Próximas etapas

- [Avaliar as VMs do VMware](tutorial-assess-vmware.md) para a migração.
- [Avaliar as VMs do Hyper-V](tutorial-assess-hyper-v.md) para a migração.

