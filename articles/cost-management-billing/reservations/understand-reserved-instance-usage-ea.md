---
title: Noções básicas sobre o uso de reservas do Azure para o Contrato Enterprise e o Contrato de Cliente da Microsoft
description: Saiba como ler suas informações de uso para entender como uma reserva do Azure se aplica ao uso do Contrato Enterprise e do Contrato de Cliente da Microsoft.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: banders
ms.openlocfilehash: 0c69e9533130d6ca70c57422c7cdd5fc75adff72
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683710"
---
# <a name="get-enterprise-agreement-and-microsoft-customer-agreement-reservation-costs-and-usage"></a>Obter o uso e os custos de reservas do Contrato Enterprise e do Contrato de Cliente da Microsoft

Os dados aprimorados para uso e custos de reservas estão disponíveis para o uso do EA (Contrato Enterprise) e do MCA (Contrato de Cliente da Microsoft) no Gerenciamento de Custos. Este artigo ajuda você a:

- Obter dados de compra de reserva
- Saber qual assinatura, grupo de recursos ou recurso usou a reserva
- Estornar a utilização da reserva
- Calcular a economia de reserva
- Obter dados de subutilização de reserva
- Amortizar custos de reserva

Os encargos de Marketplace são consolidados em dados de uso. Você exibe encargos para uso interno, uso do Marketplace e compras de uma única fonte de dados.

## <a name="reservation-charges-in-azure-usage-data"></a>Encargos de reserva em dados de uso do Azure

Os dados são divididos em dois conjuntos de dados separados: _Custo Real_ e _Custo Amortizado_. Como esses dois conjuntos de dados diferem:

**Custo real** – fornece dados a serem reconciliados com sua fatura mensal. Esses dados têm custos de compra de reserva e detalhes do aplicativo de reserva. Com esses dados, você pode saber qual assinatura, grupo de recursos ou recurso recebeu o desconto de reserva em um dia específico. O EffectivePrice do uso que recebe o desconto de reserva é zero.

**Custo Amortizado** – esse conjunto de dados é semelhante ao conjunto de dados de Custo Real, exceto pelo fato de que o EffectivePrice do uso que obtém o desconto de reserva é o custo rateado da reserva (em vez de ser zero). Isso ajuda você a saber o valor monetário do consumo de reserva por uma assinatura, um grupo de recursos ou um recurso e pode ajudá-lo a estornar a utilização de reserva internamente. O conjunto de dados também tem horas de reserva não utilizadas. O conjunto de dados não tem registros de compra de reserva.

Comparação de dois conjuntos de dados:

| Dados | Conjunto de dados de Custo Real | Conjunto de dados de Custo Amortizado |
| --- | --- | --- |
| Compras de reserva | Disponível nesta exibição.<br><br>  Para obter esse filtro de dados em ChargeType = &quot;Purchase&quot;. <br><br> Confira ReservationID ou ReservationName para saber de qual reserva é o preço.  | Não aplicável a essa exibição. <br><br> Os custos de compra não são fornecidos em dados amortizados. |
| EffectivePrice | O valor é zero para uso que obtém o desconto de reserva. | O valor é o custo rateado por hora da reserva para uso que tem o desconto de reserva. |
| Reserva não utilizada (fornece o número de horas em que a reserva não foi utilizada em um dia e o valor monetário do desperdício) | Não aplicável nessa exibição. | Disponível nesta exibição.<br><br> Para obter esses dados, filtre em ChargeType = &quot;UnusedReservation&quot;.<br><br>  Confira ReservationID ou ReservationName para saber qual reserva foi subutilizada. Essa é a quantidade da reserva desperdiçada para o dia.  |
| UnitPrice(Preço do recurso em sua folha de preços) | Disponível | Disponível |

Outras informações disponíveis nos dados de uso do Azure foram alteradas:

- Informações sobre o produto e o medidor – o Azure não substitui o medidor consumido originalmente por ReservationId e ReservationName, como fazia anteriormente.
- ReservationId e ReservationName – são seus próprios campos nos dados. Anteriormente, estava disponível apenas em AdditionalInfo.
- ProductOrderId – a ID do pedido de reserva, adicionada como seu próprio campo.
- ProductOrderName – o nome do produto da reserva comprada.
- Prazo – 12 meses ou 36 meses.
- RINormalizationRatio – disponível em AdditionalInfo. Essa é a razão em que a reserva é aplicada ao registro de uso. Se a flexibilidade de tamanho da instância estiver habilitada para sua reserva, ela poderá se aplicar a outros tamanhos. O valor mostra a razão em que a reserva foi aplicada para o registro de uso.

[Alterar definição de campo](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obter dados de consumo e de uso de reserva do Azure usando API

Você pode obter os dados usando a API ou baixá-los do portal do Azure.

Chame a [API de Detalhes de Uso](/rest/api/consumption/usagedetails/list) para obter os novos dados. Para obter detalhes sobre a terminologia, confira [termos de uso](../understand/understand-usage.md).

Esta é uma chamada de exemplo à API de Detalhes de Uso:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Para obter mais informações sobre {enrollmentId} e {billingPeriodId}, confira o artigo da API [Detalhes de Uso – Lista](/rest/api/consumption/usagedetails/list).

As informações na tabela a seguir sobre métrica e filtro podem ajudar a resolver problemas comuns de reserva.

| **Tipo de dados de API** | Ação da chamada à API |
| --- | --- |
| **Todos os encargos (uso e compras)** | Substituir {metric} por ActualCost |
| **Uso que teve desconto de reserva** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por: properties/reservationId%20ne%20 |
| **Uso que não teve desconto de reserva** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por: properties/reservationId%20eq%20 |
| **Encargos amortizados (uso e compras)** | Substituir {metric} por AmortizedCost |
| **Relatório de reserva não utilizado** | Substituir {metric} por AmortizedCost<br><br>Substituir {filter} por: properties/ChargeType%20eq%20'UnusedReservation' |
| **Compras de reserva** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por: properties/ChargeType%20eq%20'Purchase'  |
| **Reembolsos** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Baixar o arquivo CSV de uso com novos dados

Se você é administrador do EA, baixe o arquivo CSV que contém novos dados de uso do portal do Azure. Esses dados não estão disponíveis no portal do EA (ea.azure.com); é necessário baixar o arquivo de uso no portal do Azure (portal.azure.com) para ver os novos dados.

No portal do Azure, navegue até [Gerenciamento de custos + Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de cobrança.
2. Clique em **Uso + encargos**.
3. Clique em **Download**.  
![Exemplo mostrando onde baixar o arquivo de dados de uso CSV no portal do Azure](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Em **Baixar Uso + Encargos**, em **Detalhes de Uso Versão 2**, selecione **Todos os encargos (uso e compras)** e clique em baixar. Repita para **Encargos amortizados (uso e compras)** .

Os arquivos CSV que você baixa contêm custos reais e amortizados.

## <a name="common-cost-and-usage-tasks"></a>Tarefas comuns de custo e uso

As seções a seguir são tarefas comuns que a maioria das pessoas usa para exibir seu custo de reserva e dados de uso.

### <a name="get-reservation-purchase-costs"></a>Obter custos de compra de reserva

Os custos de compra de reserva estão disponíveis nos dados de Custo Real. Filtro para _ChargeType = Purchase_. Confira ProductOrderID para determinar para qual pedido de reserva é a compra.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obter quantidade e custos de reserva subutilizados

Obtenha dados de Custo Amortizado e aplique o filtro _ChargeType_ _= UnusedReservation_. Você obtém a quantidade diária de reserva não utilizada e o custo. Você pode filtrar os dados para uma reserva ou pedido de reserva usando os campos _ReservationId_ e _ProductOrderId_, respectivamente. Se uma reserva foi 100% utilizada, o registro tem uma quantidade de 0.

### <a name="amortize-reservation-costs"></a>Amortizar custos de reserva

Obtenha os dados de Custo Amortizado e filtre para um pedido de reserva usando _ProductOrderID_ para obter os custos amortizados diários de uma reserva.

### <a name="chargeback-for-a-reservation"></a>Estorno de uma reserva

Você pode estornar o uso da reserva para outras organizações por assinatura, grupos de recursos ou marcas. Os dados de custo amortizado fornecem o valor monetário da utilização de uma reserva nos seguintes tipos de dados:

- Recursos (como uma VM)
- Resource group
- Marcas
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Obter a taxa combinada de estorno

Para determinar a taxa combinada, obtenha os dados de custo amortizado e agregue o custo total. Para VMs, é possível usar as informações MeterName e ServiceType dos dados JSON de AdditionalInfo. Divida o custo total pela quantidade usada para obter a taxa combinada.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Auditar o uso de reserva ideal para flexibilidade de tamanho da instância

Multiplique a quantidade pelo _RINormalizationRatio_, de AdditionalInfo. Os resultados indicam quantas horas de uso de reserva foram aplicadas ao registro de uso.

### <a name="determine-reservation-savings"></a>Determinar economia de reserva

Obtenha os Dados de custo amortizado e filtre-os para uma instância reservada. Em seguida:

1. Obtenha os custos pagos conforme o uso estimados. Multiplique o valor _UnitPrice_ pelos valores _Quantity_ para obter os custos pagos conforme o uso estimados, se o desconto de reserva não se aplicou ao uso.
2. Obter os custos de reserva. Some os valores _Cost_ para obter o valor monetário do que você pagou pela instância reservada. Isso inclui os custos usados e não utilizados da reserva.
3. Subtraia os custos de reserva dos custos pagos conforme o uso estimados para obter a economia estimada.

Tenha em mente que, se você tiver uma reserva subutilizada, a entrada _UnusedReservation_ para _ChargeType_ se tornará um fator a ser considerado. Quando você tiver uma reserva totalmente utilizada, você receberá o máximo de economia possível. Qualquer quantidade _UnusedReservation_ reduz a economia.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Compras de reserva e amortização na análise de custo

Os custos de reserva estão disponíveis na [análise de custo](https://aka.ms/costanalysis). Por padrão, a análise de custo mostra o **Custo real**, que é o modo como os custos serão mostrados em sua fatura. Para exibir as compras de reserva detalhadas e associadas aos recursos que usaram o benefício, alterne para **Custo amortizado**:

![Exemplo mostrando onde selecionar o custo amortizado na análise de custo](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Agrupar por tipo de preço para ver uma interrupção do uso, das compras e dos reembolsos; ou por reserva para um detalhamento da reserva e dos custos sob demanda. Lembre-se de que apenas os custos de reserva que você verá ao examinar o custo real são compras, mas os custos serão alocados aos recursos individuais que usaram o benefício ao examinar o custo amortizado. Você também verá um novo tipo de preço **UnusedReservation** ao examinar o custo amortizado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](../manage/understand-vm-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Custos de software do Windows não estão incluídos nas reservas](reserved-instance-windows-software-costs.md)