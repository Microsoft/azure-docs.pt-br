---
title: "Entenda os encargos dos serviços externos do Azure | Microsoft Docs"
description: "Saiba mais sobre a cobrança de serviços externos, anteriormente conhecidos como Marketplace, no Azure."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Entender a cobrança do Azure de encargos de serviços externos
Os serviços externos são publicados por fornecedores de software de terceiros no Azure Marketplace. Por exemplo, ClearDB e SendGrid são serviços externos que você pode adquirir no Azure, mas não são publicados pela Microsoft.

Quando você provisiona um novo recurso ou serviço externo, um aviso é exibido:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Os serviços externos são publicados por empresas que não são a Microsoft, embora, às vezes, produtos da Microsoft também sejam categorizados como serviços externos.
> 
> 

## <a name="how-external-services-are-billed"></a>Como os serviços externos são cobrados
- Os serviços externos são cobrados separadamente. Eles são tratados como pedidos individuais dentro da assinatura do Azure. O período de cobrança para cada serviço é definido quando você adquire o serviço. Ele não deve ser confundido com o período de cobrança da assinatura sob a qual você fez a compra. Você também recebe faturas separadas e seu cartão de crédito é cobrado separadamente.
- Cada serviço externo tem um modelo de cobrança diferente. Alguns serviços são pré-pagos, enquanto outros usam um modelo baseado em pagamento mensal. Você precisa de um cartão de crédito para os serviços externos do Azure. Não é possível adquirir serviços externos com pagamento por fatura.
- Não é possível usar créditos gratuitos mensais para serviços externos. Se você estiver usando uma assinatura do Azure que inclua [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), eles não poderão ser aplicados a faturas de serviços externos. Use um cartão de crédito para adquirir serviços externos.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Exibir o histórico e os gastos com serviços externos no Portal do Azure
Você pode exibir uma lista dos serviços externos que estão em cada assinatura no [Portal do Azure](https://portal.azure.com/): 

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador da conta.
2. No menu Hub, selecione **Assinaturas**.
   
    ![No menu Hub, selecione Assinaturas](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Na folha **Assinaturas**, selecione a assinatura que você deseja exibir e selecione **Serviços externos**.
   
    ![Selecione uma assinatura na folha de cobrança](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Você deve ver cada um de seus pedidos de serviços externos, o nome do publicador, a camada de serviço adquirida, o nome que você deu ao recurso e o status atual do pedido. Para ver as faturas anteriores, selecione um serviço externo.
   
    ![Selecione um serviço externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. A partir daqui, você pode exibir os valores de faturas anteriores, incluindo o detalhamento dos impostos.
   
    ![Exibir histórico de cobrança de serviços externos](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Exibir gastos com serviços externos para clientes do Enterprise Agreement (EA)
Os clientes do EA podem visualizar os gastos de serviços externos e baixar relatórios no portal do EA. Consulte [Azure Marketplace para clientes do EA](https://ea.azure.com/helpdocs/azureMarketplace) para começar.

## <a name="manage-payment-methods-for-external-service-orders"></a>Gerenciar formas de pagamento para pedidos de serviços externos
Atualize suas formas de pagamento para pedidos de serviços externos no [Centro de Contas](https://account.windowsazure.com/).

> [!NOTE]
> Se você adquiriu sua assinatura com uma conta Corporativa ou de Estudante, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para fazer alterações na forma de pagamento.
> 
> 

1. Entre no [Centro de Contas](https://account.windowsazure.com/) e [navegue até a guia **marketplace**](https://account.windowsazure.com/Store)
   
    ![Selecione marketplace no centro de contas](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Selecione o serviço externo que você deseja gerenciar
   
    ![Selecione o serviço externo que você deseja gerenciar](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Clique em **Alterar forma de pagamento** no lado direito da página. Este link leva você a um portal diferente para gerenciar sua forma de pagamento.
   
    ![Resumo do pedido](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Clique em **Editar informações** e siga as instruções para atualizar suas informações de pagamento.
   
    ![Selecione editar informações](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Cancelar pedido de serviço externo
Se você deseja cancelar seu pedido de serviço externo, exclua o recurso no [portal do Azure](https://portal.azure.com).

![Excluir recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

