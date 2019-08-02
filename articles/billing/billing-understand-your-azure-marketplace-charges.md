---
title: Entenda os encargos dos serviços externos do Azure | Microsoft Docs
description: Saiba mais sobre a cobrança de serviços externos, anteriormente conhecidos como Marketplace, no Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490345"
---
# <a name="understand-your-azure-external-services-charges"></a>Entenda os encargos de serviços externos do Azure
Serviços externos são publicados por fornecedores de software de terceiros no Azure marketplace. Por exemplo, o SendGrid é um serviço externo que você pode comprar no Azure, mas não é publicado pela Microsoft. Alguns produtos da Microsoft são vendidos por meio do Azure marketplace, muito.

## <a name="how-external-services-are-billed"></a>Como os serviços externos são cobrados

- Se você tiver um [contrato de cliente do Microsoft](#check-access), seus serviços de terceiros são cobrados com o restante dos seus serviços do Azure.
- Se você não tiver um contrato de cliente da Microsoft, seus serviços externos são cobrados separadamente dos serviços do Azure.
- Cada serviço externo tem um modelo de cobrança diferente. Alguns serviços são cobrados pré-pagos, enquanto outros têm fixos de encargos mensais.
- Não é possível usar créditos gratuitos mensais para serviços externos. Se você estiver usando uma assinatura do Azure que inclui [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), não pode ser aplicados aos encargos de serviços externos. Quando você provisiona um novo recurso ou serviço externo, um aviso é exibido:

    ![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Exibir e baixar faturas

Se você tiver um [contrato de cliente do Microsoft](#check-access), os encargos de terceiros estão na mesma fatura como os encargos do Azure. Saiba como [exibir e baixar sua fatura do Azure](billing-download-azure-invoice.md) do portal do Azure para ver os encargos de terceiros.

Se você não tiver um contrato de cliente da Microsoft, você tem faturas separadas para os encargos de terceiros. Você pode exibir e baixar suas faturas do Azure Marketplace do portal do Azure seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **custo de gerenciamento + cobrança**.
1. No menu à esquerda, selecione **faturas**.
1. Clique no **do Azure Marketplace e reservas** guia.  ![Imagem da guia de reservas e o marketplace do Azure](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. No menu suspenso assinatura, selecione a assinatura que contém os serviços externos que você deseja ver as notas fiscais para.

## <a name="external-spending-for-ea-customers"></a>Externo gastos para clientes do EA

Os clientes do EA podem visualizar os gastos de serviços externos e baixar relatórios no portal do EA. Consulte [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="manage-payment-for-external-services"></a>Gerenciar o pagamento para serviços externos

Ao comprar um serviço externo, você pode escolher uma assinatura do Azure para o recurso. O método de pagamento da assinatura do Azure selecionada torna-se o método de pagamento para o serviço externo. Para alterar o método de pagamento para um serviço externo, você deve [alterar o método de pagamento da assinatura do Azure](billing-how-to-change-credit-card.md) vinculados a esse serviço externo. Você pode descobrir qual assinatura do seu pedido de serviço externo está vinculado ao seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em **todos os recursos** no menu de navegação à esquerda.
     ![captura de tela de todos os recursos do item de menu](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Procure seu serviço externo.
1. Procure o nome da assinatura na **assinatura** coluna.
    ![captura de tela do nome da assinatura para o recurso](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Clique no nome da assinatura e [atualize o método de pagamento ativo](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Cancelar pedido de serviço externo
Se você deseja cancelar seu pedido de serviço externo, exclua o recurso no [portal do Azure](https://portal.azure.com).

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Clique em **todos os recursos** no menu de navegação à esquerda.
    ![captura de tela de todos os recursos do item de menu](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Procure seu serviço externo.
1. Marque a caixa ao lado do recurso que deseja excluir.
1. Selecione **excluir** na barra de comandos.
    ![Captura de tela do botão Excluir](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Tipo de *'Sim'* na folha de confirmação.
    ![Excluir recurso](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Clique em **Excluir**.

## <a name="check-access"></a>Verificar acesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [Começar a analisar os custos](../cost-management/quick-acm-cost-analysis.md)
