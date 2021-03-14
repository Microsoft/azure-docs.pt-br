---
title: Estornar custos da Reserva do Azure
description: Saiba como exibir os custos da Reserva do Azure para estorno.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/10/2021
ms.author: banders
ms.openlocfilehash: 4fb15a7e677d566454d5d487c1cf69767d7f3a30
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368737"
---
# <a name="charge-back-azure-reservation-costs"></a>Estornar custos da Reserva do Azure

Os leitores de cobrança com Contrato Enterprise e Contrato de Cliente da Microsoft podem exibir os dados de custos amortizados das reservas. Eles podem usar os dados de custo para estornar o valor monetário de uma assinatura, um grupo de recursos, um recurso ou uma marca para seus parceiros. Nos dados amortizados, o preço efetivo é o custo de reserva rateado por hora. O custo é o custo total de uso da reserva pelo recurso naquele dia.

Os usuários com uma assinatura individual podem obter os dados de custo amortizados em seu arquivo de uso. Quando um recurso recebe um desconto de reserva, a seção *AdditionalInfo* do arquivo de uso contém os detalhes de reserva. Para obter mais informações, confira [Baixar uso no portal do Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Confira os dados de uso de reserva para mostrar novamente e estornar

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até **Gerenciamento de Custos e Cobrança** 
3. Selecione **Análise de custo** no painel de navegação esquerdo 
4. Em **Custo Real**, selecione a métrica **Custo Amortizado**.
5. Para ver quais recursos foram usados por uma reserva, aplique um filtro para **Reserva** e, em seguida, selecione as reservas.
6. Defina a **Granularidade** como **Mensal** ou **Diária**.
7. Defina o tipo de gráfico como **Tabela**.
8. Defina a opção **Agrupar por** como **Recurso**.

[![Exemplo mostrando os custos dos recursos de reserva que você pode usar para o estorno](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Assista a um vídeo mostrando como exibir os custos de utilização de reserva no portal do Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Obter os dados para mostrar novamente e estornar
1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até **Gerenciamento de Custos e Cobrança** 
3. Selecione **Exportar** no painel de navegação esquerdo 
4. Clique no botão **Adicionar**
5. Selecione Custo amortizado como o botão de métrica e configure sua exportação

O EffectivePrice do uso que obtém o desconto de reserva é o custo rateado da reserva (em vez de ser zero). Isso ajuda você a saber o valor monetário do consumo de reserva por uma assinatura, um grupo de recursos ou um recurso e pode ajudá-lo a estornar a utilização de reserva internamente. O conjunto de dados também tem horas de reserva não utilizadas. 

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
4. Em **Detalhes de Uso**, selecione **Dados de uso amortizados**.

Os arquivos CSV que você baixa contêm custos reais e amortizados.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre os dados de uso das Reservas do Azure, confira os seguintes artigos:
  - [Uso e custos de reservas do Contrato Enterprise e do Contrato de Cliente da Microsoft](understand-reserved-instance-usage-ea.md)
 
