---
title: Entenda seu uso e cobranças detalhados | Microsoft Docs
description: Saiba como ler e entender seu uso e encargos detalhados
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 66b54c027cde6341b23aef2c10b43fa21bf357da
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383454"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Entenda os termos em seu arquivo de cobrança e uso do Azure

O arquivo detalhado de uso e encargos contém uso com classificação diária com base em taxas negociadas, compras (por exemplo, reservas, taxas do Marketplace) e reembolsos para o período especificado.
As tarifas não incluem créditos, impostos ou outros encargos ou descontos.
A tabela a seguir aborda quais encargos são incluídos para cada tipo de conta.

Tipo de conta | Uso do Azure | Uso do Marketplace | Jurídica | Reembolsos
--- | --- | --- | --- | ---
EA (Enterprise Agreement) | Sim | Sim | sim | Não
MCA (Contrato de Cliente da Microsoft) | Sim | Sim | Sim | Sim
Pagamento conforme o uso (PAYG) | Sim | Não | Não | Não

Para saber mais sobre pedidos do Marketplace (também conhecidos como serviços externos), confira [entender os encargos do serviço externo do Azure](billing-understand-your-azure-marketplace-charges.md).

Para obter instruções de download, consulte [como obter sua fatura de cobrança do Azure e dados de uso diário](billing-download-azure-invoice-daily-usage-date.md).
Você pode abrir o arquivo CSV de uso e encargos no Microsoft Excel ou em outro aplicativo de planilha.

## <a name="list-of-terms-and-descriptions"></a>Lista de termos e descrições

A tabela a seguir descreve os termos importantes usados na versão mais recente do arquivo de encargos e uso do Azure.
A lista abrange as contas pré-pagas (PAYG), Enterprise Agreement (EA) e Microsoft Customer Agreement (MCA).

Termo | Tipo de conta | Descrição
--- | --- | ---
Nome da Conta | EA, PAYG | Nome de exibição da conta de registro de EA ou conta de cobrança PAYG.
AccountOwnerId | EA, PAYG | Identificador exclusivo para a conta de registro EA ou conta de cobrança PAYG.
Informações Adicionais | Todas | Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual.
BillingAccountId | Todas | Identificador exclusivo da conta de cobrança raiz.
BillingAccountName | Todas | Nome da conta de cobrança.
BillingCurrency | Todas | Moeda associada à conta de cobrança.
BillingPeriod | EA, PAYG | O período de cobrança do encargo.
BillingPeriodEndDate | Todas | A data de término do período de cobrança.
BillingPeriodStartDate | Todas | A data de início do período de cobrança.
BillingProfileId | Todas | Identificador exclusivo do registro de EA, da assinatura PAYG, do perfil de cobrança MCA ou da conta consolidada AWS.
BillingProfileName | Todas | Nome do registro de EA, assinatura PAYG, perfil de cobrança MCA ou conta consolidada AWS.
ChargeType | Todas | Indica se a cobrança representa o uso (**uso**), uma compra (**compra**) ou um reembolso (**reembolso**).
Serviço Consumido | Todas | Nome do serviço ao qual a cobrança está associada.
Centro de Custo | EA, MCA | O centro de custo definido para a assinatura para controlar os custos (disponível somente em períodos de cobrança abertos para contas MCA).
Custo | EA, PAYG | Consulte CostInBillingCurrency.
CostInBillingCurrency | MCA | Custo da cobrança na moeda de cobrança antes dos créditos ou dos impostos.
CostInPricingCurrency | MCA | Custo da cobrança na moeda de preços antes dos créditos ou dos impostos.
Currency | EA, PAYG | Consulte BillingCurrency.
Date | Todas | A data de uso ou de compra do encargo.
EffectivePrice | Todas | Preço unitário combinado do período. Os preços misturados são a média de qualquer flutuação no preço unitário, como camadas graduadas, o que reduz o preço à medida que a quantidade aumenta com o passar do tempo.
ExchangeRateDate | MCA | Data em que a taxa de câmbio foi estabelecida.
ExchangeRatePricingToBilling | MCA | Taxa de câmbio usada para converter o custo na moeda de preço para a moeda de cobrança.
Frequência | Todas | Indica se espera-se que uma cobrança se repita. Os encargos podem ocorrer uma vez (**OneTime**), repetir em uma base mensal ou anual (recorrente) ou se basear no uso (**UsageBased**).
IncludedQuantity | PAYG | O valor do medidor que está incluído sem encargos no período de cobrança atual.
Id da Instância | PAGY | Consulte ResourceId.
Faturaid | PAYG, MCA | A ID de documento exclusiva listada no PDF da nota fiscal.
InvoiceSection | MCA | Consulte InvoiceSectionName.
InvoiceSectionId | EA, MCA | Identificador exclusivo para a seção do departamento de EA ou da fatura de MCA.
InvoiceSectionName | EA, MCA | Nome da seção do departamento de EA ou da fatura de MCA.
IsAzureCreditEligible | Todas | Indica se o encargo está qualificado para ser pago pelo uso de créditos do Azure (valores: True, false).
Location | MCA | Local do datacenter em que o recurso está em execução.
Categoria do Medidor | Todas | Nome da categoria de classificação para o medidor. Por exemplo, *serviços de nuvem* e *rede*.
Id do Medidor | Todas | O identificador exclusivo para o medidor.
MeterName | Todas | O nome do medidor.
MeterRegion | Todas | Nome do local do datacenter para serviços com o preço baseado no local. Consulte local.
Sub-categoria do Medidor | Todas | Nome da categoria de subclasse de medidor.
OfferId | Todas | Nome da oferta adquirida.
PartNumber | EA, PAYG | Identificador usado para obter preços específicos do medidor.
PlanName | EA, PAYG | Nome do plano do Marketplace.
PreviousInvoiceId | MCA | Referência a uma fatura original se este item de linha for um reembolso.
PricingCurrency | MCA | Moeda usada durante a classificação com base em preços negociados.
Produto | Todas | Nome do produto.
Id do Produto | MCA | Identificador exclusivo do produto.
ProductOrderId | Todas | Identificador exclusivo para a ordem do produto.
ProductOrderName | Todas | Nome exclusivo da ordem do produto.
PublisherName | Todas | Publicador para serviços do Marketplace.
PublisherType | Todas | Tipo de Publicador (valores: **Azure**, **AWS**, **Marketplace**).
Quantidade | Todas | O número de unidades adquiridas ou consumidas.
ReservationId | EA, MCA | Identificador exclusivo da instância de reserva comprada.
ReservationName | EA, MCA | Nome da instância de reserva adquirida.
GrupoRecuso | Todas | Nome do [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) no qual o recurso está.
resourceId | Todas | Identificador exclusivo do recurso de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) .
ResourceLocation | Todas | Local do datacenter em que o recurso está em execução. Consulte local.
ResourceName | EA, PAYG | Nome do recurso.
ResourceType | MCA | Tipo de instância de recurso.
ServiceFamily | MCA | Família de serviços à qual o serviço pertence.
Informações de Serviço 1 | Todas | Metadados específicos ao serviço.
Informações de Serviço 2 | Todas | Campo herdado com metadados específicos do serviço opcionais.
ServicePeriodEndDate | MCA | A data de término do período de classificação que definiu e bloqueou o preço do serviço consumido ou adquirido.
ServicePeriodStartDate | MCA | A data de início do período de classificação que definiu e bloqueou o preço do serviço consumido ou adquirido.
SubscriptionId | Todas | Identificador exclusivo para a assinatura do Azure.
Nome da Assinatura | Todas | Nome da assinatura do Azure.
Marcas | Todas | Marcas atribuídas ao recurso. Não inclui marcas de grupo de recursos. Pode ser usado para agrupar ou distribuir custos para o estorno interno. Para saber mais, confira [Organizar os recursos do Azure com marcas](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
UnitOfMeasure | Todas | A unidade de medida para cobrança do serviço. Por exemplo, os serviços de computação são cobrados por hora.
PreçoUnitário | EA, PAYG | O preço por unidade do encargo.

Observe que alguns campos podem diferir em maiúsculas e minúsculas entre os tipos de conta.
As versões mais antigas dos arquivos de uso pré-pago têm seções separadas para a instrução e o uso diário.

### <a name="list-of-terms-from-older-apis"></a>Lista de termos de APIs mais antigas
A tabela a seguir mapeia os termos usados em APIs mais antigas para os novos termos. Consulte a tabela acima para obter essas descrições.

Termo antigo | Novo termo
--- | ---
Quantidade Consumida | Quantidade
IncludedQuantity | N/D
Id da Instância | resourceId
Taxa | EffectivePrice
Unidade | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Garantir que as cobranças estejam corretas

Para saber mais sobre o uso e os encargos detalhados, leia sobre como entender sua fatura de contrato de [cliente](billing-mca-understand-your-bill.md) [pré-pago](./billing-understand-your-bill.md) ou Microsoft.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir e baixar sua fatura de Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar seu uso de Microsoft Azure e encargos](billing-download-azure-daily-usage.md)
