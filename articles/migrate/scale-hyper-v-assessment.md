---
title: Avaliar grandes números de VMs Hyper-V para a migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar grandes números de VMs Hyper-V para migração para o Azure usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811538"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Avaliar grandes números de VMs Hyper-V para a migração para o Azure

Este artigo descreve como avaliar grandes números (> 1000) de VMs do Hyper-V locais para migração para o Azure, usando a ferramenta de avaliação de servidor de migrar do Azure.

[As migrações para Azure](migrate-services-overview.md) fornece um hub de ferramentas que ajudam você a descobrir, avaliar e migrar cargas de trabalho, infraestrutura e aplicativos para o Microsoft Azure. O hub inclui ferramentas de migrações para Azure e as ofertas do ISV (fornecedor) independentes de software de terceiros. 


Neste artigo, você aprenderá a:
> [!div class="checklist"]
> * Plano de avaliação em escala.
> * Configurar permissões do Azure e, em seguida, prepare o Hyper-V para avaliação.
> * Criar um projeto de migrações para Azure e criar uma avaliação.
> * Examine a avaliação durante o planejamento para a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar duas VMs antes de avaliar em grande escala, siga nosso [série de tutoriais](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plano para avaliação

Ao planejar a avaliação de grande número de VMs Hyper-V, há algumas coisas a considerar:

- **Planejar projetos de migrações para Azure**: Descubra como implantar projetos do migrações para Azure. Por exemplo, se seus data centers estão em regiões diferentes, ou você precisa armazenar a descoberta, avaliação ou metadados relacionados à migração em uma geografia diferente, talvez seja necessário vários projetos.
- **Planejar soluções**: As migrações para Azure usam um dispositivo migrações para Azure de local, implantado como uma VM do Hyper-V, para descobrir continuamente as VMs para avaliação e migração. O dispositivo monitora as alterações de ambiente como a adição de VMs, discos ou adaptadores de rede. Ele também envia dados de desempenho e metadados sobre elas para o Azure. Você precisará descobrir quantos dispositivos para implantar.


## <a name="planning-limits"></a>Planejamento de limites
 
Use os limites resumidos nesta tabela para o planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos de migração do Azure** | Avalie até 10.000 VMs em um projeto.
**Dispositivo de migrações para Azure** | Um dispositivo pode descobrir VMs até 5000.<br/> Um dispositivo pode se conectar aos hosts do Hyper-V até 300.<br/> Um dispositivo só pode ser associado um único projeto de migrações para Azure.<br/><br/> 
**Avaliação de migração do Azure** | Você pode avaliar até 10.000 VMs em uma única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do dispositivo, você precisa selecionar cada host Hyper-V. 
- Se você estiver executando um ambiente multilocatário, você não pode descobrir no momento, apenas as VMs que pertencem a um locatário específico. 

## <a name="prepare-for-assessment"></a>Preparar para a avaliação

Prepare o Azure e do Hyper-V para avaliação de servidor. 

1. Verifique se [limitações e requisitos de suporte do Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurar permissões para sua conta do Azure interagir com as migrações para Azure
3. Preparar VMs e hosts Hyper-V

Siga as instruções em [este tutorial](tutorial-prepare-hyper-v.md) para definir essas configurações.

## <a name="create-a-project"></a>Criar um projeto

Acordo com suas necessidades de planejamento, faça o seguinte:

1. Crie projetos de migrações para Azure.
2. Adicione a ferramenta de avaliação de servidor de migrar do Azure para os projetos.

[Saiba mais](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Criar e revisar uma avaliação

1. Crie avaliações para VMs do Hyper-V.
1. Examine as avaliações em preparação para o planejamento da migração.

[Saiba mais](tutorial-assess-hyper-v.md) sobre como criar e revisar as avaliações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migrações para Azure para VMs do Hyper-V
> * Preparado do Azure e Hyper-V para avaliação
> * Criou um projeto de migrações para Azure e executou avaliações
> * Revisadas as avaliações em preparação para migração.

Agora, [Saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar avaliações](how-to-modify-assessment.md)
