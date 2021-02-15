---
title: Planejar o gerenciamento de custos para serviços cognitivas do Azure
description: Saiba como planejar e gerenciar os custos dos serviços cognitivas do Azure usando a análise de custo no portal do Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 661af89ad223978abbefd71dd8008577475c5875
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221356"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planejar e gerenciar custos para serviços cognitivas do Azure

Este artigo descreve como planejar e gerenciar os custos dos serviços cognitivas do Azure. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de serviços cognitivas antes de adicionar qualquer recurso ao serviço para estimar os custos. Em seguida, ao adicionar recursos do Azure, examine os custos estimados. Depois de começar a usar os recursos de serviços cognitivas (por exemplo, fala, Pesquisa Visual Computacional, LUIS, Análise de Texto, tradutor, etc.), use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos para serviços cognitivas são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar custos para serviços cognitivas, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Estimar os custos antes de usar serviços cognitivas

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar serviços cognitivas.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Calculadora de preços do Azure para serviços cognitivas" border="true":::

Ao adicionar novos recursos ao seu espaço de trabalho, retorne a esta calculadora e adicione o mesmo recurso aqui para atualizar suas estimativas de custo.

Para obter mais informações, consulte [preços dos serviços cognitivas do Azure](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Entenda o modelo de cobrança completo para serviços cognitivas

Os serviços cognitivas são executados na infraestrutura do Azure que [acumula custos](https://azure.microsoft.com/pricing/details/cognitive-services/) quando você implanta o novo recurso. É importante entender que a infraestrutura adicional pode acumular o custo. Você precisa gerenciar esse custo ao fazer alterações nos recursos implantados. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Custos que normalmente se acumulam com serviços cognitivas

Normalmente, depois de implantar um recurso do Azure, os custos são determinados pelo tipo de preço e pelas chamadas de API feitas para o ponto de extremidade. Se o serviço que você está usando tiver uma camada de compromisso, passar as chamadas alocadas em sua camada poderá incorrer em um encargo excedente.

Custos adicionais podem ser acumulados ao usar esses serviços:

#### <a name="qna-maker"></a>QnA Maker

Quando você cria recursos para QnA Maker, os recursos para outros serviços do Azure também podem ser criados. Entre elas estão:

- [Serviço de Azure App (para o tempo de execução)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva do Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Custos que podem ser acumulados após a exclusão do recurso

#### <a name="qna-maker"></a>QnA Maker

Depois de excluir QnA Maker recursos, os recursos a seguir podem continuar a existir. Eles continuam acumulando os custos até que você os exclua.

- [Serviço de Azure App (para o tempo de execução)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva do Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Usando o crédito de pagamento antecipado do Azure com serviços cognitivas

Você pode pagar por cobranças de serviços cognitivas com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorar custos

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Ao usar os recursos do Azure com serviços cognitivas, você incorre em custos. Os custos de unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Assim que o uso de um serviço cognitiva (ou serviços cognitivas) é iniciado, os custos são incorridos e você pode ver os custos na [análise de custo](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Ao usar a análise de custo, você exibe os custos de serviços cognitivas em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também exibe os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E você verá onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

Para exibir os custos de serviços cognitivas na análise de custo:

1. Entre no portal do Azure.
2. Abra o escopo no portal do Azure e selecione **análise de custo** no menu. Por exemplo, vá para **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione  **análise de custo** no menu. Selecione o **escopo** para alternar para um escopo diferente na análise de custo.
3. Por padrão, o custo dos serviços é mostrado no primeiro gráfico de rosca. Selecione a área no gráfico rotulada serviços cognitivas.

Os custos mensais reais são mostrados quando a análise de custo é aberta inicialmente. Veja um exemplo que mostra todos os custos de uso mensal.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Exemplo mostrando os custos acumulados de uma assinatura":::

- Para restringir os custos de um único serviço, como serviços cognitivas, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, selecione **Serviços cognitivas**.

Aqui está um exemplo que mostra os custos para serviços cognitivas.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Exemplo mostrando os custos acumulados para serviços cognitivas":::

No exemplo anterior, você verá o custo atual do serviço. Os custos por regiões do Azure (locais) e custos de serviços cognitivas por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, as equipes de finanças podem analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .