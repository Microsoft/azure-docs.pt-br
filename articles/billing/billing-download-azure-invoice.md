---
title: Exibir e baixar sua fatura do Microsoft Azure | Microsoft Docs
description: Descreve como exibir e baixar sua fatura do Microsoft Azure
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617907"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Exibir e baixar sua fatura do Microsoft Azure

Para a maioria das assinaturas, você pode fazer o download da sua fatura no [portal do Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por e-mail. Se você for um cliente do Azure com um Contrato Enterprise (cliente da EA), não poderá fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição.

Somente certas funções tem permissão para exibir faturas, como o administrador da conta ou administrador corporativo. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

Se você tiver um [contrato de cliente do Microsoft](#check-your-access-to-a-microsoft-customer-agreement), você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de suas faturas de nota fiscal. Para saber mais sobre as funções de cobrança para contratos de clientes da Microsoft, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Baixe suas faturas do Azure (. PDF)

Para a maioria das assinaturas, você pode baixar sua fatura do portal do Azure. Se você tiver um contrato de cliente da Microsoft, consulte o Download notas fiscais para um perfil de cobrança.

### <a name="download-invoices-for-an-individual-subscription"></a>Download de faturas para uma assinatura individual

1. Selecione sua assinatura dos [página de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure como [um usuário com acesso a faturas](billing-manage-access.md).

2. Selecione **Faturas**.

    ![Captura de tela que mostra a opção de Cobrança e Uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Clique em **Baixar Fatura** para exibir uma cópia da sua fatura em PDF. Se o resultado for **Não disponível**, veja [Por que não encontro uma fatura do último período de cobrança?](#noinvoice)

    ![Captura de tela que mostra os períodos de cobrança, a opção de download e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Você também pode exibir seu uso diário clicando no período de cobrança.

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda sobre como gerenciar os custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Download de faturas para um contrato de cliente da Microsoft

As faturas são geradas para cada [perfil de cobrança](billing-mca-overview.md#understand-billing-profiles) no contrato do cliente Microsoft. Você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de download de faturas do portal do Azure de nota fiscal.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. Selecione um perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança pela primeira vez.
1. Selecione **Faturas**.
1. Na grade de nota fiscal, localize a linha da fatura que você deseja baixar.
1. Clique nas reticências (`...`) no final da linha.
    ![Captura de tela que mostra as reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. No menu de contexto de download, selecione **fatura**.

    ![Captura de tela que mostra o menu de contexto](./media/billing-download-azure-invoice/contextmenu.png)

Se você não vir uma fatura para o último período de cobrança, consulte [por que não vê uma fatura para o último período de cobrança?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Obter sua fatura por email (.pdf)

Você pode aceitar e configurar destinatários adicionais para receber sua fatura do Azure em um email. Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open. Se você tiver um contrato do Microsoft Customer, consulte Get notas fiscais no email de seu perfil de cobrança.

### <a name="get-your-subscriptions-invoices-in-email"></a>Obter notas fiscais do sua assinatura email

1. Selecione sua assinatura na [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Aceitar para cada assinatura que você possui. Clique em **Faturas** e em **Enviar minha fatura por email**.

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Clique em **Aceitar** e aceite os termos.

    ![Captura de tela que mostra a etapa 2 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Depois de aceitar o contrato, você poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de email não é armazenado. Se você mudar de ideia, você precisa adicioná-los novamente.

    ![Captura de tela que mostra a etapa 3 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Se você não receber um email depois de seguir as etapas, verifique se seu endereço de email está correto nas [opções de comunicação de seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Recusar obtendo faturas da sua assinatura no email

Você pode optar por não obter sua fatura por email, seguindo as etapas acima e clicando em **recusar as faturas por email**. Essa opção remove todos os endereços de e-mail configurados para receber faturas por e-mail. Se você optar por voltar no, você pode reconfigurar os destinatários.

 ![Captura de tela que mostra o fluxo para recusar](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Obtenha suas faturas de contrato de cliente da Microsoft por email

Se você tiver um contrato de cliente da Microsoft, você pode optar por obter sua fatura em um email. Todos os cobrança perfil proprietários, colaboradores, leitores e fatura gerenciadores receberá a fatura por email. Os leitores não é possível atualizar a preferência de nota fiscal de email.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
1. Selecione um perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança pela primeira vez.
1. Em **Configurações**, selecione **Propriedades**.
1. Sob **fatura por Email**, selecione **preferência de nota fiscal de email de atualização**.

    ![Captura de tela que mostra as propriedades de nota fiscal de email](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Selecione **aceitar**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Recusar obtendo suas faturas de contrato de cliente da Microsoft por email

Você pode optar por não obter sua fatura por email, seguindo as etapas acima e clicando em **recusar**. Todos os proprietários, colaboradores, leitores e fatura gerenciadores serão ser rejeitou a introdução da fatura por email, muito. Se você for um leitor, você não pode alterar a preferência de nota fiscal de email.

### <a name="noinvoice"></a> Por que não encontro uma fatura do último período de cobrança?

Pode haver vários motivos pelos quais você não vê uma fatura:

- Faz menos de 30 dias que você se inscreveu no Azure.

- A fatura não foi gerada ainda. Aguarde até o final do período de cobrança.

- Você não tem permissão para exibir faturas. Se você tiver um contrato de cliente da Microsoft, você deve ser o perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de nota fiscal. Para outras assinaturas, talvez você não veja faturas antigas se você não for o administrador da conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

- Se você tiver uma avaliação gratuita ou uma quantia de crédito mensal com sua assinatura que não foi ultrapassado, você não receberá uma fatura, a menos que você tiver um contrato de cliente da Microsoft.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verifique o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e os encargos, consulte:

- [Exibir e baixar seus encargos e o uso do Microsoft Azure](billing-download-azure-daily-usage.md)
- [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Compreender os termos na sua fatura do Azure](billing-understand-your-invoice.md)
- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Exiba os preços do Azure da sua organização](billing-ea-pricing.md)

Se você tiver um contrato de cliente da Microsoft, consulte:

- [Entender os encargos na fatura para o perfil de cobrança](billing-mca-understand-your-bill.md)
- [Compreender os termos na fatura para o perfil de cobrança](billing-mca-understand-your-invoice.md)
- [Entender o arquivo de uso e os encargos do Azure para o perfil de cobrança](billing-mca-understand-your-usage.md)
- [Exibir e baixar documentos de imposto para o perfil de cobrança](billing-mca-download-tax-document.md)
- [Exiba os preços do Azure da sua organização](billing-ea-pricing.md)
