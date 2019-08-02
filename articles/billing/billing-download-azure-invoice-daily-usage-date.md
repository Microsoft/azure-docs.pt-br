---
title: Baixar a fatura de cobrança e os dados de uso diário do Azure | Microsoft Docs
description: Descreve como baixar ou exibir a fatura de cobrança e os dados de uso diário do Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491399"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Baixar ou exibir a fatura de cobrança e os dados de uso diário do Azure

Para a maioria das assinaturas, você pode fazer o download da sua fatura no [portal do Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por e-mail. Se você for um cliente do Azure com um Contrato Enterprise (cliente da EA), não poderá fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição.

Se você for um cliente EA ou têm uma [contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement), você pode baixar o uso na [portal do Azure](https://portal.azure.com/). Para outras assinaturas, vá para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) baixar o uso.

Somente certas funções tem permissão para obter informações de fatura e uso, como o administrador corporativo ou de administrador da conta de cobrança. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

Se você tiver um contrato de cliente da Microsoft, você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou manager para exibir informações de cobrança e uso de nota fiscal. Para saber mais sobre as funções de cobrança para contratos de clientes da Microsoft, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

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

As faturas são geradas para cada [perfil de cobrança](billing-mca-overview.md#billing-profiles) no contrato do cliente Microsoft. Você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de download de faturas do portal do Azure de nota fiscal.

1. Pesquise **custo de gerenciamento + cobrança**.
2. Selecione um perfil de cobrança.
3. Selecione **Faturas**.
4. Na grade de nota fiscal, localize a linha da fatura que você deseja baixar.
5. Clique nas reticências (`...`) no final da linha.
6. No menu de contexto de download, selecione **fatura**.

Se você não vir uma fatura para o último período de cobrança, consulte **informações adicionais**. <!-- Fix this -->
### <a name="noinvoice"></a> Por que não encontro uma fatura do último período de cobrança?

Pode haver vários motivos pelos quais você não vê uma fatura:

- Faz menos de 30 dias que você se inscreveu no Azure.

- A fatura não foi gerada ainda. Aguarde até o final do período de cobrança.

- Você não tem permissão para exibir faturas. Se você tiver um contrato de cliente da Microsoft, você deve ser o perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de nota fiscal. Para outras assinaturas, talvez você não veja faturas antigas se você não for o administrador da conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

- Se você tiver uma avaliação gratuita ou uma quantia de crédito mensal com sua assinatura que não foi ultrapassado, você não receberá uma fatura, a menos que você tiver um contrato de cliente da Microsoft.

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

1. Pesquise **custo de gerenciamento + cobrança**.
1. Selecione um perfil de cobrança.
1. Em **Configurações**, selecione **Propriedades**.
1. Sob **fatura por Email**, selecione **preferência de nota fiscal de email de atualização**.
1. Selecione **aceitar**.
1. Clique em **Atualizar**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Recusar obtendo suas faturas de perfil de cobrança no email

Você pode optar por não obter sua fatura por email, seguindo as etapas acima e clicando em **recusar**. Todos os proprietários, colaboradores, leitores e fatura gerenciadores serão ser rejeitou a introdução da fatura por email, muito. Se você for um leitor, você não pode alterar a preferência de nota fiscal de email.

## <a name="download-usage"></a>Fazer o download de uso

 Para a maioria das assinaturas, encontre seu arquivo de uso diário no [ Centro de Contas do Azure ](https://account.azure.com/Subscriptions). Se você for um cliente EA ou têm um contrato de cliente da Microsoft, você pode baixar o uso na [portal do Azure](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Baixar uso do Centro de Contas (.csv)

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.

2. Selecione a assinatura da qual deseja obter informações de fatura e uso.

3. Clique em **HISTÓRICO DE COBRANÇA**.

    ![Captura de tela que mostra a opção de histórico de cobrança](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Você pode ver os demonstrativos dos últimos seis períodos de cobrança e o período atual não faturado.

    ![Captura de tela que mostra os períodos de cobrança, as opções para baixar a fatura e o uso diário e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Exibir Demonstrativo Atual** para ver uma estimativa dos seus encargos no momento em que a estimativa foi gerada. Essas informações são atualizadas diariamente e talvez não incluam todo o seu uso. Sua fatura mensal pode ser diferente dessa estimativa.

    ![Captura de tela que mostra a opção de Exibir Demonstrativo Atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de tela que mostra a estimativa das cobranças atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Baixar Uso** para baixar os dados de uso diário como um arquivo CSV. Se vir duas versões disponíveis, baixe a versão 2.

    ![Captura de tela que mostra a opção de Baixar Uso](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Somente o Administrador da Conta pode acessar o Centro de Contas do Azure. Outros administradores de cobrança, por exemplo um proprietário, podem obter informações de uso usando as [APIs de cobrança](billing-usage-rate-card-overview.md).

Para saber mais sobre o uso diário, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda sobre como gerenciar os custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Faça o download uso para clientes do EA

Para exibir e baixar dados de uso como um cliente EA, você deve ser um administrador corporativo, o proprietário da conta, ou o administrador de departamento com o modo de exibição de encargos política habilitada.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise *custo de gerenciamento + cobrança*.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **uso + encargos**.
1. No mês que você deseja fazer o download, selecione **fazer o download**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Baixar uso para o contrato de cliente da Microsoft

Para exibir e baixar dados de uso para um perfil de cobrança, você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou Gerenciador de nota fiscal.

#### <a name="download-usage-for-billed-charges"></a>Baixar uso para encargos cobrados

1. Pesquise **custo de gerenciamento + cobrança**.
2. Selecione um perfil de cobrança.
3. Selecione **Faturas**.
4. Na grade de nota fiscal, localize a linha da fatura correspondente para o uso que você deseja baixar.
5. Clique nas reticências (`...`) no final da linha.
6. No menu de contexto de download, selecione **uso do Azure e os encargos**.

#### <a name="download-usage-for-open-charges"></a>Baixar uso para encargos em aberto

Você também pode baixar uso month-to-date para o período de cobrança atual, o que significa que os encargos não foram cobrados ainda.

1. Pesquise **custo de gerenciamento + cobrança**.
2. Selecione um perfil de cobrança.
3. No **visão geral** folha, clique em **Baixe o uso do Azure e os encargos**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e os encargos, consulte:

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
