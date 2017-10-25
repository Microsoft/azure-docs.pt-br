---
title: "Entendendo o relatório de pagamento do Azure Marketplace | Microsoft Docs"
description: "Aprenda a examinar e ingerir o relatório de pagamento do Azure Marketplace."
services: marketplace-publishing
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: 3e99aefe-abeb-414c-8689-15352d25aefd
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: v-jeana; hascipio; v-dabosl
ms.openlocfilehash: 5a89e9ba4376d0c4f49feb3783692e28a28902a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-marketplace-payout-reports"></a>Entenda os relatórios de pagamento do Azure Marketplace
## <a name="access-and-view-your-payout-reports"></a>Acessar e exibir seus relatórios de pagamento
Enquanto fazemos a transição para o Centro de Desenvolvimento, é possível que alguns dos seus relatórios de pagamento estejam disponíveis no Centro de Desenvolvimento em https://dev.windows.com/pt-br; já outros ainda podem ser acessados no Portal de Publicação em https://publish.windowsazure.com.

O relatório de pagamento estará disponível no **Centro de Desenvolvimento** para qualquer oferta do Marketplace que estiver associada a pagamentos modernos. Atualmente, isso inclui:

* VMs
* Ofertas de B + C
* Dados e desenvolvimento de serviços oferecidos em EA

O relatório de pagamento ainda estará no **Portal de publicação** para:

* Dados e desenvolvimento de serviços oferecidos no Web Direct (que ainda usa o sistema de pagamento herdado).

Os relatórios estarão disponíveis 45 dias após o encerramento do trimestre e serão calculados após quaisquer reembolsos.

### <a name="access-payout-reports-in-dev-center"></a>Acessar relatórios de pagamento no Centro de Desenvolvimento
1. Navegue até o Centro de Desenvolvimento em https://dev.windows.com/pt-br.
2. Clique em **Painel**.

    ![LandingPageDashboardHighlight][1]
3. Clique em **Resumo do Pagamento**.

    ![DashboardPayoutSummary][2]

## <a name="view-your-payout-reports-in-dev-center"></a>Exibir relatórios de pagamento no Centro de Desenvolvimento
O relatório de pagamento para cada trimestre registra todas as transações que ocorreram dentro desse trimestre.

* O Valor reservado indica os pagamentos estão se acumulando fora do ciclo do próximo pagamento (por exemplo, esse valor será movido para o pagamento do mês seguinte).  Esse valor normalmente será US$ 0 (a menos que um cliente pague com bastante antecedência).
* Clique nos links **Exibir detalhes** de Pagamentos futuros ou do Pagamento mais recente para ver uma observação sobre os pagamentos.
* Clique em **Demonstrativos de Pagamento** para exibir os detalhes em receita por produto/aplicativo.
* Clique no link **Exibir** para ver os demonstrativos individuais.

    ![PayoutSummaryUpcomingMostRecentLinksStatement][3]
* Use o filtro **Detalhamento da Receita** na parte inferior do demonstrativo individual para exibir vários aplicativos/produtos, se existirem.

    ![PayoutSummaryPaymentStatementsFilterControl][4]

## <a name="view-your-payout-reports-in-publishing-portal"></a>Exibir relatórios de pagamento no Portal de Publicação
O relatório de pagamento para cada trimestre registra todas as transações que ocorreram dentro desse trimestre.

1. Acesse o portal de publicação em https://publish.windowsazure.com.
2. Na seção **Publicadores**, clique em **Relatórios de Pagamento**.
3. Clique na lista suspensa para exibir todos os relatórios de pagamentos trimestrais disponíveis.

    ![accessingpayoutreport][5]

### <a name="read-your-payout-reports"></a>Ler os relatórios de pagamento
O relatório de pagamento para cada trimestre registra todas as transações que ocorreram dentro desse trimestre.

* Se você estiver procurando por entradas do razão relacionadas a um trimestre específico, selecione o relatório de pagamento por trimestre na lista suspensa. Por exemplo, se você estiver interessado em entradas de razão de abril a junho de 2015, selecione o intervalo de datas na lista suspensa.
* Se você estiver procurando por detalhes de pagamentos relacionados a um trimestre específico, selecione o relatório de pagamento para o trimestre subsequente. Por exemplo, se você estiver interessado nos pagamentos de abril a junho de 2015, esses valores aparecerão no relatório de pagamento subsequente de julho a setembro de 2015.
  ![readingpayoutreport][6]
* O painel de resumo financeiro mostra saldos, débitos e créditos por categoria.
* Entradas da razão mostram transações individuais.

## <a name="definitions"></a>Definições
**Painel de resumo financeiro:**

![financialdefinitions][7]

**Entradas de razão:**

![ledgerdefinitions][8]

## <a name="payout-questions"></a>Perguntas de pagamento
Se você tiver perguntas relacionadas a seus pagamentos, entre em contato com nossa equipe de suporte.

![payoutquestions][9]

1. Navegue até as páginas de suporte.
2. Selecione **Pagamentos**.
3. Selecione **Pagamento relacionado a consultas**.
4. Clique em **Iniciar solicitação**.

## <a name="next-steps"></a>Próximas etapas
Para outras consultas de suporte, registre um problema em <https://portal.azure.com>.

[1]: ./media/marketplace-publishing-report-payout/LandingPage-DashboardHighlight.png
[2]: ./media/marketplace-publishing-report-payout/Dashboard-PayoutSummary.png
[3]: ./media/marketplace-publishing-report-payout/PayoutSummary-UpcomingOrMostRecentPaymentLinksSingleStatementLink.png
[4]: ./media/marketplace-publishing-report-payout/PayoutSummary-PaymentStatements-SingleStatement-FilterControl.png
[5]: ./media/marketplace-publishing-report-payout/accessingpayoutreport.png
[6]: ./media/marketplace-publishing-report-payout/readingpayoutreport.png
[7]: ./media/marketplace-publishing-report-payout/financialdefinitions.png
[8]: ./media/marketplace-publishing-report-payout/ledgerdefinitions.png
[9]: ./media/marketplace-publishing-report-payout/payoutquestions.png
