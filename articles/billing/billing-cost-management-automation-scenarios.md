---
title: Cenários de automação para gerenciamento de custos e de cobrança do Azure |Microsoft Docs
description: Saiba como cenários comuns de gerenciamento de cobrança e de custos são mapeados para diferentes APIs.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443247"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Cenários de automação para gerenciamento de custos e de cobrança

Este artigo lista os cenários comuns de gerenciamento de custos e cobrança do Azure. Ele mapeia esses cenários para APIs que você pode usar. Em cada mapeamento de API do cenário, você pode encontrar um resumo das APIs e a funcionalidade que elas oferecem.

## <a name="common-scenarios"></a>Cenários comuns

Você pode APIs de gerenciamento de custos e de cobrança em vários cenários para responder a perguntas relacionadas a uso e a custo. Aqui está uma estrutura de tópicos dos cenários comuns:

- **Reconciliação de fatura**: A Microsoft me cobrou o valor correto?  Qual é minha fatura e posso calculá-la por conta própria?

- **Encargos cruzados**: Agora que já sabe quanto eu estou sendo cobrado pela, quem na minha organização precisa pagar?

- **Otimização de custo**: Eu sei o quanto fui cobrado. Como posso obter mais do dinheiro que estou gastando no Azure?

- **Acompanhamento de custos**: quero ver o quanto estou gastando e usando o Azure ao longo do tempo. Quais são as tendências? Como posso melhorar?

- **Gastos do Azure durante o mês**: Quanto é meu gasto do mês atual até a presente data? É necessário fazer alguma alteração ao meus gastos e/ou uso do Azure? Em que período do mês estou consumindo mais do Azure?

- **Alertas**: Como configurar alertas baseados em consumo de recursos ou monetários?

## <a name="scenario-to-api-mapping"></a>Mapeamento de cenário para API

|         API        | Reconciliação de fatura    | Encargos cruzados    | Otimização de custos    | Controle de custos    | Gastos do meio do mês    | Alertas    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Orçamentos                     |                           |                  |           X          |                  |                    |     X     |
| Cobranças do Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Tabela de Preços                 |             X             |         X        |           X          |         X        |          X         |           |
| Recomendações de reserva |                           |                  |           X          |                  |                    |           |
| Detalhes da reserva         |                           |                  |           X          |         X        |                    |           |
| Resumos da reserva       |                           |                  |           X          |         X        |                    |           |
| Detalhes de Uso               |             X             |         X        |           X          |         X        |          X         |     X     |
| Períodos de Cobrança             |             X             |         X        |           X          |         X        |                    |           |
| Faturas                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Uso sem classificação               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Os mapeamentos de cenário para API não incluem as APIs de consumo Enterprise. Sempre que possível, use as APIs de consumo geral para novos cenários de desenvolvimento.

## <a name="api-summaries"></a>Resumos de API

### <a name="consumption"></a>Consumo
Os clientes Diretos via Web e empresariais podem usar todas as seguintes APIs, exceto quando observado:

-   [API de Orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets) (*somente clientes Enterprise*): Crie os orçamentos de custo ou de uso de recursos, grupos de recursos ou medidores de cobrança. Quando você tiver criado os orçamentos, poderá configurar alertas para notificar quando você exceder limites de orçamento definidos. Você também pode configurar ações para ocorrerem quando você atingir os valores de orçamento.

-   [API de Encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Obter dados de uso e custo em todos os recursos do Azure Marketplace (ofertas de parceiro do Azure). Você pode usar esses dados para somar os custos de todos os recursos do Marketplace ou investigar os custos ou o uso de recursos específicos.

-   [API de Tabela de Preços](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*somente clientes Enterprise*): Obtenha preço personalizado para todos os medidores. As empresas podem usar esses dados em combinação com detalhes de uso e informações de uso do marketplace calcular custos usando dados de uso e do Marketplace. 

-   [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Obtenha recomendações para a compra de instâncias de VM reservada. As recomendações ajudam a analisar as economias de custo esperadas e os valores de compra. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API de Detalhes de Reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): veja informações sobre reservas de VM compradas anteriormente, como quanto consumo está reservado versus a quantidade usada. Você pode ver os dados em um nível de detalhe por VM. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API de Resumos de Reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): veja informações agregadas sobre reservas de VM que sua organização comprou, como quanto consumo está reservado versus a quantidade usada na agregação. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails): obtenha informações de cobrança e uso em todos os recursos do Azure da Microsoft. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por dia. Você pode usar as informações para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos.

-   [API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): obter taxas de medidor, se você for um cliente Direto da Web. Você então pode usar as informações retornadas com suas informações de uso de recursos para calcular manualmente a fatura esperada. 

-   [API de Uso sem Classificação](/previous-versions/azure/reference/mt219003(v=azure.100)): obtenha informações de uso bruto antes que o Azure faça qualquer medição/cobrança.

### <a name="billing"></a>Cobrança
-   [API dos Períodos de Cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): determine um período de cobrança para analisar, juntamente com as IDs de fatura para aquele período. Você pode usar as IDs de fatura com a API de Faturas.

-   [API de Faturas](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): obtenha a URL de download para uma fatura para um período de cobrança em formato PDF.

### <a name="enterprise-consumption"></a>Consumo de Enterprise
As seguintes APIs são apenas para Enterprise:

-   [API de Resumo de Saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Obtenha um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para comparação com o resumo de encargos calculados manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos.

-   [API de detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Obtenha informações sobre o uso do Azure (ofertas da Microsoft) para o mês atual, um período de cobrança específico ou um período de datas personalizado. As empresas podem usar esses dados para calcular faturas manualmente com base na taxa e no consumo. As empresas também podem usar informações de organização/departamento para custos de atributo entre organizações. Os dados fornecem uma exibição do uso e do custo específica a um recurso.

-   [API de Encargo de Loja do Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): obtenha informações sobre o uso do Azure (ofertas do parceiro) para o mês atual, um período de cobrança específico ou um período de datas personalizado. As empresas podem usar esses dados para calcular faturas manualmente com base na taxa e no consumo. As empresas também podem usar informações de organização/departamento para custos de atributo entre organizações. Essa API fornece uma exibição do uso e do custo específica a um recurso.

-   [API de folha de preço](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): obtenha a taxa aplicável de cada medidor para o registro especificado e o período de cobrança. Você pode usar essas informações de taxa em combinação com detalhes de uso e informações de uso do marketplace para calcular manualmente a fatura esperada.

-   [API dos Períodos de Cobrança](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): obter uma lista de períodos de cobrança. A API também fornece uma propriedade que aponta para a rota da API dos quatro conjuntos de dados da API Enterprise que pertencem ao período de cobrança: BalanceSummary, UsageDetails, Marketplace Charges e PriceSheet.

-   [API de Recomendações de Instância Reservada](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Examine 7 dias, 30 dias ou 60 dias de utilização de máquina virtual e obtenha recomendações de Compra Única e Compartilhada. Você pode usar essa API para analisar as economias de custo esperadas e os valores de compra recomendados. Para obter mais informações, confira [APIs para automação de reserva do Azure](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual é a diferença entre as APIs de relatórios corporativos e as APIs de consumo? Quando devo usar cada um?
Essas APIs têm um conjunto semelhante de funcionalidades e podem responder ao mesmo conjunto amplo de perguntas no espaço de faturamento e gerenciamento de custos. Mas eles se destinam a públicos diferentes: 

- APIs de Relatórios Enterprise estão disponíveis para clientes que assinaram um Contrato Enterprise com a Microsoft que concede a eles acesso negociados investimentos e preços personalizados. As APIs exigem uma chave que você pode obter do [Enterprise Portal](https://ea.azure.com). Para obter uma descrição dessas APIs, confira [visão geral de APIs de relatórios para clientes empresariais](billing-enterprise-api.md).

- APIs de Consumo estão disponíveis para todos os clientes, com algumas exceções. Para obter mais informações, consulte [Visão geral da API de consumo do Azure](billing-consumption-api-overview.md) e a [referência da API de consumo do Azure](https://docs.microsoft.com/rest/api/consumption/). Recomendamos as APIs fornecidas como a solução para os cenários de desenvolvimento mais recentes. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Qual é a diferença entre a API de detalhes de uso e a API de uso?
Essas APIs fornecem dados fundamentalmente diferentes:

- a [API Detalhes do uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) fornece informações de uso e custo do Azure por instância do medidor. Os dados fornecidos já passaram pelo sistema de medição de custos no Azure e tiveram seu custo aplicado junto com outras possíveis alterações:

   - Alterações para contabilizar o uso de compromissos monetários pré-pagos
   - Alterações para considerar discrepâncias de uso descobertas pelo Azure

- a [API de uso](/previous-versions/azure/reference/mt219003(v=azure.100)) fornece informações de uso do Azure bruto antes de transmiti-la por meio do sistema de medição de custo do Azure. Esses dados podem não ter nenhuma correlação com a quantidade de uso ou custos visto depois que o Azure cobra um sistema de medição.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual é a diferença entre a API de fatura e a API de detalhes de uso?
Essas APIs fornecem uma exibição diferente dos mesmos dados:

- A [API de Fatura](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) destina-se somente a clientes Diretos da Web. Ela fornece um rollup mensal de sua fatura com base nos encargos agregados para cada tipo de medidor. 

- A [API de Detalhes de Uso](https://docs.microsoft.com/rest/api/consumption/usagedetails) apresenta uma exibição detalhada dos registros de uso/custo para cada dia. Os clientes Enterprise e Diretos via Web podem usá-la.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>O que é a diferença entre a API de tabela de preços e a API RateCard?
Essas APIs fornecem conjuntos semelhantes de dados, mas têm diferentes públicos:

- a [API de folha de preço](https://docs.microsoft.com/rest/api/consumption/pricesheet) fornece preço personalizado negociado para um cliente Enterprise.

- A [API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) fornece o preço voltado para o público que se aplica a clientes Diretos via Web.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar as APIs do Azure para obter insights de maneira programática sobre seu uso do Azure, veja [Visão geral da API de Consumo do Azure](billing-consumption-api-overview.md) e [Visão geral da API de Cobrança do Azure](billing-usage-rate-card-overview.md).

- Para comparar sua fatura com o arquivo detalhado de uso diário e os relatórios de gerenciamento de custos no Portal do Azure, veja [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

- Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
