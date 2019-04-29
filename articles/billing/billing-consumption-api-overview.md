---
title: Visão Geral da API de consumo do Azure | Microsoft Docs
description: Saiba como APIs de consumo do Azure oferecem acesso programático a dados de uso e de custo para os recursos do Azure.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 16e0bdfa0fc70d5239cb4127e61891a013bf54a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615892"
---
# <a name="azure-consumption-api-overview"></a>Visão geral da API de consumo do Azure 

As APIs de consumo do Azure lhe oferecem acesso programático a dados de uso e de custo para os recursos do Azure. Essas APIs atualmente dão suporte apenas a registros empresariais e assinaturas Web Direct (com algumas poucas exceções). As APIs são continuamente atualizadas para dar suporte a outros tipos de assinaturas do Azure.

As APIs de consumo do Azure fornecem acesso a:
- Clientes Enterprise e Web Direct 
    - Detalhes de uso 
    - Cobranças do Marketplace 
    - Recomendações de reserva 
    - Detalhes da reserva 
    - Resumos da reserva 
- Somente clientes Enterprise 
    - Tabela de preços 
    - Orçamentos 
    - Saldos 

## <a name="usage-details-api"></a>API de detalhes de uso

Use a API de detalhes de uso para obter dados de uso e cobrança do Azure para todos os recursos de primárias. As informações estão na forma de registros de detalhes de uso que atualmente são emitidos uma vez por metro por recurso por dia. As informações podem ser usadas para somar os custos de todos os recursos ou investigar os custos ou o uso de recursos específicos. 

A API inclui:

-   **Nível de consumo de dados do medidor** – consulte dados, incluindo o custo de uso, o medidor emitindo o encargo e a qual recurso do Azure o encargo se refere. Todos os registros de detalhes de uso são mapeados para um bucket diário.
-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Filtragem** – corte seu conjunto de resultados da API para um conjunto menor de registros de detalhes de uso usando os seguintes filtros:
    - Fim do uso/início do uso
    - Grupo de recursos
    - Nome do Recurso
-   **Agregação de dados** – use OData para aplicar expressões a detalhes de uso agregado por marcas ou propriedades de filtro
-   **Uso para tipos diferentes de oferta** – informações de detalhes de uso estão disponíveis atualmente para clientes Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API de encargos do Marketplace

Use a API de encargos do Marketplace para obter dados de uso e de cobrança em todos os recursos do Marketplace (ofertas de terceiros do Azure). Esses dados podem ser usados para somar os custos de todos os recursos do Marketplace ou investigar os custos ou o uso de recursos específicos. 

A API inclui:

-   **Nível de consumo de dados do medidor** – consulte dados, incluindo o custo de uso do Marketplace, o medidor emitindo o encargo e a qual recurso o encargo se refere. Todos os registros de detalhes de uso são mapeados para um bucket diário.
-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Filtragem** – corte seu conjunto de resultados da API para um conjunto menor de registros do Marketplace usando os seguintes filtros:
    - Início do uso/fim do uso
    - Grupo de recursos
    - Nome do Recurso
-   **Uso para tipos diferentes de oferta** – informações do Marketplace estão disponíveis atualmente para clientes Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API de saldos

Os clientes Enterprise podem usar a API de saldos para obter um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente. Você pode obter essas informações para o período de faturamento atual ou para qualquer período no passado. As empresas podem usar esses dados para realizar uma comparação com o resumo de encargos calculado manualmente. Essa API não fornece informações específicas do recurso nem uma exibição agregada de custos. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Somente para clientes Enterprise** Essa API só está disponível para clientes EA. 
    - Os clientes devem ter permissões de admin corporativo para chamar esta API 

Para obter mais informações, consulte a especificação técnica para a [API de saldos](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>API de orçamentos

Clientes Enterprise podem usar essa API para criar os orçamentos de custo ou de uso de recursos, grupos de recursos ou medidores de cobrança. Depois que essas informações tem sido determinadas, o alerta pode ser configurado para notificar quando os limites de orçamento definidos pelo usuário são excedidos. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Somente para clientes Enterprise** – essa API só está disponível para clientes EA.
-   **Notificações configuráveis** – especifique os usuários a serem notificados quando o orçamento for excedido.
-   **Orçamentos com base em uso ou custo** – crie seu orçamento com base no consumo ou custo conforme necessário para seu cenário.
-   **Filtragem** – filtre seu orçamento para um subconjunto menor de recursos usando os filtros configuráveis a seguir
    - Grupo de recursos
    - Nome do Recurso
    - Medidor
-   **Períodos de tempo configurável orçamento** – especifique com que frequência o orçamento deve ser redefinido e por quanto tempo o orçamento é válido.

Para obter mais informações, consulte a especificação técnica para a [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API de recomendações de reserva

Use esta API para obter recomendações para a compra de instâncias de VMs reservadas. As recomendações são projetadas para permitir que os clientes analisem a economia de custos esperada e os valores de compra. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Filtragem** – personalize os resultados de recomendação usando os seguintes filtros:
    - Escopo
    - Período de retrospectiva
-   **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão disponíveis atualmente para clientes Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API de detalhes de reserva

Use a API de detalhes de reserva para ver informações sobre reservas de VM adquiridas anteriormente, tais como a quantidade de consumo reservada versus a quantidade realmente sendo usada. Você pode ver os dados em um nível de detalhe por VM. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Filtragem** – corte seu conjunto de resultados da API para um conjunto menor de reservas usando os seguintes filtros:
    - Intervalo de datas
-   **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão disponíveis atualmente para clientes Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API de resumos de reserva

Use essa API para ver informações agregadas sobre reservas de VM adquiridas anteriormente, tais como a quantidade de consumo reservada versus a quantidade realmente sendo usada na agregação. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Filtragem** – personalize os resultados ao usar a agregação diária com o seguinte filtro:
    - Data de Uso
-   **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão disponíveis atualmente para clientes Enterprise e Web Direct.
-   **Agregações diárias ou mensais** – chamadores podem especificar se desejam que seus dados de resumo de reserva na agregação diária ou mensal.

Para obter mais informações, consulte a especificação técnica para a [API de resumo de reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>API de folha de preço
Um cliente Enterprise pode usar essa API para recuperar seus preços personalizados para todos os medidores. As empresas podem usar isso em combinação com detalhes de uso e informações de uso do Marketplace para executar cálculos de custo usando dados de uso e do Marketplace. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configure suas políticas de acesso no [portal do Azure](https://portal.azure.com), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure. 
-   **Somente para clientes Enterprise** – essa API só está disponível para clientes EA. Clientes Web Direct devem usar a API RateCard para obter os preços. 

Para obter mais informações, consulte a especificação técnica para a [API de tabela de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários que são possíveis por meio de APIs de consumo:

-   **Reconciliação de fatura** – a Microsoft me cobrou o valor correto?  Qual é minha fatura e posso calculá-la por conta própria?
-   **Cobranças entre setores** – agora que eu sei o quanto estou sendo cobrado, quem em minha organização precisa pagar?
-   **Otimização de custos** – eu sei quanto me foi cobrado... como posso obter mais do dinheiro que estou gastando no Azure?
-   **Controle de custos** – quero ver o quanto estou gastando com o Azure e usando dele ao longo do tempo. Quais são as tendências? Em que eu poderia melhorar?
-   **Gastos do Azure durante o mês** – quanto é o meu gasto mensal atual até a data de hoje? É necessário fazer quaisquer ajustes no meu gastos e/ou no uso do Azure? Em que período do mês estou consumindo mais do Azure?
-   **Configurar alertas** – gostaria de configurar alertas de consumo com base em recursos ou alertas com base monetária baseados em um orçamento.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre como usar APIs de cobrança do Azure para obter insights sobre o uso do Azure programaticamente, veja [Visão geral da API de cobrança do Azure](billing-usage-rate-card-overview.md).



