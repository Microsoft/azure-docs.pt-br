---
title: Planejar o gerenciamento de custos do Azure Synapse Analytics
description: Saiba como planejar e gerenciar os custos da análise de Synapse do Azure usando a análise de custo no portal do Azure.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: c7a0be6f1d402cc994532ab4bc5a5d0ea39bc8b7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599052"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planejar e gerenciar custos do Azure Synapse Analytics

Este artigo descreve como planejar e gerenciar custos do Azure Synapse Analytics. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de Synapse do Azure antes de adicionar qualquer recurso para o serviço para estimar os custos. Em seguida, ao adicionar recursos do Azure Synapse, examine os custos estimados.

Depois de começar a usar os recursos do Azure Synapse, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos do Azure Synapse são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar os custos do Azure Synapse, você será cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Estimar os custos antes de usar o Azure Synapse Analytics

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar o Azure Synapse Analytics.

O Azure Synapse tem vários recursos que têm encargos diferentes, como visto na estimativa de custo abaixo. 

![Exemplo mostrando o custo estimado na calculadora de preços do Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Entender o modelo de cobrança completo para o Azure Synapse Analytics

O Azure Synapse é executado na infraestrutura do Azure que acumula custos junto com o Azure Synapse quando você implanta o novo recurso. É importante entender que a infraestrutura adicional pode acumular o custo. Você precisa gerenciar esse custo ao fazer alterações nos recursos implantados. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Custos que normalmente se acumulam com o Azure Synapse Analytics

Quando você cria recursos para o Azure Synapse, os recursos para outros serviços do Azure também são criados. Entre elas estão:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem ser acumulados após a exclusão do recurso

Depois de excluir os recursos do Synapse do Azure, os recursos a seguir podem continuar a existir. Eles continuam acumulando os custos até que você os exclua.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Usando o crédito de pagamento antecipado do Azure com o Azure Synapse 

Você pode pagar pelos encargos do Azure Synapse com seu crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Ao criar recursos para o Azure Synapse Analytics, você verá os custos estimados. Um espaço de trabalho tem um pool SQL sem servidor criado com o espaço de trabalho. O pool SQL sem servidor não incorrerá em encargos até que você execute consultas. Outros recursos, como pools de SQL dedicados e pools de Apache Spark sem servidor, precisarão ser criados no espaço de trabalho.

Para criar um <ResourceName> e exibir o preço estimado:

1. Navegue até o serviço no portal do Azure.
2. Crie o recurso.
3. Examine o preço estimado mostrado no resumo.
4. Conclua a criação do recurso.

![Exemplo mostrando os custos estimados ao criar um recurso](./media/plan-manage-costs/create-workspace-cost.png)


Se sua assinatura do Azure tiver um limite de gastos, o Azure impedirá que você gaste em seu valor de crédito. Conforme você cria e usa os recursos do Azure, seus créditos são usados. Quando você atingir seu limite de crédito, os recursos implantados serão desabilitados para o restante desse período de cobrança. Você não pode alterar seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte [limite de gastos do Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure Synapse, você incorre em custos. Os custos de unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Assim que você começar a usar os recursos no Azure Synapse, os custos serão incorridos e você poderá ver os custos na [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Ao usar a análise de custo, você exibe os custos da análise de Synapse do Azure em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também exibe os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E você verá onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

Para exibir os custos de Synapse do Azure na análise de custo:

1. Entre no portal do Azure.
2. Abra o escopo, a assinatura ou o grupo de recursos, na portal do Azure e selecione **análise de custo** no menu. Por exemplo, vá para **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione  **análise de custo** no menu. Selecione o **escopo** para alternar para um escopo diferente na análise de custo.
3. Por padrão, o custo dos serviços é mostrado no primeiro gráfico de rosca. Selecione a área no gráfico rotulada Synapse do Azure.

Os custos mensais reais são mostrados quando a análise de custo é aberta inicialmente. Veja um exemplo que mostra todos os custos de uso mensal.

![Exemplo mostrando os custos acumulados de uma assinatura](./media/plan-manage-costs/actual-monthly-costs.png)

- Para restringir os custos de um único serviço, como o Azure Synapse, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, selecione **Azure Synapse Analytics**.

Aqui está um exemplo mostrando os custos apenas para Synapse do Azure.

![Exemplo mostrando os custos acumulados para ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

No exemplo anterior, você verá o custo atual do serviço. Os custos por regiões do Azure (locais) e custos de Synapse do Azure por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Outras maneiras de gerenciar e reduzir os custos do Azure Synapse 

### <a name="serverless-sql-pool"></a>Pool de SQL sem servidor

Para saber mais sobre os custos do pool SQL sem servidor, consulte [Gerenciamento de custos para o pool SQL sem servidor no Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Pool de SQL dedicado

Você pode controlar os custos de um pool SQL dedicado pausando o recurso quando ele não é usado. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Para obter mais informações, consulte [pausar e retomar computação no pool dedicado do SQL por meio do portal do Azure](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Integração de dados-pipelines e fluxos de dados 

Para saber mais sobre o custo de integração [de dados, consulte planejar e gerenciar custos para Azure data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Saiba mais sobre planejamento e gerenciamento de custos para [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)