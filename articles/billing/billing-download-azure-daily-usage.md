---
title: Uso do modo de exibição e baixar o Azure e encargos | Microsoft Docs
description: Descreve como baixar ou exibir seu uso diário do Azure e os encargos.
keywords: uso de cobrança, os encargos de uso, uso baixar, uso do modo de exibição, o uso de fatura do azure, azure
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
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918965"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Exibir e baixar seus encargos e o uso do Azure

Se você for um cliente EA ou têm uma [contrato de cliente do Microsoft](#check-your-access-to-a-microsoft-customer-agreement), você pode baixar o uso do Azure e encargos na [portal do Azure](https://portal.azure.com/). Para outras assinaturas, vá para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) baixar o uso.

Somente certas funções têm permissão para obter informações de uso do Azure, como o administrador corporativo ou de administrador da conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

Se você tiver um [contrato de cliente do Microsoft](#check-your-access-to-a-microsoft-customer-agreement), você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou manager para exibir seu uso do Azure e os encargos de nota fiscal. Para saber mais sobre as funções de cobrança para contratos de clientes da Microsoft, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Baixar uso do Centro de Contas (.csv)

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

## <a name="download-usage-for-ea-customers"></a>Faça o download uso para clientes do EA

Para exibir e baixar dados de uso como um cliente EA, você deve ser um administrador corporativo, o proprietário da conta, ou o administrador de departamento com o modo de exibição de encargos política habilitada.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise *custo de gerenciamento + cobrança*.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **uso + encargos**.
1. No mês que você deseja fazer o download, selecione **fazer o download**.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Baixar uso para o contrato de cliente da Microsoft

Se você tiver um contrato de cliente da Microsoft, você pode baixar o seu uso do Azure e os encargos para o perfil de cobrança. Você deve ser um perfil de cobrança proprietário, colaborador, leitor, ou fatura manager para baixar os encargos CSV e o uso do Azure.

### <a name="download-usage-for-billed-charges"></a>Baixar uso para encargos cobrados

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
3. Selecione um perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança pela primeira vez.
4. Selecione **Faturas**.
5. Na grade de nota fiscal, localize a linha da fatura correspondente para o uso que você deseja baixar.
6. Clique nas reticências (`...`) no final da linha.

    ![Captura de tela que mostra as reticências no final da linha](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. No menu de contexto de download, selecione **uso do Azure e os encargos**.

     ![Captura de tela que mostra o uso do Azure e encargos selecionados](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Baixar uso para pendente encargos

Você também pode baixar uso month-to-date para o período de cobrança atual. Esses encargos de uso que ainda não foram faturados.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.
3. Selecione um perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança pela primeira vez.
4. No **visão geral** folha, localize os links de download sob os encargos month-to-date.
5. Selecione **uso do Azure e os encargos**.

    ![Captura de tela que mostra o download da visão geral](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Verifique o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre seus encargos de fatura e uso, consulte:

- [Compreender os termos em uso detalhado do Microsoft Azure](billing-understand-your-usage.md)
- [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md)
- [Exibir e baixar sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar os preços do Azure da sua organização](billing-ea-pricing.md)

Se você tiver um contrato de cliente da Microsoft, consulte:

- [Compreender os termos no seu contrato de cliente do Microsoft Azure uso detalhado](billing-mca-understand-your-usage.md)
- [Entender os encargos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-bill.md)
- [Exibir e baixar sua fatura do Microsoft Azure](billing-download-azure-invoice.md)
- [Exibir e baixar documentos de imposto para o contrato de cliente da Microsoft](billing-mca-download-tax-document.md)
- [Exibir e baixar os preços do Azure da sua organização](billing-ea-pricing.md)
