---
title: "Gerenciar o acesso a cobrança usando funções do Azure | Documentos do Microsoft"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Gerenciar o acesso a informações de cobrança do Azure usando o controle de acesso baseado em função

Você pode conceder acesso a informações de cobrança do Azure para membros da equipe atribuindo uma das seguintes funções de usuário para sua assinatura: Conta de Administrador, Administrador de serviço, Coadministrador, Proprietário, Colaborador, Leitor e Leitor de cobrança. Elas podem ter acesso a informações de cobrança no [portal do Azure](https://portal.azure.com/), e eles podem usar o [APIs de cobrança](billing-usage-rate-card-overview.md) para obter programaticamente os detalhes de uso e notas fiscais (uma vez decidido). Para obter mais informações sobre quem pode conceder funções e o que as funções podem fazer, consulte [funções no Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a>Permitindo que usuários adicionais acessem notas fiscais

O Administrador da Conta deve aceitar usando o [portal do Azure](https://portal.azure.com/) permitir acesso às notas fiscais para outros usuários e por meio da API.

1. Como Administrador da Conta, selecione sua assinatura da [folha Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no Portal do Azure.

1. Selecione **Faturas** e **Acesso às notas fiscais**.

    ![Captura de tela mostrando como delegar acesso a faturas](./media/billing-manage-access/AA-optin.png)

1. **Ative** o acesso e, depois, salve as alterações para permitir que os usuários com funções que estão no escopo da assinatura baixem a fatura.

    ![Captura de tela mostrando o comando liga/delsiga para delegar acesso a faturas](./media/billing-manage-access/AA-optinAllow.png)

Aceitar permite que o Administrador de serviço, coadministrador, proprietário, colaborador, leitor e o leitor de cobrança da assinatura para fazer o download de notas fiscais PDF no portal do Azure. No entanto, faturas anteriores a dezembro de 2016 ficam disponíveis apenas para o Administrador de Conta por enquanto.

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, consulte [Obter sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Adicionar usuários à função de Leitor de cobrança

A função de Leitor de cobrança tem acesso Somente leitura a informações de cobrança de assinatura no portal do Azure e nenhum acesso a serviços como VMs e contas de armazenamento. Atribua a função de Leitor de Cobrança para alguém que precisa acessar as informações de cobrança da assinatura, mas não a capacidade de gerenciar os serviços do Azure. Essa função é apropriada para os usuários em uma organização que executam apenas gerenciamento de custos e financeiro de assinaturas do Azure.

1. Selecione sua assinatura na [folha de Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **Controle de acesso (IAM)** e, em seguida, clique em **Adicionar**.

    ![A captura de tela mostra IAM na folha da assinatura](./media/billing-manage-access/select-iam.PNG)

1. Escolha **Leitor de cobrança** na página **Selecionar uma função**.

    ![A captura de tela mostra a o Leitor de Cobrança no modo de exibição pop-up](./media/billing-manage-access/select-roles.PNG)

1. Digite o e-mail para o usuário que deseja convidar e, em seguida, clique em **OK** para enviar o convite.

    ![A captura de tela que mostra inserir o e-mail para convidar alguém](./media/billing-manage-access/add-user.PNG)

1. Siga as instruções no e-mail de convite para fazer logon como um Leitor de Cobrança.

    ![A captura de tela que mostra o que o Leitor de Cobrança pode ver no portal do Azure](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> O recurso de Leitor de Cobrança está na visualização e ainda não da suporte às assinaturas enterprise (EA) ou nuvens não globais.

## <a name="adding-users-to-other-roles"></a>Adicionar usuários a outras funções

Outras funções, como o Proprietário ou Colaborador, os usuários podem acessar as informações de cobrança não apenas, mas também os serviços do Azure. Para gerenciar essas funções, consulte [Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Quem pode acessar o [Centro de contas](https://account.windowsazure.com)?

Somente o Administrador da Conta pode acessar o Centro de Contas do Azure. O Administrador da conta é o proprietário legal da assinatura. Por padrão, a pessoa que assinou ou comprou a assinatura do Azure é o Administrador da Conta, a menos que [a propriedade de assinatura foi transferida](billing-subscription-transfer.md) para outra pessoa. O Administrador da Conta pode criar assinaturas, cancelar inscrições, alterar o endereço de cobrança para uma assinatura e gerenciar políticas de acesso para a assinatura.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
