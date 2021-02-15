---
title: Avaliar as VMs do VMware para migração para a AVS (Solução VMware do Azure) com as Migrações para Azure
description: Aprenda como avaliar as VMs do VMware para migração para a AVS com a Avaliação de Servidor das Migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: e57084dab00210802edbd46e3380313e034eb036
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566772"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>Tutorial: Avaliar VMs do VMware para migração para a AVS

Como parte de sua jornada de migração para o Azure, é necessário avaliar suas cargas de trabalho locais para medir a prontidão para a nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra como avaliar as VMs (máquinas virtuais) descobertas do VMware quanto à migração para a AVS usando as Migrações para Azure: Avaliação de Servidor. A AVS é um serviço gerenciado que permite executar a plataforma VMware no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
- Executar uma avaliação com base nos metadados do computador e nas informações de configuração.
- Executar uma avaliação com base nos dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este tutorial para avaliar seus computadores quanto à migração para a AVS, é preciso descobrir os computadores que você deseja avaliar:

- Para descobrir computadores usando o dispositivo de Migrações para Azure, [siga este tutorial](tutorial-discover-vmware.md). 
- Para descobrir computadores usando um arquivo CSV importado, [siga este tutorial](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decidir qual avaliação executar


Decida se deseja executar uma avaliação usando critérios de dimensionamento com base nos dados/metadados de configuração do computador, que são coletados como estão no local, ou em dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**No estado em que se encontra localmente** | Avaliar com base nos dados/metadados de configuração do computador.  | O tamanho do nó recomendado na AVS é baseado no tamanho da VM local, junto com as configurações que você especifica na avaliação para o tipo de nó, tipo de armazenamento e configuração de tolerância a falhas.
**Com base no desempenho** | Avaliar com base nos dados de desempenho dinâmicos coletados. | O tamanho do nó recomendado na AVS é baseado nos dados de utilização da CPU e da memória, junto com as configurações que você especifica na avaliação para o tipo de nó, tipo de armazenamento e configuração de tolerância a falhas.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Na página **Servidores** > **Servidores Windows e Linux**, clique em **Avaliar e migrar servidores**.

   ![Localização do botão Avaliar e migrar servidores](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Em **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

1. Em **Avaliar servidores** > **Tipo de avaliação**, selecione **AVS (Solução VMware do Azure) (versão prévia)** .

1. Em **Origem da descoberta**:

    - Se você descobriu computadores usando o dispositivo, selecione **Computadores descobertos no dispositivo de Migrações para Azure**.
    - Se você descobriu computadores usando um arquivo CSV importado, selecione **Computadores importados**. 
    
1. Clique em **Editar** para examinar as propriedades da avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Página para selecionar as configurações de avaliação":::
 

1. Em **Propriedades da avaliação** > **Propriedades de Destino**:

    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar.
       - As recomendações de tamanho e custo são baseadas na localização especificada.
       - Atualmente, você pode fazer a avaliação para quatro regiões (Leste da Austrália, Leste dos EUA, Oeste da Europa e Oeste dos EUA)
   - O **Tipo de armazenamento** usa **vSAN** como padrão. Esse é o tipo de armazenamento padrão para uma nuvem privada da AVS.
   - No momento, não há suporte para **Instâncias Reservadas** em nós da AVS.
1. Em **Tamanho da VM**:
    - O **Tipo de nó** usa **AV36** como padrão. As Migrações para Azure recomendam o uso de nós necessários para migrar as VMs para a AVS.
    - Em **Configuração FTT, nível de RAID**, selecione a combinação RAID e Tolerância a Falhas.  A opção FTT selecionada, combinada com o requisito de disco de VM local, determina o total de armazenamento vSAN necessário na AVS.
    - Em **Excesso de Assinatura de CPU**, especifique a proporção de núcleos virtuais associados a um núcleo físico no nó da AVS. Um Excesso de Assinatura maior que 4:1 pode causar degradação do desempenho, mas pode ser usado para cargas de trabalho do tipo servidor Web.

1. Em **Tamanho do Nó**: 
    - Em **Critério de Dimensionamento**, selecione se você deseja basear a avaliação em metadados estáticos ou em dados baseados em desempenho. Se você optar por usar dados de desempenho:
        - Em **Histórico de desempenho**, indique a duração dos dados em que você deseja basear a avaliação
        - Em **Utilização de percentual**, especifique o valor percentual que você deseja usar para a amostragem de desempenho. 
    - Em **Fator de conforto**, indique o buffer que você deseja usar durante a avaliação. Esse recurso detecta problemas como uso sazonal, histórico de desempenho baixo e prováveis aumentos no uso futuro. Por exemplo, se você usar um fator de conforto de dois:
    
        **Componente** | **Utilização efetiva** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB  

1. Em **Preços**:
    - Em **Oferta**, na [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você está inscrito é exibida a estimativa de custo dessa oferta de acordo com a Avaliação de Servidor.
    - Em **Moeda**, selecione a moeda de cobrança para sua conta.
    - Em **Desconto (%)** , adicione quaisquer descontos específicos à assinatura recebidos sobre a oferta do Azure. A configuração padrão é 0%.

1. Clique em **Salvar** se você fizer alterações.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propriedades de avaliação":::

1. Em **Avaliar Servidores**, clique em **Próximo**.

1. Em **Selecionar os computadores a serem avaliados** > **Nome da avaliação** > especifique um nome para a avaliação. 
 
1. Em **Selecionar ou criar um grupo** > selecione **Criar** e especifique um nome de grupo. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Adicionar VMs a um grupo":::
 
1. Selecione o dispositivo e as VMs que você deseja adicionar ao grupo. Em seguida, clique em **Próximo**.

1. Em **Examinar + criar avaliação**, examine os detalhes da avaliação e clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

    > [!NOTE]
    > Para avaliações baseadas em desempenho, recomendamos que você espere pelo menos um dia após o início da descoberta antes de criar uma avaliação. Isso fornecerá tempo para coletar dados de desempenho com maior confiança. O ideal é que, depois de iniciar a descoberta, você aguarde a duração do desempenho especificada (dia/semana/mês) para obter uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação da AVS descreve:

- Preparação para a AVS: se as VMs locais são adequadas para migração para a AVS (Solução VMware do Azure).
- Número de nós na AVS: número estimado de nós da AVS necessários para executar as VMs.
- Utilização em nós da AVS: utilização de CPU, memória e armazenamento projetada em todos os nós.
    - A utilização inclui a fatoração inicial nas sobrecargas de gerenciamento de cluster a seguir, como o vCenter Server, o NSX Manager (tamanho grande), o NSX Edge, o HCX Manager (se o HCX for implantado também) e o dispositivo IX consumindo cerca de 44 vCPUs (11 CPUs), 75 GB de RAM e 722 GB de armazenamento antes da compactação e da eliminação de duplicação. 
    - A memória, a eliminação de duplicados e a compactação estão atualmente definidas como 100% de utilização para memória e 1,5 de eliminação de duplicados e compactação, que será uma entrada definida pelo usuário nas versões futuras, permitindo que o usuário ajuste o dimensionamento necessário.
- Estimativa de custo mensal: os custos mensais estimados para todos os nós da AVS (Solução VMware do Azure) que executam as VMs locais.

## <a name="view-an-assessment"></a>Exibir uma avaliação

Para exibir uma avaliação:

1. Em **Servidores** > **Migrações para Azure: Avaliação de Servidor**, clique no número ao lado de **Avaliações**.

1. Em **Avaliações**, selecione uma avaliação para abri-la. Como exemplo (estimativas e custos somente para exemplo): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumo da avaliação da AVS":::

1. Examine o resumo da avaliação. Você também pode editar as propriedades ou recalcular a avaliação.
 

### <a name="review-readiness"></a>Examinar preparação

1. Clique em **Preparação para o Azure**.
2. Em **Preparação para o Azure**, examine o status da VM.

    - **Pronto para a AVS**: o computador pode ser migrado no estado em que se encontra para a AVS, sem nenhuma alteração. O computador será iniciado na AVS, com suporte completo para esse serviço.
    - **Pronto com condições**: O computador pode ter problemas de compatibilidade com a versão atual do vSphere. Pode ser necessário instalar ferramentas do VMware ou outras configurações antes de ter a funcionalidade completa na AVS.
    - **Não está pronto para a AVS**: a VM não será iniciada na AVS. Por exemplo, se uma VM do VMware local tiver um dispositivo externo (como um CD-ROM) anexado a ela e você estiver usando VMware VMotion, a operação do VMotion falhará.
 - **Preparação desconhecida**: as Migrações para Azure não conseguiram determinar a preparação do computador devido a metadados insuficientes coletados do ambiente local.

3. Examine a ferramenta sugerida.

    - VMware HCX ou Enterprise: Para computadores VMware, a ferramenta de migração sugerida para migrar sua carga de trabalho local para a nuvem privada da AVS (Solução VMware do Azure) é a solução de HCX (Extensão de Nuvem Híbrida) do VMware. Saiba mais.
    - Desconhecido: Para computadores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para computadores VMware, sugerimos usar a solução de HCX do VMware.
4. Clique em um status de preparação para a AVS. Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Examinar estimativas de custo

O resumo da avaliação mostra o custo estimado de computação e armazenamento da execução das VMs no Azure. 

1. Examinar os custos totais mensais. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas no número de nós da AVS necessários, considerando os requisitos de recursos de todas as VMs no total.
    - Como o preço da AVS é calculado por nó, o custo total não tem custo de computação e distribuição de custo de armazenamento.
    - A estimativa de custo refere-se à execução das VMs locais na AVS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.

2. Examinar as estimativas de armazenamento mensal. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento. 
3. Você pode fazer drill down para ver os detalhes de custo de VMs específicas.

### <a name="review-confidence-rating"></a>Revisar classificação de confiança

A Avaliação de Servidor atribui uma classificação de confiança às avaliações baseadas no desempenho. A classificação varia de uma estrela (mais baixa) a cinco estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho na avaliação. A classificação é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.

> [!NOTE]
> As classificações de confiança não serão atribuídas se você criar uma avaliação com base em um arquivo CSV.

As classificações de confiança são as mostradas a seguir.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais](concepts-assessment-calculation.md#confidence-ratings-performance-based) sobre as classificações de confiança.

## <a name="next-steps"></a>Próximas etapas

- Encontre dependências de computador usando o [mapeamento de dependências](concepts-dependency-visualization.md).
- Configure o mapeamento de dependências [sem agente](how-to-create-group-machine-dependencies-agentless.md) ou [baseado em agente](how-to-create-group-machine-dependencies.md).
