---
title: Criar uma avaliação de AVS com a avaliação do servidor de migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação de AVS com a ferramenta de avaliação de servidor de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fb1ec55bc68ccc323f8dee90982a9169e3085219
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567637"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Criar uma avaliação da AVS (solução do Azure VMware)

Este artigo descreve como criar uma avaliação da AVS (solução do Azure VMware) para VMs VMware locais com migrações para Azure: avaliação do servidor.

As [migrações para Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
- Para criar uma avaliação, você precisa configurar um dispositivo de migração do Azure para [VMware](how-to-set-up-appliance-vmware.md), que descobre os computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. [Saiba mais](migrate-appliance.md).
- Você também pode [importar os metadados do servidor](./tutorial-discover-import.md) em formato CSV (valores separados por vírgula).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Visão geral da avaliação da solução VMware do Azure (AVS)

Há dois tipos de avaliações que podem ser criadas usando as Migrações para Azure: Avaliação de Servidor.

**Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. <br/><br/> Você pode avaliar suas [VMs VMware](how-to-set-up-appliance-vmware.md)locais, VMS do [Hyper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para o Azure usando esse tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). <br/><br/> Avalie as [VMs do VMware](how-to-set-up-appliance-vmware.md) locais para migração para a AVS (Solução VMware no Azure) usando esse tipo de avaliação.[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> A avaliação da solução de VMware Azure (AVS) está atualmente em visualização e pode ser criada somente para VMs VMware.


Há dois tipos de critérios de dimensionamento que você pode usar para criar avaliações da AVS (solução VMware) do Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho coletados de VMs locais. | **Tamanho de nó recomendado**: com base nos dados de utilização de CPU e memória, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de nó recomendado**: com base no tamanho da VM local, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Executar uma avaliação da AVS (solução do Azure VMware)

Execute uma avaliação da AVS (solução do Azure VMware) da seguinte maneira:

1. Examine as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.

2. Na guia **Servidores**, no bloco **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

    ![Captura de tela mostra os servidores de migração do Azure com a avaliação selecionada em ferramentas de avaliação.](./media/how-to-create-assessment/assess.png)

3. Em **avaliar servidores**, selecione o tipo de avaliação como "solução do Azure VMware (AVS)", selecione a origem da descoberta.

    :::image type="content" source="./media/how-to-create-avs-assessment/assess-servers-avs.png" alt-text="Adicionar noções básicas de avaliação":::

4. Clique em **Editar** para examinar as propriedades de avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Local do botão Editar para examinar as propriedades de avaliação":::

1. Em **Selecionar computadores para avaliar**  >  o **nome da avaliação** > especifique um nome para a avaliação. 
 
1. Em **selecionar ou criar um grupo** > selecione **criar novo** e especifique um nome de grupo. Um grupo reúne uma ou mais VMs para avaliação.
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Adicionar VMs a um grupo":::

1. Em **Adicionar computadores ao grupo**, selecione as VMs a serem adicionadas ao grupo.

1. Clique em **avançar** para **Examinar + criar avaliação** para examinar os detalhes da avaliação.

1. Clique em **Criar Avaliação** para criar o grupo e execute a avaliação.

1. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.

1. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Examinar uma avaliação da AVS (solução do Azure VMware)

Uma avaliação da AVS (solução do Azure VMware) descreve:

- **Preparação da solução VMware do Azure (AVS)**: se as VMs locais são adequadas para a migração para a solução VMware do Azure (AVS).
- **Número de nós de AVS**: número estimado de nós de AVS necessários para executar as VMs.
- **Utilização em nós de AVS**: utilização de CPU, memória e armazenamento projetada em todos os nós.
    - A utilização inclui o fator de início nas seguintes sobrecargas de gerenciamento de cluster, como o vCenter Server, o NSX Manager (grande), o NSX Edge, se o HCX for implantado também o HCX Manager e o dispositivo IX consumindo ~ 44vCPU (11 CPU), 75 GB de RAM e 722GB de armazenamento antes da compactação e da eliminação de duplicação.
    - A memória, a eliminação de duplicação e a compactação estão atualmente definidas como 100% de utilização para memória e 1,5 eliminação de duplicação e compactação, que será uma entrada definida pelo usuário nas versões futuras, permitindo que o usuário ajuste seu tamanho necessário.
- **Estimativa de custo mensal**: os custos mensais estimados para todos os nós da AVS (solução do Azure VMware) que executam as VMs locais.


### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **Metas de migração** >  **Servidores**, clique em **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.

2. Em **Avaliações**, clique em uma avaliação para abri-la.

    :::image type="content" source="./media/how-to-create-avs-assessment/avs-assessment-summary.png" alt-text="Resumo da avaliação de AVS":::

### <a name="review-azure-vmware-solution-avs-readiness"></a>Examinar a preparação da solução VMware do Azure (AVS)

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para a AVS.

2. Examine o status da VM:
    - **Pronto para a sincronização automática**: o computador pode ser migrado no estado em que se encontra para o Azure (AVS) sem nenhuma alteração. Ele será iniciado no AVS com suporte completo a AVS.
    - **Pronto com condições**: a VM pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas do VMware e outras configurações, antes que a funcionalidade completa da VM possa ser obtida na AVS.
    - **Não está pronto para o AVS**: a VM não será iniciada na AVS. Por exemplo, se a VM do VMware local tiver um dispositivo externo anexado, como um CD-ROM, a operação do VMotion falhará (se estiver usando VMware VMotion).
    - **Preparação desconhecida**: as migrações para Azure não puderam determinar a prontidão do computador devido a metadados insuficientes coletados do ambiente local.

3. Examine a ferramenta sugerida:
    - **VMware HCX ou Enterprise**: para máquinas VMware, a solução de HCX (extensão de nuvem híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para sua nuvem privada da AVS (solução VMware) do Azure. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Desconhecido**: Para computadores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para computadores VMware, sugerimos usar a solução de HCX do VMware. 

4. Clique em um status de **preparação de AVS** . Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar os detalhes de custo

Esta exibição mostra o custo estimado de executar VMs na solução VMware do Azure (AVS).

1. Examinar os custos totais mensais. Os custos são agregados para todas as VMs no grupo avaliado. 

    - As estimativas de custo são baseadas no número de nós de AVS necessários considerando os requisitos de recursos de todas as VMs no total.
    - Como o preço da solução do Azure VMware (AVS) é por nó, o custo total não tem custo de computação e distribuição de custo de armazenamento.
    - A estimativa de custo refere-se à execução das VMs locais na AVS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.
    
2. Você pode examinar as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.

3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações com base no desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é fornecida.
- A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- Para o dimensionamento baseado em desempenho, as avaliações de AVS na avaliação do servidor precisam dos dados de utilização da CPU e da memória da VM. Os dados de desempenho a seguir são coletados, mas não usados em recomendações de dimensionamento para avaliações de AVS:
  - Os dados de taxa de transferência e IOPS de disco para cada disco anexado à VM.
  - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a uma VM.

As classificações de confiança para uma avaliação são indicadas a seguir.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre os dados de desempenho 


## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o [mapeamento de dependência](how-to-create-group-machine-dependencies.md) para criar grupos de alta confiança.
- [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md) sobre como as avaliações de AVS são calculadas.