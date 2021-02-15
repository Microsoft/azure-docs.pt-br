---
title: Estornar custos da Reserva do Azure
description: Saiba como exibir os custos da Reserva do Azure para estorno.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: aba6ea467788c51d179ef9377243efb6035b6f98
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148354"
---
# <a name="charge-back-azure-reservation-costs"></a>Estornar custos da Reserva do Azure

Os leitores de cobrança com Contrato Enterprise e Contrato de Cliente da Microsoft podem exibir os dados de custos amortizados das reservas. Eles podem usar os dados de custo para estornar o valor monetário de uma assinatura, um grupo de recursos, um recurso ou uma marca para seus parceiros. Nos dados amortizados, o preço efetivo é o custo de reserva rateado por hora. O custo é o custo total de uso da reserva pelo recurso naquele dia.

Os usuários com uma assinatura individual podem obter os dados de custo amortizados em seu arquivo de uso. Quando um recurso recebe um desconto de reserva, a seção *AdditionalInfo* do arquivo de uso contém os detalhes de reserva. Para obter mais informações, confira [Baixar uso no portal do Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Obter dados de estorno de reserva para um estorno

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Gerenciamento de Custos + Cobrança**.
1. Em **Custo Real**, selecione a métrica **Custo Amortizado**.
1. Para ver quais recursos foram usados por uma reserva, aplique um filtro para **Reserva** e, em seguida, selecione as reservas.
1. Defina a **Granularidade** como **Mensal** ou **Diária**.
1. Defina o tipo de gráfico como **Tabela**.
1. Defina a opção **Agrupar por** como **Recurso**.

[![Exemplo mostrando os custos dos recursos de reserva que você pode usar para o estorno](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Assista a um vídeo mostrando como exibir os custos de utilização de reserva no portal do Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)