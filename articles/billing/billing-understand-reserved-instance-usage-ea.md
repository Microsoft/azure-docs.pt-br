---
title: Entender o uso de reservas do Azure para Enterprise Agreements
description: Aprenda a ler o seu uso para entender como a reserva do Azure para o seu registro Enterprise é aplicada.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b0c7c38ebabfdd142394152f735d40320a98dced
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798152"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Obter o uso e custos de reserva do Enterprise Agreement

Dados de uso e custos de reserva estão disponíveis para clientes do contrato Enterprise no portal do Azure e APIs REST. Este artigo ajuda você a:

- Obter dados de compra de reserva
- Obter dados de subutilização de reserva
- Amortizar os custos de reserva
- Estorno para utilização de reserva
- Calcular a economia de reserva

Encargos do Marketplace são consolidados em dados de uso. Você exibir encargos para primeiro uso de terceiros, o uso do marketplace e compras de uma única fonte de dados.

## <a name="reservation-charges-in-azure-usage-data"></a>Encargos de reserva em dados de uso do Azure

Dados são divididos em dois conjuntos de dados separados: _Custo real_ e _custo amortizado_. Diferem entre esses dois conjuntos de dados:

**Custo real** -fornece dados para reconciliar com sua fatura mensal. Esses dados tem a reserva de custos de aquisição. Ele tem zero EffectivePrice para o uso que recebeu o desconto de reserva.

**Custo amortizado** -EffectiveCost que obtém o desconto de reserva do recurso é o custo proporcional de instância reservada. O conjunto de dados também tem custos de reserva não utilizados. A soma do custo de reserva e reserva não utilizada fornece o custo amortizado diário da reserva.

Comparação de dois conjuntos de dados:

| Dados | Conjunto de dados de custo real | Conjunto de dados de custo amortizado |
| --- | --- | --- |
| Compras de reserva | Disponíveis nessa exibição.<br><br>  Para obter esse filtro de dados em ChargeType = &quot;compra&quot;. <br><br> Consulte ReservationID ou ReservationName saber quais reserva o encargo é cobrado.  | Não aplicável a este modo de exibição. <br><br> Os custos de compra não são fornecidos nos dados amortizados. |
| effectivePrice | O valor é zero para uso que obtém um desconto de reserva. | O valor é o custo é Rateado por hora da reserva para uso com o desconto de reserva. |
| Reserva não utilizada (fornece o número de horas que a reserva não foi usada em um dia e o valor monetário do desperdício) | Não é aplicável nesta exibição. | Disponíveis nessa exibição.<br><br> Para obter esses dados, filtrar ChargeType = &quot;UnusedReservation&quot;.<br><br>  Consulte ReservationID ou ReservationName saber quais reserva foi subutilizada. Essa é a quantidade de reserva foi desperdiçado em para o dia.  |
| UnitPrice (preço do recurso da sua folha de preço) | Disponível | Disponível |

Outras informações disponíveis nos dados de uso do Azure mudou:

- Produto e informações de medidor - Azure não substitui o medidor consumido originalmente com o ReservationId e ReservationName, como anteriormente.
- ReservationId e ReservationName - são seus próprios campos nos dados. Anteriormente, costumava ser disponível somente para AdditionalInfo.
- ProductOrderId - a identificação do pedido de reserva, adicionado como seu próprio campo.
- ProductOrderName - o nome do produto a reserva adquirida.
- Termo - 12 meses ou 36 meses.
- RINormalizationRatio - disponível em AdditionalInfo. Isso é a taxa em que a reserva é aplicada para o registro de uso. Se a flexibilidade de tamanho de instância está habilitada na sua reserva, em seguida, ele pode aplicar a outros tamanhos. O valor mostra a proporção que a reserva foi aplicada para o registro de uso.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obter dados de uso de reserva e de consumo do Azure usando a API

Você pode obter os dados usando a API ou baixá-lo do portal do Azure.

Você chama o [API detalhes de uso](/rest/api/consumption/usagedetails/list) com a versão de API &quot;2019-04-01-preview&quot; para obter os novos dados. Para obter detalhes sobre a terminologia, consulte [termos de uso](billing-understand-your-usage.md). O chamador deve ser um administrador corporativo para o contrato corporativo usando o [portal de EA](https://ea.azure.com). Os administradores de empresa somente leitura também pode obter os dados.

Os dados não estão disponíveis no [APIs de relatórios para clientes Enterprise – detalhes de uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Aqui está um exemplo de chamada para a API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Para obter mais informações sobre {enrollmentId} e {billingPeriodId}, consulte o [detalhes de uso – lista](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) artigo de API.

Informações na tabela a seguir sobre a métrica e o filtro podem ajudar a resolver problemas comuns de reserva.

| **Tipo de dados de API** | Ação de chamada de API |
| --- | --- |
| **Todos os encargos (uso e compras)** | Substitua {metric} ActualCost |
| **Uso que obteve o desconto de reserva** | Substitua {metric} ActualCost<br><br>Substitua {filter} por: properties/reservationId%20ne%20 |
| **Uso que não obtiveram o desconto de reserva** | Substitua {metric} ActualCost<br><br>Substitua {filter} por: properties/reservationId%20eq%20 |
| **Encargos amortizados (uso e compras)** | Substitua {metric} AmortizedCost |
| **Relatório de reserva não utilizados** | Substitua {metric} AmortizedCost<br><br>Substitua {filter} por: properties/ChargeType%20eq%20'UnusedReservation' |
| **Compras de reserva** | Substitua {metric} ActualCost<br><br>Substitua {filter} por: properties/ChargeType%20eq%20'Purchase'  |
| **Reembolsos** | Substitua {metric} ActualCost<br><br>Substitua {filter} por: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Baixe o arquivo CSV de uso com novos dados

Se você for um administrador EA, você pode baixar o arquivo CSV que contém os novos dados de uso do portal do Azure. Esses dados não estão disponíveis a partir de [portal de EA](https://ea.azure.com).

No portal do Azure, navegue até [gerenciamento de custos + cobrança](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de cobrança.
2. Clique em **uso + encargos**.
3. Clique em **Baixar**.  
![Exemplo mostrando onde baixar o arquivo de dados de uso do CSV no portal do Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Na **baixar uso + encargos** , em **detalhes de uso da versão 2** , selecione **todos os encargos (uso e compras)** e, em seguida, clique em baixar. Repita a operação em **amortizado encargos (uso e compras)** .

Os arquivos CSV que você baixar contêm os custos reais e os custos amortizados.

## <a name="common-cost-and-usage-tasks"></a>Tarefas comuns de uso e custo

As seções a seguir são tarefas comuns que a maioria das pessoas use para exibir seus dados de uso e custo de reserva.

### <a name="get-reservation-purchase-costs"></a>Obter custos de compra de reserva

Os custos de compra de reserva estão disponíveis nos dados de custo real. Filtrar por _ChargeType = compra_. Consulte ProductOrderID para determinar qual pedido de reserva a compra destina-se.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obter a quantidade de reserva do subutilizada e os custos

Obter dados de custo amortizado e filtrar _ChargeType_ _= UnusedReservation_. Você obtém a quantidade diária de reserva não utilizados e o custo. Você pode filtrar os dados para uma reserva ou ordem de reserva usando _ReservationId_ e _ProductOrderId_ campos, respectivamente. Se uma reserva foi 100% de utilização, o registro tem uma quantidade igual a 0.

### <a name="amortize-reservation-costs"></a>Amortizar os custos de reserva

Obter dados de custo amortizado e filtro para um pedido de reserva usando _ProductOrderID_ para obter custos amortizados diários de uma reserva.

### <a name="chargeback-for-a-reservation"></a>Estorno de uma reserva

É possível usar de reserva de estorno para outras organizações por marcas, grupos de recursos ou assinatura. Dados de custo amortizado fornecem valor monetário de utilização de uma reserva atentamente os tipos de dados a seguir:

- Recursos (como uma VM)
- Grupo de recursos
- Marcas
- Assinatura

### <a name="get-the-blended-rate-for-chargeback"></a>Obter a taxa combinada de estorno

Para determinar a taxa combinada, obter os dados de custos amortizados e o custo total de agregação. Para VMs, você pode usar informações MeterName ou ServiceType dados AdditionalInfo JSON. Divida o custo pela quantidade usada para obter a taxa combinada de total.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Reserva ideal de auditoria use, por exemplo, flexibilidade de tamanho

A quantidade de múltiplos com o _RINormalizationRatio_, de AdditionalInfo. Os resultados indicam quantas horas de uso de reserva foi aplicada para o registro de uso.

### <a name="determine-reservation-savings"></a>Determinar as economias de reserva

Obter os dados de custos Amortized e filtrar os dados para uma instância reservada. Em seguida:

1. Obter custos estimados de pago conforme o uso. Multiplicar os _UnitPrice_ de valor com _quantidade_ valores para obter a estimados custos pagos conforme o uso, se o desconto de reserva não se aplicam ao uso.
2. Obtenha os custos de reserva. Soma de _custo_ valores para obter o valor monetário de você paga a instância reservada. Ele inclui os custos utilizados e não utilizados de reserva.
3. Subtrai os custos de reserva de custos estimados de pago conforme o uso para obter a economia estimada.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>As compras de reserva e amortização na análise de custo

Os custos de reserva estão disponíveis no [análise de custo](https://aka.ms/costanalysis). Por padrão, mostra de análise de custo **custo real**, que é como os custos serão exibidos na sua fatura. Para exibir as compras dividido e associados com os recursos que é usado o benefício de reserva, mude **custo amortizado**:

![Exemplo mostrando onde selecionar custo amortizado na análise de custo](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Agrupar por tipo de encargo para verificar um desdobramento de uso, compras e reembolsos; ou por reserva para uma divisão de custos de reserva e sob demanda. Lembre-se os custos de reserva única, que você verá ao examinar os custos reais são compras, mas os custos serão alocados para os recursos individuais que usado a vantagem ao examinar os custos amortizados. Você também verá uma nova **UnusedReservation** cobrar tipo quando observando o custo amortizado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
