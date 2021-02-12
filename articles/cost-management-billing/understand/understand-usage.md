---
title: Noções básicas sobre o uso detalhado e os custos
description: Saiba como ler e entender o uso detalhado e o arquivo de encargos. Exiba uma lista de termos e descrições usados no arquivo.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: banders
ms.openlocfilehash: 07e3cfdce238d5fc4e2737a49dde6fd624de8506
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882493"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Noções básicas sobre os termos em seu arquivo de uso e encargos do Azure

O arquivo de uso detalhado e encargos contém uso classificado diariamente com base em tarifas negociadas, compras (por exemplo, reservas, tarifas do Marketplace) e reembolsos para o período especificado.
As tarifas não incluem créditos, impostos ou outros encargos ou descontos.
A tabela a seguir aborda quais encargos são incluídos para cada tipo de conta.

Tipo de conta | Uso do Azure | Uso do Marketplace | Compras | Reembolsos
--- | --- | --- | --- | ---
EA (Enterprise Agreement) | Sim | Sim | Sim | Não
MCA (Contrato de Cliente da Microsoft) | Sim | Sim | Sim | Sim
Pagamento conforme o uso (PAYG) | Sim | Sim | Não | Não

Para saber mais sobre os pedidos do Marketplace (também conhecidos como serviços externos), confira [Noções básicas sobre os encargos do serviço externo do Azure](understand-azure-marketplace-charges.md).

Para obter instruções sobre download, confira [Como obter sua fatura de cobrança e os dados de uso diário do Azure](../manage/download-azure-invoice-daily-usage-date.md).
É possível abrir seu arquivo CSV de uso e encargos no Microsoft Excel ou em outro aplicativo de planilha.

## <a name="list-of-terms-and-descriptions"></a>Lista de termos e descrições

A tabela a seguir descreve os termos importantes usados na versão mais recente do arquivo de uso e encargos do Azure.
A lista abrange contas PAYG (pagas conforme o uso), EA (Contrato Enterprise) e MCA (Contrato de Cliente da Microsoft).

Termo | Tipo de conta | Descrição
--- | --- | ---
AccountName | EA, PAYG | Nome de exibição da conta de registro de EA ou da conta de cobrança PAYG.
AccountOwnerId<sup>1</sup> | EA, PAYG | Identificador exclusivo da conta de registro de EA ou da conta de cobrança PAYG.
AdditionalInfo | Todos | Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual.
BillingAccountId<sup>1</sup> | Todos | Identificador exclusivo da conta de cobrança raiz.
BillingAccountName | Todos | Nome da conta de cobrança.
BillingCurrency | Todos | Moeda associada à conta de cobrança.
BillingPeriod | EA, PAYG | O período de cobrança do encargo.
BillingPeriodEndDate | Todos | A data de término do período de cobrança.
BillingPeriodStartDate | Todos | A data de início do período de cobrança.
BillingProfileId<sup>1</sup> | Todos | Identificador exclusivo do registro de EA, assinatura PAYG, perfil de cobrança do MCA ou conta consolidada da AWS.
BillingProfileName | Todos | Nome do registro de EA, assinatura PAYG, perfil de cobrança do MCA ou conta consolidada da AWS.
ChargeType | Todos | Indica se o encargo representa o uso (**Usage**), a compra (**Purchase**) ou um reembolso (**Refund**).
ConsumedService | Todos | Nome do serviço ao qual a cobrança está associada.
CostCenter<sup>1</sup> | EA, MCA | O centro de custo definido para a assinatura para acompanhar custos (disponível apenas em períodos de cobrança abertos para contas MCA).
Custo | EA, PAYG | Confira CostInBillingCurrency.
CostInBillingCurrency | MCA | Custo do encargo na moeda da cobrança antes de créditos ou impostos.
CostInPricingCurrency | MCA | Custo do encargo na moeda do preço antes de créditos ou impostos.
Moeda | EA, PAYG | Confira BillingCurrency.
Date<sup>1</sup> | Todos | A data de uso ou de compra do encargo.
EffectivePrice | Todos | Preço unitário combinado do período. Os preços combinados estabelecem a média de flutuações no preço unitário, como camadas graduadas, que reduz o preço conforme a quantidade aumenta com o tempo.
ExchangeRateDate | MCA | Data em que a taxa de câmbio foi estabelecida.
ExchangeRatePricingToBilling | MCA | Taxa de câmbio usada para converter o custo na moeda do preço na moeda de cobrança.
Frequência | Todos | Indica se espera-se que um encargo se repita. Os encargos podem ocorrer uma vez (**OneTime**), repetir-se por mês ou ano (**Recurring**) ou ser baseados no uso (**UsageBased**).
InvoiceId | PAYG, MCA | A ID de documento exclusiva listada no PDF da fatura.
InvoiceSection | MCA | Confira InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Identificador exclusivo do departamento de EA ou da seção da fatura de MCA.
InvoiceSectionName | EA, MCA | Nome do departamento de EA ou da seção da fatura de MCA.
IsAzureCreditEligible | Todos | Indica se o encargo está qualificado a ser pago pelo uso de créditos Azure (valores: True, False).
Location | MCA | Localização do datacenter em que o recurso está sendo executado.
MeterCategory | Todos | Nome da categoria de classificação do medidor. Por exemplo, *Serviços de Nuvem* e *Rede* etc.
MeterId<sup>1</sup> | Todos | O identificador exclusivo do medidor.
MeterName | Todos | O nome do medidor.
MeterRegion | Todos | Nome da localização do datacenter para serviços com o preço baseado na localização. Confira Localização.
MeterSubCategory | Todos | Nome da categoria de subclassificação do medidor.
OfferId<sup>1</sup> | Todos | Nome da oferta comprada.
PayGPrice | Todos | Preço de varejo do recurso.
PartNumber<sup>1</sup> | EA, PAYG | Identificador usado para obter preços de medidor específicos.
PlanName | EA, PAYG | Nome do plano do Marketplace.
PreviousInvoiceId | MCA | Referência a uma fatura original se esse item de linha é um reembolso.
PricingCurrency | MCA | Moeda usada ao classificar com base em preços negociados.
PricingModel | Todos | Identificador que indica como o medidor é cobrado. (Valores: Sob demanda, Reserva, Spot)
Produto | Todos | O nome do produto.
ProductId<sup>1</sup> | MCA | Identificador exclusivo do produto.
ProductOrderId | Todos | Identificador exclusivo do pedido do produto.
ProductOrderName | Todos | Nome exclusivo do pedido do produto.
PublisherName | Todos | Editor de serviços do Marketplace.
PublisherType | Todos | Tipo de editor (valores: **Azure**, **AWS**, **Marketplace**).
Quantidade | Todos | O número de unidades compradas ou consumidas.
ReservationId | EA, MCA | Identificador exclusivo da instância de reserva comprada.
ReservationName | EA, MCA | Nome da instância de reserva comprada.
ResourceGroup | Todos | Nome do [grupo de recursos](../../azure-resource-manager/management/overview.md) no qual o recurso está. Nem todos os encargos são provenientes de recursos implantados em grupos de recursos. Os encargos que não têm um grupo de recursos serão mostrados como nulo/vazio, **Outros** ou **Não aplicável**.
ResourceId<sup>1</sup> | Todos | Identificador exclusivo do recurso [Azure Resource Manager](/rest/api/resources/resources).
ResourceLocation | Todos | Localização do datacenter em que o recurso está sendo executado. Confira Localização.
ResourceName | EA, PAYG | Nome do recurso. Nem todos os encargos são provenientes de recursos implantados. Os encargos que não têm um tipo de recurso serão mostrados como nulo/vazio, **Outros** ou **Não aplicável**.
ResourceType | MCA | Tipo de instância do recurso. Nem todos os encargos são provenientes de recursos implantados. Os encargos que não têm um tipo de recurso serão mostrados como nulo/vazio, **Outros** ou **Não aplicável**.
ServiceFamily | MCA | Família de serviços ao qual o serviço pertence.
ServiceInfo1 | Todos | Metadados específicos ao serviço.
ServiceInfo2 | Todos | Campo herdado com metadados opcionais específicos do serviço.
ServicePeriodEndDate | MCA | A data de término do período de classificação que definiu e bloqueou o preço para o serviço consumido ou comprado.
ServicePeriodStartDate | MCA | A data de início do período de classificação que definiu e bloqueou o preço para o serviço consumido ou comprado.
SubscriptionId<sup>1</sup> | Todos | Identificador exclusivo da assinatura do Azure.
SubscriptionName | Todos | Nome da assinatura do Azure.
Tags<sup>1</sup> | Todos | Marcas atribuídas ao recurso. Não inclui marcas do grupo de recursos. Podem ser usadas para agrupar ou distribuir custos para estorno interno. Para saber mais, confira [Organizar os recursos do Azure com marcas](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Termo | Todos | Exibe o prazo de validade da oferta. Por exemplo:  No caso de instâncias reservadas, exibe os 12 meses como o Prazo. Para compras avulsas ou recorrentes, o prazo é 1 mês (SaaS, suporte do Marketplace). Isso não é aplicável para consumo do Azure.
UnitOfMeasure | Todos | A unidade de medida para cobrança do serviço. Por exemplo, os serviços de computação são cobrados por hora.
UnitPrice | EA, PAYG | O preço unitário do encargo.

_<sup>**1**</sup> Campos usados para criar uma ID exclusiva para um registro de custo único._

Observe que alguns campos podem ser diferentes na grafia de maiúsculas e minúsculas e no espaçamento entre os tipos de conta.
Versões mais antigas de arquivos de uso pago conforme o uso têm seções separadas para a instrução e o uso diário.

### <a name="list-of-terms-from-older-apis"></a>Lista de termos de APIs mais antigas
A tabela a seguir mapeia termos usados em APIs mais antigas para os novos termos. Confira a tabela acima para obter essas descrições.

Termo antigo | Termo novo
--- | ---
ConsumedQuantity | Quantidade
IncludedQuantity | N/D
InstanceId | ResourceId
Tarifa | EffectivePrice
Unidade | UnitOfMeasure
UsageDate | Data
UsageEnd | Data
UsageStart | Data

## <a name="ensure-charges-are-correct"></a>Verifique se os encargos estão corretos

Para saber mais sobre o uso detalhado e os encargos, leia sobre como entender sua fatura [paga conforme o uso](review-individual-bill.md) ou [do Contrato de Cliente da Microsoft](review-customer-agreement-bill.md).

## <a name="unexpected-usage-or-charges"></a>Uso ou encargos inesperados

Se você tiver uso ou cobranças que não reconhece, há várias ações que você pode realizar para ajudar a entender o motivo:

- Examinar a fatura que tem encargos para o recurso
- Examinar seus encargos faturados na Análise de custo
- Encontrar pessoas responsáveis pelo recurso e interagir com elas
- Analisar os logs de auditoria
- Analisar as permissões do usuário para o escopo pai do recurso
- Criar uma [solicitação de suporte do Azure](https://go.microsoft.com/fwlink/?linkid=2083458) para ajudar a identificar os encargos

Para obter mais informações, confira [Analisar encargos inesperados](analyze-unexpected-charges.md).

Observe que o Azure não registra a maioria das ações do usuário. Em vez disso, a Microsoft registra o uso de recursos para cobrança. Se você observar um pico de uso no passado e não tiver habilitado o registro em log, a Microsoft não poderá identificar a causa. Habilite o registro em log do serviço para o qual você deseja exibir o aumento de uso para que a equipe técnica apropriada possa ajudar você com o problema.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- [Exibir e baixar a fatura do Microsoft Azure](download-azure-invoice.md)
- [Exibir e baixar o uso e os encargos do Microsoft Azure](download-azure-daily-usage.md)