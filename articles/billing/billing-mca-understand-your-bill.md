---
title: Entender os encargos na fatura do contrato cliente da Microsoft - Azure
description: Saiba como ler e entender os encargos na sua fatura.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490670"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Entender os encargos na sua fatura de contrato de cliente da Microsoft

Você pode entender os encargos na sua fatura, analisando as transações individuais. Na conta de cobrança para um contrato de cliente da Microsoft, uma fatura é gerada a cada mês para cada perfil de cobrança. A nota fiscal inclui todos os encargos do mês anterior. Você pode exibir suas faturas no portal do Azure. Para obter mais informações, consulte [download de faturas para um contrato de cliente do Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Exibir transações para uma nota fiscal no portal do Azure

1. Entre no [Portal do Azure](https://www.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione **todas as transações** do lado esquerdo da página. Dependendo do seu acesso, talvez você precise selecionar uma conta de cobrança, o perfil de cobrança ou uma seção de nota fiscal e, em seguida, selecione **todas as transações**.

4. Página todas as transações exibe as seguintes informações:

    ![Captura de tela que mostra a lista de transações cobrado](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Coluna  |Definição  |
    |---------|---------|
    |Data     | A data da transação  |
    |ID da fatura     | O identificador da nota fiscal no qual a transação foi cobrada. Se você enviar uma solicitação de suporte, compartilhe a ID com suporte do Azure para acelerar a sua solicitação de suporte |
    |Tipo de transação     |  O tipo de transação, como os encargos de uso, cancelar e compra  |
    |Família de produtos     | A categoria de produto, como computação para máquinas virtuais ou banco de dados para o banco de dados SQL do Azure|
    |Sku do produto     | Um código exclusivo que identifica a instância do seu produto |
    |Amount     |  O valor de transação      |
    |Seção de nota fiscal     | A transação aparecerá nessa seção da fatura do perfil de cobrança |
    |Perfil de faturamento     | A transação é mostrada na fatura de cobrança desse perfil |

5. Procure a ID da nota fiscal filtrar as transações da nota fiscal.

### <a name="view-transactions-by-invoice-sections"></a>Exibir transações pelas seções de nota fiscal

Seções de nota fiscal ajudam a organizar os custos para a fatura de cobrança um perfil. Para obter mais informações, consulte [entender a seção de nota fiscal](billing-mca-overview.md#invoice-sections). Quando uma fatura é gerada, os encargos para todas as seções no perfil de cobrança são mostrados na fatura.

A imagem a seguir mostra os encargos para a seção do departamento de contabilidade da fatura em uma fatura de exemplo.

![Imagem de exemplo mostrando os detalhes com informações da seção de nota fiscal](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Quando você identificou os encargos para uma seção de fatura, você pode exibir as transações no portal do Azure para entender os encargos.

1. Vá para a página de todas as transações no portal do Azure para exibir transações para uma nota fiscal. Para obter mais informações, consulte [exibir as transações de uma fatura no portal do Azure](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtre pelo nome da seção para exibir as transações da nota fiscal.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Revisar pendentes encargos para estimar sua próxima fatura

Na conta de cobrança para um contrato de cliente da Microsoft, as cobranças são estimadas e consideradas pendente até que elas são faturadas. Você pode exibir pendentes encargos no portal do Azure para estimar sua próxima fatura. Pendente encargos são estimados e elas não incluem impostos. Os encargos reais na sua próxima fatura irá variar dos encargos pendentes.

### <a name="view-summary-of-pending-charges"></a>Exiba o resumo de pendentes encargos

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo do seu acesso, você terá que selecionar uma conta de cobrança. Na conta de cobrança, selecione **perfis de cobrança** , em seguida, selecione um perfil de cobrança.

4. Selecione **resumo** guia na parte superior da tela.

5. A seção encargos exibir month-to-date e os encargos do último mês.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Os encargos de month-to-date são os encargos pendentes para o mês atual e são cobrados quando a nota fiscal é gerada para o mês. Se ainda não a fatura para o último mês é gerada, os encargos do último mês também estão pendentes e aparecerá na sua próxima fatura.

### <a name="view-pending-transactions"></a>Exibir transações pendentes

Ao identificar pendentes encargos, você pode entender os encargos, analisando as transações individuais que contribuíram para os encargos. Neste ponto, pendentes uso encargos não são exibidos na página tudo da transação. Você pode exibir os encargos de uso pendentes na página de assinaturas do Azure. Para obter mais informações, consulte [exibição pendentes encargos de uso](#view-pending-usage-charges)

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo do seu acesso, você terá que selecionar uma conta de cobrança. Na conta de cobrança, selecione **perfis de cobrança** , em seguida, selecione um perfil de cobrança.

4. Selecione **todas as transações** do lado esquerdo da página.

5. Pesquise *pendente*. Use o **Timespan** filtro para exibir pendentes encargos para atual ou último mês.

   ![Captura de tela que mostra a lista de transações pendentes](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Exibir encargos de uso pendentes

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise *custo de gerenciamento + cobrança*.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Selecione um perfil de cobrança. Dependendo do seu acesso, você terá que selecionar uma conta de cobrança. Na conta de cobrança, selecione **perfis de cobrança** , em seguida, selecione um perfil de cobrança.

4. Selecione **todas as assinaturas** um lado esquerdo da página.

5. A página de assinaturas do Azure exibe atual e os encargos do último mês para cada assinatura no perfil de cobrança. Os encargos de month-to-date são os encargos pendentes para o mês atual e são cobrados quando a nota fiscal é gerada para o mês. Se ainda não a fatura para o último mês é gerada, os encargos do último mês também estão pendentes.

    ![Captura de tela que mostra a lista de assinaturas do Azure para o perfil de cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analisar os encargos de uso do Azure

Use o arquivo CSV de uso e encargos do Azure para analisar os encargos de uso. Você pode baixar o arquivo para uma nota fiscal ou pendentes encargos. Para obter mais informações, consulte [obter sua fatura e o diário de dados de uso de cobrança do Azure](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Exibir uso detalhado pela seção de nota fiscal

Você pode filtrar o arquivo de uso e os encargos do Azure para reconciliar os encargos de uso para suas seções de nota fiscal.

As etapas a seguir orientam você durante a reconciliação de encargos de computação para a seção do departamento de contabilidade da nota fiscal:

![Imagem de exemplo mostrando os detalhes com informações da seção de nota fiscal](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Fatura em PDF | Uso do Azure e os encargos de CSV |
 | --- | --- |
 |Departamento de contabilidade |invoiceSectionName |
 |Encargos de uso - plano do Microsoft Azure |productOrderName |
 |Computação |serviceFamily |

1. Filtro de **invoiceSectionName** coluna no arquivo CSV para **departamento de contabilidade**.
2. Filtro de **productOrderName** coluna no arquivo CSV para **plano do Microsoft Azure**.
3. Filtro de **serviceFamily** coluna no arquivo CSV para **Microsoft. Compute**.

![Captura de tela que mostra o uso e os encargos de arquivo filtrado pela seção de nota fiscal](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Exibir uso detalhado por assinatura

Você pode filtrar o arquivo CSV de uso e os encargos do Azure para reconciliar os encargos de uso de suas assinaturas. Para exibir todas as assinaturas em um perfil de cobrança, consulte [pendentes encargos de uso do modo de exibição](#view-pending-usage-charges).

Ao identificar os encargos para uma assinatura, use o arquivo CSV de uso e encargos do Azure para analisar os encargos.

A imagem a seguir mostra a lista de assinaturas no portal do Azure.

![Captura de tela que mostra a lista de assinaturas do Azure para o perfil de cobrança](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtro de **subscriptionName** coluna em que o arquivo CSV de uso e os encargos do Azure para **WA_Subscription** para exibir os encargos de uso detalhado para WA_Subscription.

![Captura de tela que mostra o uso e os encargos de arquivo filtrado por assinatura](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Pagar sua fatura

Instruções para pagar sua fatura são mostradas na parte inferior da fatura. [Saiba como pagar](billing-mca-understand-your-invoice.md#how-to-pay).

Se você já pagou sua fatura, você pode verificar o status do pagamento na página de notas fiscais no portal do Azure.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e uso detalhado, consulte:

- [Como obter sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-invoice.md)
- [Compreender os termos na seu CSV de uso do contrato de cliente da Microsoft](billing-mca-understand-your-usage.md)
