---
title: Avaliar grandes números de servidores no ambiente Hyper-V para migração para o Azure com migrações para Azure | Microsoft Docs
description: Descreve como avaliar um grande número de servidores no ambiente Hyper-V para migração para o Azure usando o serviço migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780286"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Avaliar um grande número de servidores no ambiente Hyper-V para migração para o Azure

Este artigo descreve como avaliar grandes números de servidores locais no ambiente do Hyper-V para migração para o Azure, usando a ferramenta de avaliação e descoberta de migrações para Azure.

As [Migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam você a descobrir, avaliar e migrar aplicativos, a infraestrutura e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas das Migrações para Azure e ofertas de ISV (fornecedor independente de software) de terceiros. 


Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Planeje a avaliação em escala.
> * Configure as permissões do Azure e prepare o Hyper-V para avaliação.
> * Crie um projeto de migrações para Azure e crie uma avaliação.
> * Examine a avaliação conforme planeja a migração.


> [!NOTE]
> Se você quiser experimentar uma prova de conceito para avaliar alguns servidores antes de avaliar em escala, siga nossa [série de tutoriais](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Planejar a avaliação

Ao planejar a avaliação de um grande número de servidores no ambiente Hyper-V, há algumas coisas a considerar:

- **Planejar projetos de migrações para Azure**: Descubra como implantar projetos de migrações para Azure. Por exemplo, se seus data centers estiverem em geografias diferentes, ou se você precisar armazenar metadados relacionados à migração, à avaliação ou ao migrar em uma geografia diferente, talvez precise de vários projetos.
- **Dispositivos de plano**: as migrações para Azure usam um dispositivo de migrações do Azure local, implantado como uma VM do Hyper-V, para descobrir continuamente os servidores para avaliação e migração. O dispositivo monitora as alterações de ambiente, como adicionar servidores, discos ou adaptadores de rede. Ele também envia metadados e dados de desempenho sobre eles para o Azure. Você precisa descobrir quantos dispositivos implantar.


## <a name="planning-limits"></a>Limites de planejamento
 
Use os limites resumidos nesta tabela para planejamento.

**Planejamento** | **Limites**
--- | --- 
**Projetos das Migrações para Azure** | Avalie até 35.000 servidores em um projeto.
**Dispositivo de Migrações para Azure** | Um dispositivo pode descobrir até 5000 servidores.<br/> Um dispositivo pode se conectar a até 300 hosts do Hyper-V.<br/> Um dispositivo só pode ser associado a um único projeto de migrações para Azure.<br/> Qualquer número de dispositivos pode ser associado a um único projeto de migrações para Azure. <br/><br/> 
**Grupo** | Você pode adicionar até 35.000 servidores em um único grupo.
**Avaliação de migrações para Azure** | Você pode avaliar até 35.000 servidores em uma única avaliação.



## <a name="other-planning-considerations"></a>Outras considerações de planejamento

- Para iniciar a descoberta do dispositivo, você precisa selecionar cada host Hyper-V. 
- Se você estiver executando um ambiente multilocatário, não será possível descobrir atualmente apenas os servidores que pertencem a um locatário específico. 

## <a name="prepare-for-assessment"></a>Preparar-se para avaliação

Prepare o Azure e o Hyper-V para a ferramenta de descoberta e avaliação: 

1. Verifique [os requisitos de suporte e as limitações do Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurar permissões para sua conta do Azure para interagir com as migrações para Azure
3. Preparar servidores e hosts Hyper-V

Siga as instruções neste [tutorial](./tutorial-discover-hyper-v.md) para definir essas configurações.

## <a name="create-a-project"></a>Criar um projeto

De acordo com seus requisitos de planejamento, faça o seguinte:

1. Criar projetos de migrações para Azure.
2. Adicione a ferramenta de avaliação e descoberta de migrações para projetos do Azure.

[Saiba mais](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Criar e examinar uma avaliação

1. Crie Avaliações para servidores no ambiente Hyper-V.
1. Examine as avaliações em preparação para o planejamento de migração.

[Saiba mais](tutorial-assess-hyper-v.md) sobre como criar e revisar avaliações.
    

## <a name="next-steps"></a>Próximas etapas

Neste artigo você:
 
> [!div class="checklist"] 
> * Planejado para dimensionar as avaliações de migração do Azure para servidores no ambiente do Hyper-V
> * Azure e Hyper-V preparados para avaliação
> * Criou um projeto de migrações para Azure e executou avaliações
> * Avaliações revisadas em preparação para a migração.

Agora, [saiba como](concepts-assessment-calculation.md) as avaliações são calculadas e como [modificar as avaliações](how-to-modify-assessment.md)