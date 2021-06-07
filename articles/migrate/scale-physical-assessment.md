---
title: Avaliar grandes números de servidores físicos para migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar grandes números de servidores físicos para migração para o Azure usando o serviço migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 232475c50ab56fe6fb7a39a3497a8de3947fe851
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780303"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Avaliar grandes números de servidores físicos para migração para o Azure

Este artigo descreve como avaliar grandes números de servidores físicos locais para migração para o Azure, usando a ferramenta de avaliação e descoberta de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 


Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Planeje a avaliação em escala.
> * Configure as permissões do Azure e prepare servidores físicos para avaliação.
> * Crie um projeto de migrações para Azure e crie uma avaliação.
> * Examine a avaliação conforme planeja a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga nossa [série de tutoriais](./tutorial-discover-physical.md).

## <a name="plan-for-assessment"></a>Planejar a avaliação

Ao planejar a avaliação de um grande número de servidores físicos, há algumas coisas a considerar:

- **Planejar projetos de migrações para Azure**: Descubra como implantar projetos de migrações para Azure. Por exemplo, se seus data centers estiverem em geografias diferentes, ou se você precisar armazenar metadados relacionados à migração, à avaliação ou ao migrar em uma geografia diferente, talvez precise de vários projetos.
- **Dispositivos de plano**: as migrações para Azure usam um dispositivo de migrações do Azure local, implantado em um servidor Windows, para descobrir continuamente os servidores para avaliação e migração. O dispositivo monitora as alterações de ambiente, como adicionar servidores, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos dispositivos implantar.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos das Migrações para Azure** | Avalie até 35.000 servidores em um projeto.
**Dispositivo de Migrações para Azure** | Um dispositivo pode descobrir até 1000 servidores.<br/> Um dispositivo só pode ser associado a um único projeto de migrações para Azure.<br/> Qualquer número de dispositivos pode ser associado a um único projeto de migrações para Azure. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 servidores em um único grupo.
**Avaliação de migrações para Azure** | Você pode avaliar até 35.000 servidores em uma única avaliação.


## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do dispositivo, você precisa selecionar cada servidor físico. 

## <a name="prepare-for-assessment"></a>Preparar-se para avaliação

Prepare os servidores físicos e do Azure para a ferramenta de descoberta e avaliação:  

1. Verifique [os requisitos de suporte do servidor físico e as limitações](migrate-support-matrix-physical.md).
2. Configure as permissões para sua conta do Azure para interagir com as migrações para Azure.
3. Prepare os servidores físicos.

Siga as instruções neste [tutorial](./tutorial-discover-physical.md) para definir essas configurações.

## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar um projeto do Migrações para Azure.
2. Adicione a ferramenta de avaliação e descoberta de migrações para projetos do Azure.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e examinar uma avaliação

1. Crie Avaliações para servidores físicos.
1. Examine as avaliações em preparação para o planejamento de migração.

[Saiba mais](tutorial-assess-physical.md) sobre como criar e revisar avaliações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migração do Azure para servidores físicos.
> * Servidores físicos e Azure preparados para avaliação.
> * Criou um projeto de migrações para Azure e executou avaliações.
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar as avaliações](how-to-modify-assessment.md).