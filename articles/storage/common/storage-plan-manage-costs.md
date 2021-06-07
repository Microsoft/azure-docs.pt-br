---
title: Planejar e gerenciar custos para o armazenamento de blobs do Azure
description: Saiba como planejar e gerenciar os custos do armazenamento de BLOBs do Azure usando a análise de custo no portal do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0bad4637f13bbcf02047416499e4f82fdc53eb4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98601307"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planejar e gerenciar custos para o armazenamento de blobs do Azure

Este artigo ajuda você a planejar e gerenciar os custos do armazenamento de BLOBs do Azure. Primeiro, estimar os custos usando a calculadora de preços do Azure. Depois de criar sua conta de armazenamento, otimize a conta para que você pague apenas pelo que precisa. Use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e monitorar as tendências de gastos para identificar as áreas em que talvez queira agir.

Tenha em mente que os custos para o armazenamento de BLOBs são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como estimar e gerenciar os custos do armazenamento de BLOBs, você será cobrado por todos os serviços e recursos do Azure usados para sua assinatura do Azure, incluindo os serviços de terceiros. Depois de estar familiarizado com o gerenciamento de custos para o armazenamento de BLOBs, você pode aplicar métodos semelhantes para gerenciar os custos de todos os serviços do Azure usados em sua assinatura.

## <a name="estimate-costs"></a>Estimar custos

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar e começar a transferir dados para uma conta de armazenamento do Azure.

1. Na página [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , escolha o bloco **contas de armazenamento** .

2. Role a página e localize a seção **contas de armazenamento** da sua estimativa.

3. Escolha opções nas listas suspensas. 

   Conforme você modifica o valor dessas listas suspensas, a estimativa de custo é alterada. Essa estimativa aparece no canto superior, bem como na parte inferior da estimativa.

   ![Captura de tela mostrando sua estimativa](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Conforme você altera o valor da lista suspensa **tipo** , outras opções que aparecem nessa planilha também são alteradas. Use os links na seção **mais informações** para saber mais sobre o que cada opção significa e como essas opções afetam o preço das operações relacionadas ao armazenamento. 

4. Modifique as opções restantes para ver seus efeitos em sua estimativa.

   > [!NOTE]
   > Você pode pagar pelos encargos do armazenamento de BLOBs do Azure com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="optimize-costs"></a>Otimizar custos

Considere o uso dessas opções para reduzir os custos. 

- Reservar capacidade de armazenamento

- Organizar dados em camadas de acesso

- Mover dados automaticamente entre as camadas de acesso

Esta seção aborda cada opção em mais detalhes. 

#### <a name="reserve-storage-capacity"></a>Reservar capacidade de armazenamento

Você pode economizar dinheiro em custos de armazenamento para dados de blob com capacidade reservada de armazenamento do Azure. A capacidade reservada do armazenamento do Azure oferece um desconto sobre a capacidade para BLOBs de blocos e para Azure Data Lake Storage Gen2 dados em contas de armazenamento padrão quando você se compromete com uma reserva por um ou três anos. Uma reserva fornece uma quantidade fixa de capacidade de armazenamento para o termo da reserva. A capacidade reservada de armazenamento do Azure pode reduzir significativamente os custos de capacidade para BLOBs de bloco e dados de Azure Data Lake Storage Gen2. 

Para saber mais, confira [otimizar os custos para o armazenamento de BLOBs com capacidade reservada](../blobs/storage-blob-reserved-capacity.md).

#### <a name="organize-data-into-access-tiers"></a>Organizar dados em camadas de acesso

Você pode reduzir os custos colocando dados de blob nas camadas de acesso mais econômicas. Escolha entre três camadas que foram projetadas para otimizar seus custos em relação ao uso de dados. Por exemplo, a camada *quente* tem um custo de armazenamento maior, mas menor custo de acesso. Portanto, se você planeja acessar os dados com frequência, a camada quente pode ser a opção mais econômica. Se você planeja acessar dados com menos frequência, a camada *fria* ou de *arquivo* pode fazer mais sentido, pois aumenta o custo de acesso aos dados, reduzindo o custo de armazenamento de dados.    

Para saber mais, confira [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Mover dados automaticamente entre as camadas de acesso

Use políticas de gerenciamento do ciclo de vida para mover dados periodicamente entre as camadas para economizar mais dinheiro. Essas políticas podem mover dados para o usando regras que você especificar. Por exemplo, você pode criar uma regra que mova os BLOBs para a camada de arquivo se esse BLOB não tiver sido modificado em 90 dias. Ao criar políticas que ajustam a camada de acesso de seus dados, você pode criar as opções de armazenamento menos caras para suas necessidades.

Para saber mais, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

## <a name="create-budgets"></a>Criar orçamentos

É possível criar [orçamentos](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar alertas que notificam automaticamente os stakeholders de anomalias de gastos e risco de gastos em excesso. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. No entanto, eles podem ter funcionalidade limitada para gerenciar os custos de serviço do Azure individuais como o custo do armazenamento do Azure, pois eles foram projetados para controlar os custos em um nível mais alto.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure com o armazenamento do Azure, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Os custos são incorridos assim que o uso do armazenamento do Azure é iniciado. Você pode ver os custos no painel [análise de custo](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) na portal do Azure.

Ao usar a análise de custo, você pode exibir os custos de armazenamento do Azure em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também pode exibir os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

>[!NOTE]
> A análise de custo dá suporte a diferentes tipos de conta do Azure. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Para exibir os custos de armazenamento do Azure na análise de custo:

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. Abra a janela **Gerenciamento de custos + cobrança** , selecione **Gerenciamento de custos** no menu e, em seguida, selecione análise de **custo**. Em seguida, você pode alterar o escopo de uma assinatura específica da lista suspensa **escopo** .

   ![Captura de tela mostrando escopo](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Para exibir apenas os custos do armazenamento do Azure, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, escolha **armazenamento** na lista. 

   Veja um exemplo que mostra os custos apenas para o armazenamento do Azure:

   ![Captura de tela mostrando filtrar por armazenamento](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

No exemplo anterior, você verá o custo atual do serviço. Os custos pelas regiões do Azure (locais) e por grupo de recursos também são exibidos. Você também pode adicionar outros filtros (por exemplo: um filtro para ver os custos de contas de armazenamento específicas).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como os preços funcionam com o armazenamento do Azure. Consulte [visão geral do armazenamento do Azure preços](https://azure.microsoft.com/pricing/details/storage/).
- [Otimize os custos para o armazenamento de BLOBs com capacidade reservada](../blobs/storage-blob-reserved-capacity.md).
- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .