---
title: Planejar o gerenciamento de custos para aplicativos lógicos do Azure
description: Saiba como planejar e gerenciar custos para aplicativos lógicos do Azure usando a análise de custo no portal do Azure
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180415"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planejar e gerenciar custos para aplicativos lógicos do Azure

Este artigo ajuda você a planejar e gerenciar custos para aplicativos lógicos do Azure. Antes de criar ou adicionar recursos usando esse serviço, estime os custos usando a calculadora de preços do Azure. Depois de começar a usar os recursos dos aplicativos lógicos, você pode definir orçamentos e monitorar custos usando o [Gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para identificar as áreas em que você pode querer agir, também é possível examinar os custos previstos e monitorar as tendências de gastos.

Tenha em mente que os custos dos aplicativos lógicos são apenas parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como estimar e gerenciar os custos dos aplicativos lógicos, você será cobrado por todos os serviços e recursos do Azure que são usados na sua assinatura do Azure, incluindo qualquer serviço de terceiros. Depois de estar familiarizado com o gerenciamento de custos para aplicativos lógicos, você pode aplicar métodos semelhantes para gerenciar os custos de todos os serviços do Azure usados em sua assinatura.

## <a name="prerequisites"></a>Pré-requisitos

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

O [Gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) dá suporte à maioria dos tipos de conta do Azure Para exibir todos os tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir os dados de custo, você precisará de, pelo menos, acesso de leitura em sua conta do Azure.

Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Entender o modelo de cobrança

Os aplicativos lógicos do Azure são executados na infraestrutura do Azure que [acumula custos](https://azure.microsoft.com/pricing/details/logic-apps/) quando você implanta novos recursos. Verifique se você entendeu o [modelo de cobrança para o serviço de aplicativos lógicos, juntamente com os recursos relacionados do Azure](logic-apps-pricing.md), e gerencie os custos devido a essas dependências ao fazer alterações nos recursos implantados.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Custos que normalmente se acumulam com os aplicativos lógicos do Azure

O serviço de aplicativos lógicos aplica modelos de preços diferentes, com base nos recursos que você cria e usa:

* Os recursos do aplicativo lógico que você cria e executa no serviço de aplicativos lógicos multilocatários usam um [modelo de preços de consumo](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Os recursos do aplicativo lógico que você cria e executa em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) usam um [modelo de preço fixo](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Aqui estão outros recursos que incorrem em custos quando você os cria para uso com aplicativos lógicos:

* Uma [conta de integração](../logic-apps/logic-apps-pricing.md#integration-accounts) é um recurso separado que você cria e vincula a aplicativos lógicos para criar integrações B2B. As contas de integração usam um [modelo de preço fixo](../logic-apps/logic-apps-pricing.md#integration-accounts) em que a taxa é baseada no tipo de conta de integração ou *camada* que você usa.

* Um [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) é um recurso separado que você cria como um local de implantação para aplicativos lógicos que precisam de acesso direto aos recursos em uma rede virtual. ISEs use um [modelo de preço fixo](../logic-apps/logic-apps-pricing.md#fixed-pricing) em que a taxa se baseia na SKU do ISE que você cria e em outras configurações.

* Um [conector personalizado](../logic-apps/logic-apps-pricing.md#consumption-pricing) é um recurso separado que você cria para uma API REST que não tem um conector predefinido para uso em seus aplicativos lógicos. As execuções do conector personalizado usam um [modelo de preços de consumo](../logic-apps/logic-apps-pricing.md#consumption-pricing) , exceto quando você as usa em um ISE.

* No serviço aplicativos lógicos multilocatários, a [retenção de dados e o consumo de armazenamento](../logic-apps/logic-apps-pricing.md#data-retention) acumulam custos usando um [modelo de preço fixo](../logic-apps/logic-apps-pricing.md#fixed-pricing). Por exemplo, as entradas e saídas do histórico de execução são mantidas no armazenamento em bastidores, que difere dos recursos de armazenamento que você cria, gerencia e acessa de forma independente do seu aplicativo lógico.

  Em um ISE, a retenção de dados e o consumo de armazenamento não incorrem em custos.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem ser acumulados após a exclusão do recurso

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Depois de excluir um aplicativo lógico, o serviço de aplicativos lógicos não criará ou executará novas instâncias de fluxo de trabalho. No entanto, todas as execuções em andamento e pendentes continuam até que sejam concluídas. Dependendo do número dessas execuções, esse processo pode levar algum tempo. Para obter mais informações, consulte [gerenciar aplicativos lógicos](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Se você tiver esses recursos depois de excluir um aplicativo lógico, esses recursos continuarão a existir e acumularão os custos até você excluí-los:

* Recursos do Azure que você cria e gerencia independentemente do aplicativo lógico que se conecta a esses recursos, por exemplo, aplicativos de funções do Azure, hubs de eventos, grades de eventos e assim por diante

* Contas de integração

* Ambientes do serviço de integração (ISEs)

  Se você [excluir um ISE](ise-manage-integration-service-environment.md#delete-ise), a rede virtual do Azure associada, as sub-redes e outros recursos relacionados continuarão a existir. Depois de excluir o ISE, talvez seja necessário aguardar até um número específico de horas antes de tentar excluir a rede virtual ou sub-redes.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Usando crédito monetário com aplicativos lógicos do Azure

Você pode pagar pelos encargos dos aplicativos lógicos do Azure com seu crédito de compromisso monetário de EA. No entanto, você não pode usar crédito de compromisso monetário de EA para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Estimar custos

Antes de criar recursos com os aplicativos lógicos do Azure, estime os custos usando a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/). Para obter mais informações, consulte [modelo de preços para aplicativos lógicos do Azure](../logic-apps/logic-apps-pricing.md).

1. Na [página calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), no menu à esquerda, selecione **integração**  >  **aplicativos lógicos do Azure**.

   ![Captura de tela que mostra a calculadora de preços do Azure com "aplicativos lógicos do Azure" selecionado.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Role a página até que você possa exibir a calculadora de preços dos aplicativos lógicos do Azure. Nas várias seções para recursos do Azure que estão diretamente relacionadas aos aplicativos lógicos do Azure, insira os números de recursos que você planeja usar e o número de intervalos nos quais você pode usar esses recursos.

   Esta captura de tela mostra um exemplo de estimativa de custo usando a calculadora:

   ![Exemplo mostrando o custo estimado na calculadora de preços do Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Para atualizar suas estimativas de custo conforme você cria e usa novos recursos relacionados, retorne a esta calculadora e atualize esses recursos aqui.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Criar orçamentos e alertas

Para ajudá-lo a gerenciar de forma proativa os custos de sua conta ou assinatura do Azure, você pode criar [orçamentos](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) e [alertas](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) usando o serviço e os recursos de [cobrança e gerenciamento de custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .  Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral.

Com base nos gastos em comparação com os limites de orçamento e custo, os alertas notificam automaticamente os participantes sobre a perda de anomalias e a perda de riscos. Se você quiser mais granularidade no monitoramento, também poderá criar orçamentos que usam filtros para recursos ou serviços específicos no Azure. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro extra. Para obter mais informações sobre as opções de filtro, consulte [Opções de grupo e filtro](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorar custos

Os custos da unidade de uso de recursos variam de acordo com os intervalos de tempo, como segundos, minutos, horas e dias, ou por uso de unidade, como bytes, megabytes e assim por diante. Alguns exemplos são por dia, mês atual e anterior e ano. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. Ao usar os recursos de análise de custo, você pode exibir os custos como grafos e tabelas em vários intervalos de tempo. Se você tiver criado orçamentos e previsões de custo, também poderá encontrar facilmente onde os orçamentos são excedidos e que o excesso de gastos pode ter ocorrido.

Depois de começar a incorrer em custos para recursos que criam ou começam a usar o no Azure, você pode revisar e monitorar esses custos das seguintes maneiras:

* [Monitorar execuções de aplicativo lógico e consumo de armazenamento](#monitor-billing-metrics) usando Azure monitor

* Executar [análise de custo](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) usando o [Gerenciamento de custos e a cobrança do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorar execuções de aplicativo lógico e consumo de armazenamento

Usando Azure Monitor, você pode exibir essas métricas para um aplicativo lógico específico:

* Execuções de ações faturáveis
* Execuções de gatilho Faturável
* Uso de cobrança para execuções de operação nativa
* Uso de cobrança para execuções de conector padrão
* Uso de cobrança para consumo de armazenamento
* Total de execuções faturáveis

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Exibir métricas de consumo de armazenamento e execução

1. No portal do Azure, encontre e abra seu aplicativo lógico. No menu do aplicativo lógico, em **monitoramento**, selecione **métricas**.

1. No painel do lado direito, em **título do gráfico**, na barra métrica, abra a lista **métrica** e selecione a métrica desejada.

   > [!NOTE]
   > O consumo de armazenamento é medido como o número de unidades de armazenamento (GB) que seu aplicativo lógico usa e é cobrado. As execuções que usam menos de 500 MB no armazenamento podem não aparecer na exibição de monitoramento, mas ainda são cobradas.

   ![Captura de tela que mostra o painel de métricas com a lista "métrica" aberta.](./media/logic-apps-pricing/select-metric.png)

1. No canto superior direito do painel, selecione o período de tempo desejado.

1. Para exibir outros dados de consumo de armazenamento, especificamente os tamanhos de entrada e saída de ação no histórico de execução do aplicativo lógico, [siga estas etapas](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Exibir os tamanhos de entrada e saída da ação no histórico de execuções

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. No painel do lado direito, em **histórico de execuções**, selecione a execução que tem as entradas e saídas que você deseja exibir.

1. Em **execução do aplicativo lógico**, selecione **detalhes da execução**.

1. No painel **detalhes de execução do aplicativo lógico** , na tabela ações, que lista o status e a duração de cada ação, selecione a ação que você deseja exibir.

1. No painel de **ação do aplicativo lógico** , localize os tamanhos das entradas e saídas da ação. Em link de **entradas** e **link de saídas**, localize os links para essas entradas e saídas.

   > [!NOTE]
   > Para loops, somente as ações de nível superior mostram tamanhos para suas entradas e saídas. Para ações dentro de loops aninhados, entradas e saídas mostram tamanho zero e nenhum link.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Executar análise de custo usando o gerenciamento de custos e a cobrança do Azure

Para examinar os custos do serviço de aplicativos lógicos com base em um escopo específico, por exemplo, uma assinatura do Azure, você pode usar os recursos de [análise de custo](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) no [Gerenciamento de custos e na cobrança do Azure](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. No portal do Azure, abra o escopo desejado, como sua assinatura do Azure. No menu à esquerda, em **Gerenciamento de custos**, selecione **análise de custo**.

   Quando você abre o painel de análise de custo pela primeira vez, o grafo superior mostra os custos de uso reais e previstos em todos os serviços na assinatura para o mês atual.

   ![Captura de tela que mostra portal do Azure e o painel de análise de custo com exemplo para custos reais e previstos em uma assinatura.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Para alterar os escopos, no painel **análise de custo** , na barra filtros, selecione o filtro de **escopo** . No painel **selecionar escopo** , alterne para o escopo desejado.

   Abaixo, os gráficos de rosca mostram os custos atuais pelos serviços do Azure, pela região do Azure (local) e por grupo de recursos.

   ![Captura de tela que mostra portal do Azure e o painel de análise de custo com gráficos de rosca de exemplo para serviços, regiões e grupos de recursos.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Para filtrar o gráfico para uma área específica, como um serviço ou recurso, na barra filtros, selecione **Adicionar filtro**.

1. Na lista do lado esquerdo, selecione o tipo de filtro, por exemplo, **nome do serviço**. Na lista do lado direito, selecione o filtro, por exemplo, **aplicativos lógicos**. Quando terminar, selecione a marca de seleção verde.

   ![Captura de tela que mostra portal do Azure e o painel de análise de custo com seleções de filtro.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Por exemplo, aqui está o resultado para o serviço de aplicativos lógicos:

   ![Captura de tela que mostra portal do Azure e o painel análise de custo com os resultados filtrados em "aplicativos lógicos".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Exportar dados de custo

Quando você precisar fazer mais análises de dados sobre custos, poderá [exportar dados de custo](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Por exemplo, uma equipe de Finanças pode analisar esses dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Outras maneiras de gerenciar e reduzir custos

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Para ajudá-lo a reduzir os custos de seus APS lógicos e recursos relacionados, tente estas opções:

* Se possível, use [gatilhos e ações internas](../connectors/apis-list.md#built-in), que custam menos para serem executados por execução do que os [gatilhos e ações do conector gerenciado](../connectors/apis-list.md#managed-connectors).

  Por exemplo, você poderá reduzir os custos ao acessar outros recursos usando a [ação http](../connectors/connectors-native-http.md) ou chamando uma função que você criou usando o [serviço de Azure Functions](../azure-functions/functions-overview.md) e usando a [ação de Azure Functions interna](../logic-apps/logic-apps-azure-functions.md). No entanto, usar Azure Functions também incorre em custos, portanto, certifique-se de comparar suas opções.

* [Especifique condições de gatilho precisas](logic-apps-workflow-actions-triggers.md#trigger-conditions) para executar um fluxo de trabalho.

  Por exemplo, você pode especificar que um gatilho seja acionado somente quando o site de destino retornar um erro de servidor interno. Na definição JSON do gatilho, use a `conditions` propriedade para especificar uma condição que referencie o código de status do gatilho.

* Se um gatilho tiver uma versão de sondagem e uma versão de webhook, tente a versão do webhook, que aguarda que o evento especificado ocorra antes do acionamento, em vez de verificar regularmente o evento.

* Chame seu aplicativo lógico por meio de outro serviço para que o gatilho seja acionado somente quando o fluxo de trabalho deve ser executado.

  Por exemplo, você pode chamar seu aplicativo lógico a partir de uma função que você cria e executa usando o serviço de Azure Functions. Por exemplo, consulte [chamar ou disparar aplicativos lógicos usando o Azure Functions e o barramento de serviço do Azure](logic-apps-scenario-function-sb-trigger.md).

* [Desabilite os aplicativos lógicos](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) que não precisam ser executados constantemente ou [exclua os aplicativos lógicos](manage-logic-apps-with-azure-portal.md#delete-logic-apps) que não são mais necessários. Se possível, desabilite todos os outros recursos que não são necessários constantemente ativos.

## <a name="next-steps"></a>Próximas etapas

* [Otimizar seu investimento na nuvem com o Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Gerenciar custos usando a análise de custo](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Evitar custos inesperados](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Faça o curso de aprendizado guiado de [Gerenciamento de custos](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)


