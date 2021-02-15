---
title: Criar uma avaliação de VM do Azure com a avaliação de servidor de migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação de VM do Azure com a ferramenta de avaliação de servidor de migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 178bdca78c6f011c607de8e1f5d5eabcdbaab7d4
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567697"
---
# <a name="create-an-azure-vm-assessment"></a>Criar uma avaliação de VM do Azure

Este artigo descreve como criar uma avaliação de VM do Azure para VMs VMware locais ou VMs do Hyper-V com migrações para Azure: avaliação de servidor.

As [migrações para Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros. 

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
- Para criar uma avaliação, você precisa configurar um dispositivo de migração do Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor. [Saiba mais](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Visão geral da avaliação de VM do Azure
Há dois tipos de critérios de dimensionamento que você pode usar para criar uma avaliação de VM do Azure usando migrações para Azure: avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização da CPU e de memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento selecionada para a avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.

## <a name="run-an-assessment"></a>Ler uma avaliação

Execute uma avaliação da seguinte maneira:

1. Na página **Servidores** > **Servidores Windows e Linux**, clique em **Avaliar e migrar servidores**.

   ![Localização do botão Avaliar e migrar servidores](./media/tutorial-assess-vmware-azure-vm/assess.png)

2. Em **Migrações para Azure: Avaliação de Servidor**, clique em **Avaliar**.

    ![Localização do botão Avaliar](./media/tutorial-assess-vmware-azure-vm/assess-servers.png)

3. Em **Avaliar servidores** > **Tipo de avaliação**, selecione **VM do Azure**.
4. Em **Origem da descoberta**:

    - Se você descobriu computadores usando o dispositivo, selecione **Computadores descobertos no dispositivo de Migrações para Azure**.
    - Se você descobriu computadores usando um arquivo CSV importado, selecione **Computadores importados**. 
    
1. Clique em **Editar** para examinar as propriedades de avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assessment-name.png" alt-text="Local do botão Editar para examinar as propriedades de avaliação":::

1. Em **Propriedades da avaliação** > **Propriedades de Destino**:
    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar.
        - As recomendações de tamanho e custo são baseadas na localização especificada. Depois de alterar o local de destino de padrão, você será solicitado a especificar as **instâncias reservadas** e a **série de VMs**.
        - No Azure Governamental, você pode direcionar avaliações [nestas regiões](migrate-support-matrix.md#supported-geographies-azure-government)
    - Em **Tipo de armazenamento**,
        - Se você quiser usar dados baseados em desempenho na avaliação, selecione **Automático** para que as Migrações para Azure recomendem um tipo de armazenamento com base na IOPS do disco e na taxa de transferência.
        - Como alternativa, selecione o tipo de armazenamento que você deseja usar para a VM ao migrá-la.
    - Em **Instâncias Reservadas**, especifique se deseja usar instâncias reservadas para a VM ao migrá-la.
        - Se você optar por usar uma instância reservada, não poderá especificar **Desconto (%)** nem **Tempo de atividade da VM**. 
        - [Saiba mais](https://aka.ms/azurereservedinstances).
 1. Em **Tamanho da VM**:
     - Em **Critério de dimensionamento**, selecione se você deseja basear a avaliação em metadados/dados de configuração de computador ou em dados baseados no desempenho. Se você optar por usar dados de desempenho:
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
    - Em **Oferta**, especifique a [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) se você estiver registrado. A Avaliação de Servidor estima o custo dessa oferta.
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

1. Em **avaliar servidores** > clique em **Avançar**.

1. Em **Selecionar computadores para avaliar**  >  o **nome da avaliação** > especifique um nome para a avaliação. 

1. Em **selecionar ou criar um grupo** > selecione **criar novo** e especifique um nome de grupo. 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vm/assess-group.png" alt-text="Adicionar VMs a um grupo":::

1. Selecione o dispositivo e as VMs que você deseja adicionar ao grupo. Em seguida, clique em **Próximo**.


1. Em **Examinar + criar avaliação**, examine os detalhes da avaliação e clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

1. Após a criação da avaliação, veja-a em **Servidores** > **Migrações para Azure: Avaliação de Servidor** > **Avaliações**.

1. Clique em **Exportar avaliação**, para baixá-la como um arquivo do Excel.
    > [!NOTE]
    > Para avaliações baseadas em desempenho, recomendamos que você espere pelo menos um dia após o início da descoberta antes de criar uma avaliação. Isso fornecerá tempo para coletar dados de desempenho com maior confiança. O ideal é que, depois de iniciar a descoberta, você aguarde a duração do desempenho especificada (dia/semana/mês) para obter uma classificação de alta confiança.


## <a name="review-an-azure-vm-assessment"></a>Examinar uma avaliação de VM do Azure

Uma avaliação de VM do Azure descreve:

- **Preparação para o Azure**: indica se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: os custos mensais estimados de computação e armazenamento para execução das VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-azure-vm-assessment"></a>Exibir uma avaliação de VM do Azure

1. Em **Metas de migração** >  **Servidores**, clique em **Avaliações** em **Migrações para Azure: Avaliação de Servidor**.
2. Em **Avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a Preparação para o Azure

1. Em **Preparação para o Azure**, verifique se as VMs estão prontas para a migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: as Migrações para Azure recomendam um tamanho de VM e estimativas de custo para as VMs na avaliação.
    - **Pronto com condições**: mostra os problemas e a correção sugerida.
    - **Não está pronto para o Azure**: mostra os problemas e a correção sugerida.
    - **Preparação desconhecida**: usado quando as Migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

3. Clique em um status de **Preparação para o Azure**. Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar os detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento da execução das VMs no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e em seus discos e propriedades.
    - Os custos mensais estimados de computação e armazenamento são mostrados.
    - A estimativa de custo refere-se à execução das VMs locais como VMs de IaaS. A Avaliação de Servidor das Migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações com base no desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é fornecida.
- A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são indicadas a seguir.

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