---
title: Criar uma avaliação de AVS com as migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação de AVS com as migrações para Azure
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 72372e6365a2535e449681549a515c3f8594f2f1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786593"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>Criar uma avaliação da AVS (solução do Azure VMware)

Este artigo descreve como criar uma avaliação da AVS (solução do Azure VMware) para servidores locais no ambiente VMware com migrações para Azure: descoberta e avaliação.

As [migrações para Azure](migrate-services-overview.md) ajudam a migrar para o Azure. As Migrações para Azure oferecem um hub centralizado para acompanhar a descoberta, a avaliação e a migração de infraestrutura, aplicativos e dados locais para o Azure. O hub fornece ferramentas do Azure para avaliação e migração, além de ofertas de ISV (fornecedor independente de software) de terceiros.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](./create-manage-projects.md) um projeto de migrações para Azure.
- Se você já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: descoberta e avaliação.
- Para criar uma avaliação, você precisa configurar um dispositivo de migração do Azure para [VMware](how-to-set-up-appliance-vmware.md), que descobre os servidores locais e envia metadados e dados de desempenho para migrações para Azure: descoberta e avaliação. [Saiba mais](migrate-appliance.md).
- Você também pode [importar os metadados do servidor](./tutorial-discover-import.md) em formato CSV (valores separados por vírgula).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Visão geral da avaliação da solução VMware do Azure (AVS)

Há três tipos de avaliações que você pode criar usando as migrações para Azure: descoberta e avaliação.

***Tipo de avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar servidores locais para máquinas virtuais do Azure. Você pode avaliar seus servidores locais no ambiente [VMware](how-to-set-up-appliance-vmware.md) e [Hyper-V](how-to-set-up-appliance-hyper-v.md) e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para VMs do Azure usando esse tipo de avaliação.
**SQL do Azure** | Avaliações para migrar seus SQL Servers locais de seu ambiente VMware para o banco de dados SQL do Azure ou o Azure SQL Instância Gerenciada.
**AVS (Solução VMware no Azure)** | Avaliações para migrar servidores locais para a [AVS (Solução VMware no Azure)](../azure-vmware/introduction.md). Você pode avaliar seus servidores locais no [ambiente VMware](how-to-set-up-appliance-vmware.md) para migração para a solução VMware do Azure (AVS) usando esse tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> A avaliação da solução de VMware Azure (AVS) pode ser criada somente para servidores no ambiente VMware.


Há dois tipos de critérios de dimensionamento que você pode usar para criar avaliações da AVS (solução VMware) do Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Com base no desempenho** | Avaliações baseadas em dados de desempenho coletados de servidores locais. | **Tamanho de nó recomendado**: com base nos dados de utilização de CPU e memória, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de nó recomendado**: com base no tamanho do servidor local, juntamente com o tipo de nó, o tipo de armazenamento e a configuração de FTT que você seleciona para a avaliação.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Executar uma avaliação da AVS (solução do Azure VMware)

1.  Na página **Visão geral** > **Windows, Linux e SQL Server**, clique em **Avaliar e migrar servidores**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Página de visão geral das Migrações para Azure":::

1. Em **migrações para Azure: descoberta e avaliação**, clique em **avaliar**.

   ![Local do botão de avaliação](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Em **Avaliar servidores** > **Tipo de avaliação**, selecione **AVS (Solução VMware no Azure)** .

1. Em **Origem da descoberta**:

    - Se você descobriu servidores usando o dispositivo, selecione os **servidores descobertos do dispositivo de migrações para Azure**.
    - Se você descobriu servidores usando um arquivo CSV importado, selecione **servidores importados**. 
    
1. Clique em **Editar** para examinar as propriedades da avaliação.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Página para selecionar as configurações de avaliação":::
 

1. Em **Propriedades da avaliação** > **Propriedades de Destino**:

    - Em **Local de destino**, especifique a região do Azure para a qual você deseja migrar.
       - As recomendações de tamanho e custo são baseadas na localização especificada.
   - O **Tipo de armazenamento** usa **vSAN** como padrão. Esse é o tipo de armazenamento padrão para uma nuvem privada da AVS.
   - No momento, não há suporte para **Instâncias Reservadas** em nós da AVS.
1. Em **Tamanho da VM**:
    - O **Tipo de nó** usa **AV36** como padrão. As migrações para Azure recomendam o nó de nós necessários para migrar os servidores para o AVS.
    - Em **Configuração FTT, nível de RAID**, selecione a combinação RAID e Tolerância a Falhas.  A opção FTT selecionada, combinada com o requisito de disco do servidor local, determina o total de armazenamento vSAN necessário na AVS.
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
    - Na **oferta**, a [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) que você está inscrito no é exibida. A avaliação estima o custo para essa oferta.
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


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Examinar uma avaliação da AVS (solução do Azure VMware)

Uma avaliação da AVS (solução do Azure VMware) descreve:

- **Preparação da solução VMware do Azure (AVS)**: se os servidores locais são adequados para a migração para a solução VMware do Azure (AVS).
- **Número de nós de AVS**: número estimado de nós de AVS necessários para executar os servidores.
- **Utilização em nós de AVS**: utilização de CPU, memória e armazenamento projetada em todos os nós.
    - A utilização inclui o fator de início nas seguintes sobrecargas de gerenciamento de cluster, como o vCenter Server, o NSX Manager (grande), o NSX Edge, se o HCX for implantado também o HCX Manager e o dispositivo IX consumindo ~ 44vCPU (11 CPU), 75 GB de RAM e 722GB de armazenamento antes da compactação e da eliminação de duplicação.
- **Estimativa de custo mensal**: os custos mensais estimados para todos os nós da AVS (solução do Azure VMware) que executam as VMs locais.


### <a name="view-an-assessment"></a>Exibir uma avaliação

1. No **Windows, Linux e SQL Server**  >  **migrações para Azure: descoberta e avaliação**, clique no número ao lado de * * solução VMware do Azure * *.

1. Em **Avaliações**, selecione uma avaliação para abri-la. Como exemplo (estimativas e custos somente para exemplo): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Resumo da avaliação da AVS":::

1. Examine o resumo da avaliação. Você também pode editar as propriedades ou recalcular a avaliação.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Examinar a preparação da solução VMware do Azure (AVS)

1. Em **preparação do Azure**, verifique se os servidores estão prontos para migração para a AVS.

2. Examine o status do servidor:
    - **Pronto para a sincronização automática**: o servidor pode ser migrado no estado em que se encontra para o Azure (AVS) sem nenhuma alteração. Ele será iniciado no AVS com suporte completo a AVS.
    - **Pronto com condições**: o servidor pode ter problemas de compatibilidade com a versão atual do vSphere, bem como exigir ferramentas do VMware e outras configurações, antes que a funcionalidade completa do servidor possa ser obtida na AVS.
    - **Não está pronto para a AVS**: o servidor não será iniciado no AVS. Por exemplo, se o servidor local tiver um dispositivo externo anexado, como um CD-ROM, a operação do VMotion falhará (se você estiver usando VMware VMotion).
    - **Preparação desconhecida**: as migrações para Azure não puderam determinar a prontidão do servidor devido a metadados insuficientes coletados do ambiente local.

3. Examine a ferramenta sugerida:
    - **VMware HCX ou Enterprise**: para servidores VMware, a solução de HCX (extensão de nuvem híbrida) do VMware é a ferramenta de migração sugerida para migrar sua carga de trabalho local para sua nuvem privada da AVS (solução VMware) do Azure. [Saiba mais](../azure-vmware/tutorial-deploy-vmware-hcx.md).
    - **Desconhecido**: para servidores importados por meio de um arquivo CSV, a ferramenta de migração padrão é desconhecida. No entanto, para servidores VMware, é recomendável usar a solução de HCX (extensão de nuvem híbrida) do VMware. 

4. Clique em um status de **preparação de AVS** . Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar os detalhes de custo

Esta exibição mostra o custo estimado de servidores em execução na solução VMware do Azure (AVS).

1. Examinar os custos totais mensais. Os custos são agregados para todos os servidores no grupo avaliado. 

    - As estimativas de custo são baseadas no número de nós de AVS necessários considerando os requisitos de recursos de todos os servidores no total.
    - Como o preço da solução do Azure VMware (AVS) é por nó, o custo total não tem custo de computação e distribuição de custo de armazenamento.
    - A estimativa de custo é para executar os servidores locais na AVS. A avaliação da AVS não considera os custos de PaaS ou SaaS.
    
2. Você pode examinar as estimativas de custo mensal de armazenamento. Essa exibição mostra os custos agregados de armazenamento para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.

3. Você pode fazer uma busca detalhada para ver os detalhes de servidores específicos.


### <a name="review-confidence-rating"></a>Revisar classificação de confiança

Quando você executa avaliações com base no desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/how-to-create-assessment/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é fornecida.
- A classificação de confiança ajuda você a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação.
- Para o dimensionamento baseado em desempenho, as avaliações de AVS precisam dos dados de utilização da CPU e da memória do servidor. Os dados de desempenho a seguir são coletados, mas não usados em recomendações de dimensionamento para avaliações de AVS:
  - Os dados de taxa de transferência e IOPS de disco para cada disco anexado ao servidor.
  - A e/s de rede para lidar com o dimensionamento baseado em desempenho para cada adaptador de rede anexado a um servidor.

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