---
title: Planejar e gerenciar custos para Azure Cosmos DB
description: Saiba como planejar e gerenciar custos para Azure Cosmos DB usando a análise de custo no portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 2bea2324817986654de6689a2be15d0cbf999b38
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602139"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planejar e gerenciar custos para Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo descreve como você pode planejar e gerenciar custos para Azure Cosmos DB. Primeiro, use a calculadora de capacidade de Azure Cosmos DB para estimar o custo da carga de trabalho antes de criar qualquer recurso. Posteriormente, você pode examinar o custo estimado e começar a criar seus recursos.

Depois de começar a usar Azure Cosmos DB recursos, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos para Azure Cosmos DB são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar custos para Azure Cosmos DB, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="provisioned-throughput-or-serverless"></a>Taxa de transferência provisionada ou sem servidor

O Azure Cosmos DB dá suporte a dois tipos de modos de capacidade: [taxa de transferência provisionada](set-throughput.md) e sem [servidor](serverless.md). A maneira como você é cobrado pelo seu uso de Azure Cosmos DB varia muito entre esses dois modos, portanto, é importante escolher aquele que funcione melhor para sua carga de trabalho. Consulte o artigo [como escolher entre produtividade provisionada e sem servidor](throughput-serverless.md) para obter diretrizes e recomendações sobre como fazer essa escolha.

### <a name="cost-analysis"></a>Análise de custo

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Estimando os custos de taxa de transferência provisionada antes de usar Azure Cosmos DB

Se você planeja usar Azure Cosmos DB no modo de taxa de transferência provisionado, use a [calculadora de capacidade de Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para estimar os custos antes de criar os recursos em uma conta do Azure Cosmos. A calculadora de capacidade é usada para obter uma estimativa da taxa de transferência e custo necessários de sua carga de trabalho. Configurar seus bancos de dados do Azure Cosmos e contêineres com a quantidade certa de taxa de transferência provisionada ou de [unidades de solicitação (ru/s)](request-units.md)para sua carga de trabalho é essencial para otimizar o custo e o desempenho. Você precisa inserir detalhes como tipo de API, número de regiões, tamanho do item, solicitações de leitura/gravação por segundo, total de dados armazenados para obter uma estimativa de custo. Para saber mais sobre a calculadora de capacidade, consulte o artigo de [estimativa](estimate-ru-with-capacity-planner.md) .

A captura de tela a seguir mostra a taxa de transferência e estimativa de custo usando a calculadora de capacidade:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custo na calculadora de capacidade Azure Cosmos DB":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Estimando os custos sem servidor antes de usar o Azure Cosmos DB

Se você planeja usar Azure Cosmos DB no modo sem servidor, você precisa estimar quantas [unidades de solicitação](request-units.md) e GB de armazenamento você pode consumir mensalmente. Você pode estimar a quantidade necessária de unidades de solicitação avaliando o número de operações de banco de dados que seriam emitidas em um mês e multiplicando sua quantidade pelo custo de RU correspondente. A tabela a seguir lista as cobranças de RU estimadas para operações de banco de dados comuns:

| Operação | Custo estimado | Observações |
| --- | --- | --- |
| Criar um item | 5 RUs | Custo médio para um item de 1 KB com menos de 5 Propriedades para indexar |
| Atualizar um item | 10 RUs | Custo médio para um item de 1 KB com menos de 5 Propriedades para indexar |
| Ler um item individual por sua ID e chave de partição (ponto-leitura) | 1 RU | Custo médio para um item de 1 KB |
| Excluir um item | 5 RUs | |
| Executar uma consulta | 10 RUs | Custo médio de uma consulta que aproveita ao máximo a [indexação](index-overview.md) e retorna 100 resultados ou menos |

> [!IMPORTANT] 
> Preste atenção às anotações da tabela acima. Para obter uma estimativa mais precisa dos custos reais de suas operações, você pode usar o [emulador de Azure Cosmos DB](local-emulator.md) e [medir o custo de ru exato de suas operações](find-request-unit-charge.md). Embora o emulador de Azure Cosmos DB não dê suporte a servidor, ele relata um encargo de RU padrão para operações de banco de dados e pode ser usado para essa estimativa.

Depois de calcular o número total de unidades de solicitação e GB de armazenamento que você provavelmente consumirá em um mês, a fórmula a seguir retornará sua estimativa de custo: **([número de unidades de solicitação]/1 milhão * $0.25) + ([GB de armazenamento] * $0.25)**.

> [!NOTE]
> Os custos mostrados no exemplo anterior são apenas para fins de demonstração. Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as informações de preços mais recentes.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Ao começar a usar Azure Cosmos DB recursos do portal do Azure, você pode ver os custos estimados. Use as seguintes etapas para examinar a estimativa de custo:

1. Entre no portal do Azure e navegue até sua conta do Azure Cosmos.
1. Vá para a seção **visão geral** .
1. Verifique o gráfico de **custo** na parte inferior. Este gráfico mostra uma estimativa de seu custo atual em um período de tempo configurável:
1. Crie um novo contêiner, como um contêiner de grafo.
1. Insira a taxa de transferência necessária para sua carga de trabalho, como 400 RU/s. Depois de inserir o valor da taxa de transferência, você poderá ver a estimativa de preços, conforme mostrado na seguinte captura de tela:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Estimativa de custo no portal do Azure":::

Se sua assinatura do Azure tiver um limite de gastos, o Azure impedirá que você gaste em seu valor de crédito. Conforme você cria e usa os recursos do Azure, seus créditos são usados. Quando você atingir seu limite de crédito, os recursos implantados serão desabilitados para o restante desse período de cobrança. Você não pode alterar seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte [limite de gastos do Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Você pode pagar por Azure Cosmos DB encargos com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar recursos com Azure Cosmos DB, você incorre em custos. Os custos da unidade de uso de recursos variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso da unidade de solicitação. Assim que o uso de Azure Cosmos DB é iniciado, os custos são incorridos e você pode vê-los no painel de [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) na portal do Azure.

Ao usar a análise de custo, você pode exibir os custos de Azure Cosmos DB em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, atual, mês anterior e ano. Você também pode exibir os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos e ver onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos. 

Para exibir os custos de Azure Cosmos DB na análise de custo:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Abra o escopo no portal do Azure e selecione **análise de custo** no menu. Por exemplo, vá para **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione  **análise de custo** no menu. Selecione o **escopo** para alternar para um escopo diferente na análise de custo.

1. Por padrão, o custo de todos os serviços é mostrado no primeiro gráfico de rosca. Selecione a área no gráfico rotulada como "Azure Cosmos DB".

1. Para restringir os custos de um único serviço, como Azure Cosmos DB, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, escolha **Azure Cosmos DB** na lista. Veja um exemplo que mostra os custos para apenas Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Monitorar custos com o painel de análise de custo":::

No exemplo anterior, você verá o custo atual de Azure Cosmos DB para o mês de fev. Os gráficos também contêm Azure Cosmos DB custos por local e por grupo de recursos.

## <a name="create-budgets"></a>Criar orçamentos

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para saber mais sobre como os preços funcionam no Azure Cosmos DB:

* [Modelo de preços no Azure Cosmos DB](how-pricing-works.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Otimizar o custo de armazenamento no Azure Cosmos DB](optimize-cost-storage.md)
* Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .