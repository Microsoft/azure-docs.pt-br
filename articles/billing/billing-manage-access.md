---
title: Gerenciar o acesso a cobrança do Azure | Microsoft Docs
description: Saiba como conceder acesso às informações de cobrança do Azure para membros da equipe.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491163"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerenciar acesso a informações de cobrança do Azure

Você pode fornecer outras pessoas acessem as informações de cobrança para sua conta no portal do Azure. O tipo de cobrança de funções e as instruções para fornecer acesso às informações de cobrança variam de acordo com o tipo de sua conta de cobrança. Para determinar o tipo de sua conta de cobrança, consulte [verificar o tipo de sua conta de cobrança](#check-the-type-of-your-billing-account).

O artigo se aplica aos clientes com contas do programa de serviço Online da Microsoft. Se você for um cliente do Azure com um Enterprise Agreement (EA) e o administrador da empresa, você pode conceder permissões para os administradores de departamento e os proprietários de conta no Enterprise portal. Para obter mais informações, consulte [Entendendo as funções administrativas do Azure Enterprise Agreement no Azure](billing-understand-ea-roles.md). Se você for um cliente de contrato de cliente da Microsoft, consulte, [funções administrativas de entender contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administradores de contas para contas do programa de serviço Online da Microsoft

Um administrador da conta é o único proprietário para uma conta de cobrança do programa de serviço Online da Microsoft. A função é atribuída a uma pessoa que se inscreveram para o Azure. Os administradores de contas estão autorizados a realizar várias tarefas de cobrança, como criar assinaturas, exibir faturas ou alterar a cobrança para uma assinatura.

## <a name="give-others-access-to-view-billing-information"></a>Que outras pessoas acesso para exibir informações de cobrança

Administrador da conta pode conceder outras pessoas acesso a informações de cobrança do Azure, atribuindo uma das seguintes funções em uma assinatura em sua conta.

- Administrador de serviços
- Coadministrador
- Proprietário
- Colaborador
- Leitor
- Leitor de cobrança

Essas funções têm acesso a informações de cobrança na [portal do Azure](https://portal.azure.com/). As pessoas que são atribuídas a essas funções também podem usar o [APIs de cobrança](billing-usage-rate-card-overview.md) obter programaticamente os detalhes de uso e faturas.

Para atribuir funções, confira [Gerenciar acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

\* * Se você for um cliente EA, o proprietário da conta pode atribuir a função acima a outros usuários da sua equipe. Mas, para esses usuários exibir informações de cobrança, o administrador da empresa deve habilitar os encargos de exibição do sol no Enterprise portal.


### <a name="opt-in"></a> Permitir que os usuários façam download de faturas

Depois que um administrador da conta tem atribuídos às funções apropriadas para outros usuários, eles devem ativar o acesso ao download de faturas no portal do Azure. Faturas anteriores a dezembro de 2016 ficam disponíveis apenas para o Administrador de Conta por enquanto.

1. Entrar para o [portal do Azure](https://portal.azure.com/), como um administrador da conta,

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Selecione **assinaturas** do painel esquerdo. Dependendo do seu acesso, você precisará selecionar um escopo de cobrança e, em seguida, selecione **assinaturas**.
 
    ![Captura de tela que mostra como selecionar assinaturas](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selecione **faturas** e, em seguida **acesso à fatura**.

    ![Captura de tela mostrando como delegar acesso a faturas](./media/billing-manage-access/AA-optin.png)

1. Selecione **Em** e salve.

    ![Captura de tela mostrando o comando liga/delsiga para delegar acesso a faturas](./media/billing-manage-access/AA-optinAllow.png)

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, consulte [Obter sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Conceda acesso somente leitura à cobrança

Atribua a função de Leitor de Cobrança para alguém que precisa de acesso somente leitura às informações de cobrança da assinatura, mas não a capacidade de gerenciar os serviços do Azure. Essa função é apropriada para os usuários em uma organização que são responsáveis pelo gerenciamento de custos e financeiro de assinaturas do Azure.

O recurso Leitor de Cobrança está na visualização prévia e ainda não oferece suporte a nuvens não globais.

1. Entrar para o [portal do Azure](https://portal.azure.com/), como um administrador da conta,

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Selecione **assinaturas** do painel esquerdo. Dependendo do seu acesso, você precisará selecionar um escopo de cobrança e, em seguida, selecione **assinaturas**.
 
    ![Captura de tela que mostra como selecionar assinaturas](./media/billing-manage-access/billing-select-subscriptions.png)

1. Selecione **IAM (Controle de acesso)** .
1. Selecione **adicionar** da parte superior da página.

    ![Captura de tela que mostra clicando em Adicionar atribuição de função](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. Na lista suspensa **Função**, escolha **Leitor de Cobrança**.
1. Na caixa de texto **Selecionar**, digite o nome ou email para o usuário que você deseja adicionar.
1. Selecione o usuário.
1. Clique em **Salvar**.
    ![Captura de tela que mostra clicando em Adicionar atribuição de função](./media/billing-manage-access/billing-save-role-assignment.png)

1. Após alguns instantes, o usuário é atribuído à função de leitor de cobrança para a assinatura.

\* * Se você for um cliente EA, um proprietário de conta ou o administrador de departamento pode atribuir a função de leitor de cobrança para os membros da equipe. Mas, para esse Leitor de Cobrança exibir informações de cobrança do departamento ou da conta, o Administrador corporativo deve habilitar as políticas **Exibir encargos AO**  ou **Exibir encargos DA** no Portal empresarial.

## <a name="check-the-type-of-your-billing-account"></a>Verifique o tipo da sua conta de cobrança
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Próximas etapas

- Outras funções, como o Proprietário ou Colaborador, os usuários podem acessar as informações de cobrança não apenas, mas também os serviços do Azure. Para saber mais detalhes, confira [Gerenciar acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre as funções, consulte [Funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
