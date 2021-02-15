---
title: Exibir transações de reembolso e compra da Reserva do Azure
description: Saiba como exibir transações de reembolso e compra da Reserva do Azure.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b986aa2bfce203be85adbcde8e2966c167bf7ca1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151782"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Exibir transações de reembolso e compra de reserva

Há algumas maneiras diferentes de exibir transações de reembolso e compra de reserva. Você pode usar o portal do Azure, o Power BI e as APIs REST.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Exibir transações de reserva no portal do Azure

Um registro Enterprise ou administrador de cobrança do Contrato de Cliente da Microsoft pode exibir transações de reserva em Gerenciamento de Custos e Cobrança.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. Selecione **Transações de reserva**.
1. Para filtrar os resultados, selecione **Timespan**, **Tipo** ou **Descrição**.
1. Escolha **Aplicar**.

[![Captura de tela mostrando transações de reserva no portal do Azure](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Exibir transações de reserva no Power BI

Um registro Enterprise ou administrador de cobrança do Contrato de Cliente da Microsoft pode exibir transações de reserva com o aplicativo do Power BI de Gerenciamento de Custos.

1. Obtenha o [Aplicativo do Power BI de Gerenciamento de Custos](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Navegue até o relatório de Compras de RI.

[![Exemplo mostrando transações de reserva](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Para saber mais, confira [Aplicativo do Power BI de Gerenciamento de Custos do Azure para Contratos Enterprise](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Usar APIs para obter transações de reserva

Usuários do EA (Contrato Enterprise) e do Contrato de Cliente da Microsoft podem obter os dados de transações de reserva usando a [API de Transações de Reserva – Listar](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Para aprender a gerenciar uma reserva, confira [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerenciar Reservas no Azure](manage-reserved-vm-instance.md)