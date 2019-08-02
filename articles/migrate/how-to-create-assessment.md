---
title: Criar uma avaliação com a avaliação do servidor de migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação com a ferramenta de avaliação de servidor de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229096"
---
# <a name="create-an-assessment"></a>Criar uma avaliação

Este artigo descreve como criar uma avaliação para VMs VMware locais ou VMs do Hyper-V com migrações para Azure: Avaliação do servidor.

As migrações para [Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As migrações para Azure fornecem um hub centralizado para acompanhar a descoberta, avaliação e migração de infraestrutura, aplicativos e dados locais para o Azure. O Hub fornece ferramentas do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a migração do Azure: Ferramenta de avaliação do servidor.
- Para criar uma avaliação, você precisa configurar um dispositivo de migração do Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do servidor. [Saiba mais](migrate-appliance.md).


## <a name="assessment-overview"></a>Visão geral da avaliação
Há dois tipos de avaliações que você pode criar usando as migrações para Azure: Avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : Com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: Com base na configuração de tipo de armazenamento que você selecionar para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , em **migrações para Azure: Bloco avaliação** do servidor, clique em **avaliar**.

    ![Avaliar](./media/how-to-create-assessment/assess.png)

2. Em **avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Exibir tudo** para examinar as propriedades da avaliação.

    ![Propriedades da avaliação](./media/how-to-create-assessment//view-all.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione VMs para adicionar ao grupo.
5. Clique em **criar avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/how-to-create-assessment//assessment-create.png)

6. Após a criação da avaliação, exiba-a nos **servidores** > **migrações para Azure: **Avaliações**de**avaliação > do servidor.
7. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: Se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: A computação mensal estimada e os custos de armazenamento para executar as VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: Custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **metas** >  de migração**servidores**,  clique em avaliações **em migrações para Azure: Avaliação**do servidor.
2. Em **avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: As migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: Mostra problemas e correção sugerida.
    - **Preparação desconhecida**: Usado quando as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique em um status de **preparação do Azure** . Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento de VMs em execução no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e seus discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo é para executar as VMs locais como VMs de IaaS. A avaliação do servidor de migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo de armazenamento mensal. Essa exibição mostra os custos de armazenamento agregados para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações baseadas em desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é concedida.
- A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas




## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o [mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
