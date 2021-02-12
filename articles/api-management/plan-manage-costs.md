---
title: Planejar e gerenciar custos para o gerenciamento de API do Azure
description: Saiba como planejar e gerenciar os custos do gerenciamento de API do Azure usando a análise de custo no portal do Azure.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581322"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planejar e gerenciar custos para o gerenciamento de API

Este artigo descreve como planejar e gerenciar custos para o gerenciamento de API do Azure. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos de gerenciamento de API antes de adicionar qualquer recurso para o serviço para estimar os custos. Depois de começar a usar os recursos de gerenciamento de API, use os recursos de gerenciamento de custos para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. 

Os custos de gerenciamento de API são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar custos para o gerenciamento de API, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custo no gerenciamento de custos dá suporte à maioria dos tipos de conta do Azure, mas não a todos eles. Para exibir a lista completa dos tipos de contas compatíveis, confira [Entender os dados do Gerenciamento de Custos](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para exibir dados de custo, você precisa de, pelo menos, acesso de leitura para uma conta do Azure. Para obter informações sobre como atribuir o acesso aos dados do Gerenciamento de Custos do Azure, confira [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Estimar os custos antes de usar o gerenciamento de API

Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar o gerenciamento de API. 

1. Pesquise *Gerenciamento de API* ou selecione gerenciamento de API de **integração**  >  .
1. Selecione **Exibir** para adicionar uma estimativa de custo padrão para uma instância de serviço de gerenciamento de API.

> [!NOTE]
> Os custos mostrados neste exemplo são apenas para fins de demonstração. Consulte [preços do gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/) para obter as informações de preços mais recentes.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Estimar custos para a camada de desenvolvedor":::

* A estimativa de custo padrão é baseada em uma instância do serviço de gerenciamento de API na [camada de serviço](api-management-features.md) do **desenvolvedor** com uma [unidade de capacidade](api-management-capacity.md). A camada de desenvolvedor é para casos de uso e avaliações de não produção. Ele não é apoiado por um contrato de nível de serviço.

* Para estimar os custos de unidades de capacidade adicionais ou de uma camada de serviço diferente, selecione outras opções nas listas suspensas **unidades** e **camada** .

* Dependendo da camada de serviço e disponibilidade do recurso, encargos adicionais podem ser aplicados para uso de [gateways hospedados internamente](self-hosted-gateway-overview.md).

Para obter mais detalhes sobre preços e recursos, consulte:

* [Preços do gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/)
* [Comparação baseada em recursos dos níveis de gerenciamento de API do Azure](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Usando crédito monetário com o gerenciamento de API

Você pode pagar por cobranças de gerenciamento de API com seu pagamento antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure com o gerenciamento de API, você incorre em custos. Os custos de unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias) ou por uso de unidade (bytes, megabytes e assim por diante). Assim que o gerenciamento de API é iniciado, os custos são incorridos e você pode ver os custos na [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Ao usar a análise de custo, você exibe os custos de gerenciamento de API em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também exibe os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E você verá onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.

> [!NOTE]
> Os custos mostrados neste exemplo são apenas para fins de demonstração. Os custos variam dependendo do uso de recursos e dos preços atuais.

Para exibir os custos de gerenciamento de API na análise de custo:

1. Entre no [portal do Azure](https://azure.microsoft.com).
1. Abra a janela **Gerenciamento de custos + cobrança** , selecione **Gerenciamento de custos** no menu e, em seguida, selecione um escopo de **cobrança**. Por exemplo, selecione uma assinatura na lista.
1. Selecione **Gerenciamento de custos** no menu e, em seguida, selecione **análise de custo**.
1. Por padrão, os custos mensais de todos os serviços são mostrados no primeiro gráfico de rosca. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Custos mensais da assinatura":::

1. Para restringir os custos de um único serviço, como o gerenciamento de API, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, selecione **Gerenciamento de API**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Exemplo mostrando os custos acumulados para o gerenciamento de API":::

No exemplo anterior, você verá o custo atual do serviço. Os custos por regiões do Azure (locais) e custos de gerenciamento de API por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro adicional. Para obter mais informações sobre as opções de filtro ao criar um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa de outras pessoas para realizar análise de dados adicional para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Outras maneiras de gerenciar e reduzir custos para o gerenciamento de API

### <a name="choose-tier"></a>Escolher camada

Examine a [comparação baseada em recursos das camadas de gerenciamento de API do Azure](api-management-features.md) para ajudar a decidir qual camada de serviço pode ser apropriada para seus cenários. As diferentes camadas de serviço dão suporte a combinações de recursos e funcionalidades projetadas para vários casos de uso, com custos diferentes. 

* A camada de serviço de **consumo** fornece uma opção leve e sem servidor que incorre em custos fixos. Você será cobrado com base no número de chamadas de API para o serviço acima de um determinado limite. A capacidade também é dimensionada automaticamente com base na carga no serviço.
* As camadas de gerenciamento de API de **desenvolvedor**, **básica**, **Standard** e **Premium** incorrem em custos mensais e fornecem maior taxa de transferência e conjuntos de recursos mais avançados para cargas de trabalho de avaliação e produção. [Atualize](upgrade-and-scale.md) para uma camada de serviço diferente a qualquer momento.

### <a name="scale-using-capacity-units"></a>Dimensionar usando unidades de capacidade

Exceto na camada de serviço de consumo, o gerenciamento de API dá suporte ao dimensionamento adicionando ou removendo [*unidades de capacidade*](api-management-capacity.md). À medida que a carga aumenta em uma instância de gerenciamento de API, a adição de unidades de capacidade pode ser mais econômica do que a atualização para uma camada de serviço superior. O número máximo de unidades depende da camada de serviço.

Cada unidade de capacidade tem um determinado recurso de processamento de solicitação que depende da camada do serviço. Por exemplo, uma unidade da camada básica tem uma taxa de transferência máxima estimada de aproximadamente 1.000 solicitações por segundo. 

À medida que você adiciona ou remove unidades, capacidade e escala de custo proporcionalmente. Por exemplo, duas unidades da camada Standard fornecem uma taxa de transferência estimada de aproximadamente 2.000 solicitações por segundo. A taxa de transferência real pode ser diferente devido ao tamanho das solicitações ou respostas, aos padrões de conexão, ao número de clientes que fazem solicitações e a outros fatores.

[Monitore](api-management-howto-use-azure-monitor.md) a métrica de capacidade para sua instância de gerenciamento de API para ajudar a tomar decisões quanto a dimensionar ou atualizar uma instância de gerenciamento de API para acomodar mais carga.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .
- Saiba mais sobre a [capacidade](api-management-capacity.md)de gerenciamento de API.
- Consulte as etapas para dimensionar e atualizar o gerenciamento de API usando o [portal do Azure](upgrade-and-scale.md)e saiba mais sobre o [dimensionamento](api-management-howto-autoscale.md)automático.
