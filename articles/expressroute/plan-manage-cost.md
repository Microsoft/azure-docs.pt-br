---
title: Planejar o gerenciamento de custos do Azure ExpressRoute
description: Saiba como planejar e gerenciar os custos do Azure ExpressRoute usando a análise de custo no portal do Azure.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784234"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planejar e gerenciar custos do Azure ExpressRoute

Este artigo descreve como planejar e gerenciar os custos do ExpressRoute. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos do ExpressRoute antes de adicionar qualquer recurso ao serviço para estimar os custos. Em seguida, à medida que adicionar recursos do Azure, examine os custos estimados. 

Depois de começar a usar os recursos do ExpressRoute, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. 

Tenha em mente que os custos do ExpressRoute são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar os custos do ExpressRoute, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. 

Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Local versus Standard versus Premium

O ExpressRoute tem três SKUs de circuito diferentes: [*local*](./expressroute-faqs.md#expressroute-local), *Standard* e [*Premium*](./expressroute-faqs.md#expressroute-premium). A maneira como você é cobrado pelo uso do ExpressRoute varia entre esses três tipos de SKU. Com o SKU local, você será cobrado automaticamente com um plano de dados ilimitado. Com o SKU Standard e Premium, você pode selecionar entre um plano de dados limitado ou ilimitado. Todos os dados de entrada são livres de encargos, exceto ao usar o complemento de Alcance Global. É importante entender quais tipos de SKU e plano de dados funcionam melhor para sua carga de trabalho para otimizar melhor o custo e o orçamento.

## <a name="estimate-costs"></a>Estimar custos

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar um circuito do ExpressRoute. 

1. À esquerda, selecione **rede** e, em seguida, selecione **Azure ExpressRoute** para começar. 

1. Selecione a *zona* apropriada dependendo do seu local de emparelhamento. Consulte [provedores de conectividade do ExpressRoute](./expressroute-locations-providers.md#partners) para selecionar a *zona* apropriada na lista suspensa. 

1. Em seguida, selecione a *SKU*, a *velocidade do circuito* e o *plano de dados* para o qual você gostaria de obter uma estimativa. 

1. Na *transferência de dados de saída adicional*, insira uma estimativa em GB de quantos dados de saída você pode usar durante o curso de um mês. 

1. Por fim, você pode adicionar o *complemento alcance global* à estimativa.

A captura de tela a seguir mostra a estimativa de custo usando a calculadora:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custo de ExpressRoute na calculadora do Azure":::

Para obter mais informações, consulte [preços do Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Custo estimado do gateway do ExpressRoute

Se você estiver usando um gateway de ExpressRoute para vincular uma rede virtual ao circuito do ExpressRoute, use as etapas a seguir para estimar o custo de uso do gateway.

1. À esquerda, selecione **rede** e selecione gateway de **VPN** para começar. 

1. Selecione a *região* para o gateway e, em seguida, altere o *tipo* para **gateways de ExpressRoute**.

1. Selecione o *tipo de gateway* na lista suspensa.

1. Insira as *horas do gateway*. (720 horas = 30 dias)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Entender o modelo de cobrança completo do ExpressRoute

O ExpressRoute é executado na infraestrutura do Azure que acumula custos junto com o ExpressRoute quando você implanta o novo recurso. É importante entender que a infraestrutura adicional pode acumular o custo. Você precisa gerenciar esse custo ao fazer alterações nos recursos implantados. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Custos que normalmente se acumulam com o ExpressRoute

Ao criar um circuito de ExpressRoute, você pode optar por criar um gateway de ExpressRoute para vincular sua rede virtual ao circuito. Os gateways são cobrados a uma taxa por hora mais o custo de um circuito do ExpressRoute. Consulte [preços do expressroute](https://azure.microsoft.com/en-us/pricing/details/expressroute) e selecione gateways do expressroute para ver taxas para SKUs de gateway diferentes.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem ser acumulados após a exclusão do recurso

Se você tiver um gateway de ExpressRoute após excluir o circuito de ExpressRoute, ainda será cobrado pelo custo até que você o exclua.

### <a name="using-azure-prepayment-credit"></a>Usando o crédito de pagamento antecipado do Azure

Você pode pagar por encargos do ExpressRoute com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure com o ExpressRoute, você incorre em custos. Os custos de unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Assim que o ExpressRoute é iniciado, os custos são incorridos e você pode ver os custos na [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Ao usar a análise de custo, você exibe os custos de circuito do ExpressRoute em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também exibe os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E você verá onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

Para exibir os custos do ExpressRoute na análise de custo:

1. Entre no portal do Azure.

1. Vá para **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione  **análise de custo** no menu. Selecione o **escopo** para alternar para um escopo diferente na análise de custo. Por padrão, o custo dos serviços é mostrado no primeiro gráfico de rosca.

    Os custos mensais reais são mostrados quando a análise de custo é aberta inicialmente. Veja um exemplo que mostra todos os custos de uso mensal.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Exemplo mostrando os custos acumulados de uma assinatura":::
    

1.  Para restringir os custos de um único serviço, como o Expressroute, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, selecione **ExpressRoute**.

    Aqui está um exemplo mostrando os custos para apenas o ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Exemplo mostrando os custos acumulados para o ExpressRoute":::

No exemplo anterior, você verá o custo atual do serviço. Os custos por regiões do Azure (locais) e custos do ExpressRoute por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets-and-alerts"></a>Criar orçamentos e alertas

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como os preços funcionam com o Azure ExpressRoute. Confira [preços de visão geral do Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .