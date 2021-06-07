---
title: Avaliar servidores físicos quanto à migração para o Azure com as Migrações para Azure
description: Descreve como avaliar servidores físicos locais quanto à migração para o Azure usando as Migrações para Azure.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: b1026f718c7c59e88dbf3b041c4b07cd1cfc641c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780558"
---
# <a name="tutorial-assess-physical-servers-for-migration-to-azure"></a>Tutorial: Avaliar servidores físicos para migração para o Azure

Como parte de sua jornada de migração para o Azure, é necessário avaliar suas cargas de trabalho locais para medir a prontidão para a nuvem, identificar riscos e estimar custos e complexidade.

Este artigo mostra como avaliar os servidores físicos locais quanto à migração para o Azure usando a ferramenta Migrações para Azure: descoberta e avaliação.


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
- Executar uma avaliação com base nos metadados do servidor e nas informações de configuração.
- Executar uma avaliação com base nos dados de desempenho.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam as opções padrão sempre que possível. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de seguir este tutorial para avaliar seus servidores quanto à migração para VMs do Azure, lembre-se de descobrir os servidores que deseja avaliar:
    - Para descobrir servidores usando o dispositivo de Migrações para Azure, [siga este tutorial](tutorial-discover-physical.md). 
    - Para descobrir servidores usando um arquivo CSV importado, [siga este tutorial](tutorial-discover-import.md).
- Verifique se os servidores físicos que deseja avaliar não estão executando o Windows Server 2003 nem o SUSE Linux. Não há suporte para a avaliação nesses servidores.


## <a name="decide-which-assessment-to-run"></a>Decidir qual avaliação executar


Decida se deseja executar uma avaliação usando critérios de dimensionamento com base nos dados/metadados de configuração do servidor que são coletados no estado em que se encontram localmente ou nos dados de desempenho dinâmicos.

**Avaliação** | **Detalhes** | **Recomendação**
--- | --- | ---
**No estado em que se encontra localmente** | Avaliação com base nos dados/metadados de configuração do servidor.  | A recomendação de tamanho da VM do Azure é baseada no tamanho da VM local.<br/><br> O tipo de disco recomendado do Azure é baseado no que você seleciona na configuração de tipo de armazenamento na avaliação.
**Com base no desempenho** | Avaliar com base nos dados de desempenho dinâmicos coletados. | A recomendação de tamanho da VM do Azure é baseada nos dados de utilização da CPU e da memória.<br/><br/> A recomendação do tipo de disco é baseada na IOPS e na taxa de transferência dos discos locais.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Na página **Visão geral** > **Windows, Linux e SQL Server**, clique em **Avaliar e migrar servidores**.

   ![Localização do botão Avaliar e migrar servidores](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Em **Migrações para Azure: descoberta e avaliação**, clique em **Avaliar**.

    ![Localização do botão Avaliar](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Em **Avaliar servidores** > **Tipo de avaliação**, selecione **VM do Azure**.
4. Em **Origem da descoberta**:

    - Se você tiver descoberto servidores usando o dispositivo, selecione **Servidores descobertos no dispositivo de Migrações para Azure**.
    - Se você tiver descoberto servidores usando um arquivo CSV importado, selecione **Servidores importados**. 
    
1. Clique em **Editar** para examinar as propriedades da avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Localização do botão Editar para examinar as propriedades da avaliação":::

1. Em **Propriedades da avaliação** > **Propriedades de Destino**:
    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar.
        - As recomendações de tamanho e custo são baseadas na localização especificada. Depois de alterar a localização de destino padrão, você precisará especificar **Instâncias Reservadas** e **Série de VM**.
        - No Azure Governamental, você pode direcionar avaliações [nestas regiões](migrate-support-matrix.md#supported-geographies-azure-government)
    - Em **Tipo de armazenamento**,
        - Se você quiser usar dados baseados em desempenho na avaliação, selecione **Automático** para que as Migrações para Azure recomendem um tipo de armazenamento com base na IOPS do disco e na taxa de transferência.
        - Como alternativa, selecione o tipo de armazenamento que você deseja usar para a VM ao migrá-la.
    - Em **Instâncias Reservadas**, especifique se deseja usar instâncias reservadas para a VM ao migrá-la.
        - Se você optar por usar uma instância reservada, não poderá especificar **Desconto (%)** nem **Tempo de atividade da VM**. 
        - [Saiba mais](https://aka.ms/azurereservedinstances).
 1. Em **Tamanho da VM**:
     - Em **Critério de dimensionamento**, selecione se deseja basear a avaliação em metadados/dados de configuração do servidor ou em dados baseados no desempenho. Se você optar por usar dados de desempenho:
        - Em **Histórico de desempenho**, indique a duração dos dados em que você deseja basear a avaliação
        - Em **Utilização de percentual**, especifique o valor percentual que você deseja usar para a amostragem de desempenho. 
    - Em **Série de VMs**, especifique a série de VMs do Azure que você deseja considerar.
        - Se você usa a avaliação baseada em desempenho, a ferramenta Migrações para Azure sugere um valor para você.
        - Ajuste as configurações conforme necessário. Por exemplo, se você não tiver um ambiente de produção que exija VMs da série A no Azure, poderá excluir a série A da lista de séries.
    - Em **Fator de conforto**, indique o buffer que você deseja usar durante a avaliação. Esse recurso detecta problemas como uso sazonal, histórico de desempenho baixo e prováveis aumentos no uso futuro. Por exemplo, se você usar um fator de conforto de dois:
    
        **Componente** | **Utilização efetiva** | **Adicionar fator de conforto (2.0)**
        --- | --- | ---
        Núcleos | 2  | 4
        Memória | 8 GB | 16 GB
   
1. Em **Preços**:
    - Em **Oferta**, especifique a [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) se você estiver registrado. A avaliação estima o custo dessa oferta.
    - Em **Moeda**, selecione a moeda de cobrança para sua conta.
    - Em **Desconto (%)** , adicione quaisquer descontos específicos à assinatura recebidos sobre a oferta do Azure. A configuração padrão é 0%.
    - Em **Tempo de atividade da VM**, especifique a duração (dias por mês/hora por dia) em que as VMs serão executadas.
        - Isso é útil para VMs do Azure que não serão executadas continuamente.
        - As estimativas de custo são baseadas na duração especificada.
        - O valor padrão é de 31 dias por mês/24 horas por dia.
    - Em **Assinatura de EA**, especifique se deseja levar em consideração o desconto de assinatura de EA (Contrato Enterprise) para a estimativa de custo. 
    - Em **Benefício Híbrido do Azure**, especifique se você já tem uma licença do Windows Server. Caso você tenha e ela esteja coberta pelo Software Assurance das Assinaturas do Windows Server, você pode se inscrever no [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/) ao trazer as licenças para o Azure.

1. Clique em **Salvar** se você fizer alterações.

    ![Propriedades de avaliação](./media/tutorial-assess-vmware-azure-vm/assessment-properties.png)

1. Em **Avaliar Servidores** > clique em **Avançar**.

1. Em **Selecionar os servidores a serem avaliados** > **Nome da avaliação** > especifique um nome para a avaliação. 

1. Em **Selecionar ou criar um grupo** > selecione **Criar** e especifique um nome de grupo. 
    

1. Selecione o dispositivo e as VMs que você deseja adicionar ao grupo. Em seguida, clique em **Próximo**.


1. Em **Examinar + criar avaliação**, examine os detalhes da avaliação e clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

1. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: descoberta e avaliação** > **Avaliações**.

1. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.
    > [!NOTE]
    > Para avaliações baseadas em desempenho, recomendamos que você espere pelo menos um dia após o início da descoberta antes de criar uma avaliação. Isso fornecerá tempo para coletar dados de desempenho com maior confiança. O ideal é que, depois de iniciar a descoberta, você aguarde a duração do desempenho especificada (dia/semana/mês) para obter uma classificação de alta confiança.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

Para exibir uma avaliação:

1. Em **Windows, Linux e SQL Server** > **Migrações para Azure: descoberta e avaliação**, clique no número ao lado de **Avaliações**.
2. Em **Avaliações**, selecione uma avaliação para abri-la. Como exemplo (estimativas e custos somente para exemplo): 

    ![Resumo da avaliação](./media/tutorial-assess-physical/assessment-summary.png)

3. Examine o resumo da avaliação. Você também pode editar as propriedades ou recalcular a avaliação.
 
 
### <a name="review-readiness"></a>Examinar preparação

1. Clique em **Preparação para o Azure**.
2. Em **Preparação para o Azure**, examine o status da VM:
    - **Pronto para o Azure**: usado quando as Migrações para Azure recomendam um tamanho e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: usado quando as Migrações para Azure não podem avaliar a preparação devido a problemas de disponibilidade de dados.

3. Selecione um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM. Você também pode fazer drill down para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-estimates"></a>Examinar estimativas de custo

O resumo da avaliação mostra o custo estimado de computação e armazenamento da execução das VMs no Azure. 

1. Examinar os custos totais mensais. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um servidor, bem como para os respectivos discos e as propriedades.
    - Os custos mensais estimados de computação e armazenamento são mostrados.
    - A estimativa de custo refere-se à execução das VMs locais em VMs do Azure. A estimativa não considera os custos de PaaS ou SaaS.

2. Examinar os custos mensais de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento. 
3. Você pode fazer drill down para ver os detalhes de custo de VMs específicas.

### <a name="review-confidence-rating"></a>Revisar classificação de confiança

As Migrações para Azure atribuem uma classificação de confiança às avaliações baseadas no desempenho. A classificação varia de uma estrela (mais baixa) a cinco estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-physical/confidence-rating.png)

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
- Configure o mapeamento de dependências [baseado em agente](how-to-create-group-machine-dependencies.md).
