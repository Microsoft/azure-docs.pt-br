---
title: Obter o uso do Azure com APIs de Cobrança do Azure | Microsoft Docs
description: Aprenda sobre as APIs RateCard e de Uso de Cobrança do Azure, que são usadas para fornecer informações sobre o consumo de recursos e as tendências do Azure.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 4756c7abcb354e0b72c8a95c9d2df4bb3a14671a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443029"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Usar APIs de Cobrança do Azure para obter informações programaticamente sobre o uso do Azure
Use APIs de cobrança do Azure para efetuar pull de dados de uso e de recurso em suas ferramentas de análise de dados preferidas. As APIs RateCard e de Uso de Recursos do Azure e podem ajudá-lo a prever e gerenciar seus custos com precisão. As APIs são implementadas como um Provedor de Recursos e como parte da família de APIs expostas pelo Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API de Download de fatura do Azure (Visualização)
Após a [conclusão da aceitação](billing-manage-access.md#opt-in), baixe as faturas usando a versão de visualização da [API de Fatura](/rest/api/billing). Os recursos incluem:

* **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificarem quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.
* **Filtragem de Data** - Use o parâmetro `$filter` para obter todas as faturas em ordem cronológica inversa por data de término do período da fatura.

> [!NOTE]
> Esse recurso está na primeira versão de visualização e pode estar sujeito a alterações incompatíveis com versões anteriores. Atualmente, ele não está disponível para determinadas ofertas de assinatura (não há suporte para EA, CSP, AIO) e para o Azure na Alemanha.

## <a name="azure-resource-usage-api-preview"></a>API de uso de recursos do Azure (visualização)
Use as [API de Uso de Recursos](/previous-versions/azure/reference/mt219003(v=azure.100)) do Azure para obter seus dados de consumo estimado do Azure. A API inclui:

* **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificarem quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.
* **Agregações diárias ou por hora** - os chamadores podem especificar se eles desejam seus dados de uso do Azure em buckets por hora ou buckets diários. O padrão é diário.
* **Metadados da instância (inclui as marcas do recurso)** – obtenha detalhes no nível da instância, como o URI de recurso totalmente qualificado (/subscriptions/{subscription-id}/..), as informações do grupo de recursos e marcas do recurso. Esses metadados ajudam você a alocar de forma determinista e programática o uso por marcas, para casos de uso como cobrança cruzada.
* **Metadados de recurso** – detalhes de recurso como nome do medidor, categoria do medidor, subcategoria do medidor, unidade e região proporcionam ao chamador um melhor entendimento do que foi consumido. Também estamos trabalhando para alinhar a terminologia de metadados de recursos entre o portal do Azure, o CSV de uso do Azure, o CSV de cobrança EA e outras experiências públicas, para permitir que você correlacione dados entre as experiências.
* **Uso para diferentes tipos de oferta** – os dados de uso estão disponíveis para os tipos de oferta, assim como pré-pago, MSDN, investimento e crédito monetário e EA, exceto [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>API RateCard de Recursos do Azure (visualização)
Use a [API RateCard de Recursos do Azure](/previous-versions/azure/reference/mt219005(v=azure.100)) para obter a lista de recursos do Azure disponíveis, juntamente com as informações estimadas de preço para cada um deles. A API inclui:

* **Controle de Acesso baseado em Funções do Azure** – configure suas políticas de acesso no [Portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificarem quais usuários ou aplicativos podem ter acesso aos dados do RateCard. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura particular do Azure.
* **Suporte para pré-pago, MSDN, ofertas de investimento e crédito monetário (EA e [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) não têm suporte)** – Esta API fornece informações de taxa no nível da oferta do Azure.  O chamador dessa API deve passar as informações de oferta para obter taxas e detalhes do recurso. Já que as ofertas de EA têm taxas personalizadas por registro, não seremos capazes de fornecer as taxas de EA neste momento.

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários possíveis com a combinação das APIs de Uso e RateCard:

* **Gasto do Azure durante o mês** – use a combinação das APIs RateCard e de uso para obter melhores informações sobre o seu gasto com nuvem durante o mês. Você pode analisar os buckets horários e diários de uso e as estimativas de custo.
* **Configurar alertas** – use as APIs RateCard e de uso para obter o consumo de nuvem e encargos estimados e configurar alertas com base em recursos ou em valores financeiros.
* **Previsão de fatura** – obtenha seu consumo estimado e gasto de nuvem e aplicar algoritmos de aprendizado de máquina para prever qual seria sua fatura no final do ciclo de cobrança.
* **Análise de custo de pré-consumo** – usar a API RateCard para prever quanto sua fatura seria para seu uso esperado ao mover suas cargas de trabalho para o Azure. Se você tem cargas de trabalho existentes em outras nuvens ou nuvens privadas, você também pode mapear seu uso com as taxas do Azure para obter uma melhor estimativa do seu gasto com o Azure. Essa estimativa fornece a capacidade de dinamizar a oferta e comparar e contrastar os tipos diferentes de oferta além de pré-pago, tais como o compromisso monetário e o crédito monetário. A API também oferece a capacidade de ver as diferenças de custo por região e permite que você faça uma análise hipotética do custo para lhe ajudar a tomar decisões de implantação.
* **Hipóteses** -

  * Você pode determinar se é mais econômico executar suas cargas de trabalho em outra região ou em outra configuração do recurso do Azure. Os custos de recursos do Azure podem mudar com base na região do Azure que você está usando.
  * Você também pode determinar se outro tipo de oferta do Azure oferece uma melhor taxa em um recurso do Azure.


## <a name="next-steps"></a>Próximas etapas
* Confira as amostras de código no GitHub:
  * [Exemplo de código da API de Fatura](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Exemplo de código da API de Uso](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Exemplo de código da API RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
