---
title: Avaliar servidores VMware quanto à migração para a AVS (Solução VMware no Azure) com as Migrações para Azure
description: Saiba como avaliar servidores no ambiente VMware quanto à migração para a AVS com as Migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: 31bf3909012231996bd340cfa4d388f0fe20a4f5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782105"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Tutorial: Avaliar servidores VMware quanto à migração para a AVS

Como parte de sua jornada de migração para o Azure, é necessário avaliar suas cargas de trabalho locais para medir a prontidão para a nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra como avaliar as máquinas virtuais/os servidores descobertos do VMware quanto à migração para a AVS (Solução VMware no Azure) usando as Migrações para Azure. A AVS é um serviço gerenciado que permite executar a plataforma VMware no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
- Executar uma avaliação com base nos metadados do servidor e nas informações de configuração.
- Executar uma avaliação com base nos dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.



## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir este tutorial para avaliar seus servidores quanto à migração para a AVS, lembre-se de descobrir os servidores que deseja avaliar:

- Para descobrir servidores usando o dispositivo de Migrações para Azure, [siga este tutorial](tutorial-discover-vmware.md). 
- Para descobrir servidores usando um arquivo CSV importado, [siga este tutorial](tutorial-discover-import.md).


## <a name="decide-which-assessment-to-run"></a>Decidir qual avaliação executar


Decida se deseja executar uma avaliação usando critérios de dimensionamento com base nos dados/metadados de configuração do servidor que são coletados no estado em que se encontram localmente ou nos dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**No estado em que se encontra localmente** | Avaliação com base nos dados/metadados de configuração do servidor.  | O tamanho de nó recomendado na AVS é baseado no tamanho da VM/do servidor local, com as configurações que você especifica na avaliação do tipo de nó, do tipo de armazenamento e da configuração de tolerância a falhas.
**Com base no desempenho** | Avaliar com base nos dados de desempenho dinâmicos coletados. | O tamanho do nó recomendado na AVS é baseado nos dados de utilização da CPU e da memória, junto com as configurações que você especifica na avaliação para o tipo de nó, tipo de armazenamento e configuração de tolerância a falhas.

> [!NOTE]
> A avaliação da AVS (Solução VMware no Azure) só pode ser criada para VMs/servidores do VMware.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1.  Na página **Visão geral** > **Windows, Linux e SQL Server**, clique em **Avaliar e migrar servidores**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de visão geral das Migrações para Azure":::

1. Em **Migrações para Azure: descoberta e avaliação**, clique em **Avaliar**.

   ![Localização do botão Avaliar](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Em **Avaliar servidores** > **Tipo de avaliação**, selecione **AVS (Solução VMware no Azure)** .

1. Em **Origem da descoberta**:

    - Se você tiver descoberto servidores usando o dispositivo, selecione **Servidores descobertos no dispositivo de Migrações para Azure**.
    - Se você tiver descoberto servidores usando um arquivo CSV importado, selecione **Servidores importados**. 
    
1. Clique em **Editar** para examinar as propriedades da avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Página para selecionar as configurações de avaliação":::
 

1. Em **Propriedades da avaliação** > **Propriedades de Destino**:

    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar.
       - As recomendações de tamanho e custo são baseadas na localização especificada.
   - O **Tipo de armazenamento** usa **vSAN** como padrão. Esse é o tipo de armazenamento padrão para uma nuvem privada da AVS.
   - No momento, não há suporte para **Instâncias Reservadas** em nós da AVS.
1. Em **Tamanho da VM**:
    - O **Tipo de nó** usa **AV36** como padrão. As Migrações para Azure recomendam o uso de nós necessários para migrar os servidores para a AVS.
    - Em **Configuração FTT, nível de RAID**, selecione a combinação RAID e Tolerância a Falhas.  A opção FTT selecionada, combinada com o requisito de disco de servidor local, determina o total de armazenamento vSAN necessário na AVS.
    - Em **Excesso de Assinatura de CPU**, especifique a proporção de núcleos virtuais associados a um núcleo físico no nó da AVS. Um Excesso de Assinatura maior que 4:1 pode causar degradação do desempenho, mas pode ser usado para cargas de trabalho do tipo servidor Web.
    - Em **Fator de memória para confirmação**, especifique a proporção de memória para confirmações no cluster. Um valor de 1 representa 100% de uso da memória; um valor de 0,5 representa 50% e um valor de 2 usaria 200% da memória disponível. Você só pode adicionar valores de 0,5 a 10, com até uma casa decimal.
    - Em **Fator de eliminação de duplicação e compactação**, especifique o fator de eliminação de duplicação e compactação previstas para suas cargas de trabalho. O valor real pode ser obtido da configuração de armazenamento ou da vSAN local e pode variar por carga de trabalho. Um valor de 3 significaria 3x para, de modo que para um disco de 300 GB, somente 100 GB de armazenamento seriam usados. Um valor de 1 significaria que não há eliminação de duplicação ou compactação. Você só pode adicionar valores de 1 a 10, com até uma casa decimal.
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
    - Em **Oferta**, a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) na qual você se registrou será exibida. A Avaliação estima o custo dessa oferta.
    - Em **Moeda**, selecione a moeda de cobrança para sua conta.
    - Em **Desconto (%)** , adicione quaisquer descontos específicos à assinatura recebidos sobre a oferta do Azure. A configuração padrão é 0%.

1. Clique em **Salvar** se você fizer alterações.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Propriedades de avaliação":::

1. Em **Avaliar Servidores**, clique em **Próximo**.

1. Em **Selecionar os servidores a serem avaliados** > **Nome da avaliação** > especifique um nome para a avaliação. 
 
1. Em **Selecionar ou criar um grupo** > selecione **Criar** e especifique um nome de grupo. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Adicionar servidores a um grupo":::
 
1. Selecione o dispositivo e os servidores que deseja adicionar ao grupo. Em seguida, clique em **Próximo**.

1. Em **Examinar + criar avaliação**, examine os detalhes da avaliação e clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

    > [!NOTE]
    > Para avaliações baseadas em desempenho, recomendamos que você espere pelo menos um dia após o início da descoberta antes de criar uma avaliação. Isso fornecerá tempo para coletar dados de desempenho com maior confiança. O ideal é que, depois de iniciar a descoberta, você aguarde a duração do desempenho especificada (dia/semana/mês) para obter uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação da AVS descreve:

- Preparação para a AVS: se os servidores locais são adequados para migração para a AVS (Solução VMware no Azure).
- Número de nós da AVS: número estimado de nós da AVS necessários para executar os servidores.
- Utilização em nós da AVS: utilização de CPU, memória e armazenamento projetada em todos os nós.
    - A utilização inclui a fatoração inicial nas sobrecargas de gerenciamento de cluster a seguir, como o vCenter Server, o NSX Manager (tamanho grande), o NSX Edge, o HCX Manager (se o HCX for implantado também) e o dispositivo IX consumindo cerca de 44 vCPUs (11 CPUs), 75 GB de RAM e 722 GB de armazenamento antes da compactação e da eliminação de duplicação. 
- Estimativa de custo mensal: os custos mensais estimados para todos os nós da AVS (Solução VMware no Azure) que executam os servidores locais.

## <a name="view-an-assessment"></a>Exibir uma avaliação

Para exibir uma avaliação:

1. Em **Windows, Linux e SQL Server** > **Migrações para Azure: descoberta e avaliação**, clique no número ao lado da **Solução VMware no Azure**.

1. Em **Avaliações**, selecione uma avaliação para abri-la. Como exemplo (estimativas e custos somente para exemplo): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumo da avaliação da AVS":::

1. Examine o resumo da avaliação. Você também pode editar as propriedades ou recalcular a avaliação.
 

### <a name="review-readiness"></a>Examinar preparação

1. Clique em **Preparação para o Azure**.
2. Em **Preparação para o Azure**, examine o status da preparação.

    - **Pronto para a AVS**: o servidor pode ser migrado no estado em que se encontra para a AVS no Azure, sem nenhuma alteração. O servidor será iniciado na AVS, com suporte completo dela.
    - **Pronto com condições**: o servidor pode ter problemas de compatibilidade com a versão atual do vSphere. Pode ser necessário instalar ferramentas do VMware ou outras configurações antes de ter a funcionalidade completa na AVS.
    - **Não está pronto para a AVS**: a VM não será iniciada na AVS. Por exemplo, se um servidor VMware local tiver um dispositivo externo (como um CD-ROM) anexado e você estiver usando o VMware VMotion, a operação do VMotion falhará.
 - **Preparação desconhecida**: as Migrações para Azure não conseguiram determinar a preparação do servidor devido a metadados insuficientes coletados do ambiente local.

3. Examine a ferramenta sugerida.

    - VMware HCX ou Enterprise: para servidores VMware, a solução de HCX (Extensão de Nuvem Híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para a nuvem privada da AVS (Solução VMware no Azure). Saiba mais.
    - Desconhecida: para servidores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para servidores VMware, sugerimos usar a solução de HCX (Extensão de Nuvem Híbrida) do VMware.
4. Clique em um status de preparação para a AVS. Você pode exibir os detalhes de preparação do servidor e fazer uma busca detalhada para ver os detalhes do servidor, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Examinar estimativas de custo

O resumo da avaliação mostra o custo estimado de computação e armazenamento da execução de servidores no Azure. 

1. Examinar os custos totais mensais. Os custos são agregados para todos os servidores no grupo avaliado.

    - As estimativas de custo são baseadas no número de nós da AVS necessários, considerando os requisitos de recursos de todos os servidores no total.
    - Como o preço da AVS é calculado por nó, o custo total não tem custo de computação e distribuição de custo de armazenamento.
    - A estimativa de custo refere-se à execução dos servidores locais na AVS. A avaliação da AVS não considera os custos de PaaS ou SaaS.

2. Examinar as estimativas de armazenamento mensal. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento. 
3. Faça uma busca detalhada para ver os detalhes de custo de servidores específicos.

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

- Encontre dependências de servidor usando o [mapeamento de dependências](concepts-dependency-visualization.md).
- Configure o mapeamento de dependências [sem agente](how-to-create-group-machine-dependencies-agentless.md) ou [baseado em agente](how-to-create-group-machine-dependencies.md).
