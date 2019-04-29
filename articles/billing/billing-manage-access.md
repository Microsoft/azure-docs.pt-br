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
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918420"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerenciar acesso a informações de cobrança do Azure

Para a maioria das assinaturas, você pode conceder acesso de informações de cobrança para os membros da sua equipe das **Assinaturas** no portal do Azure. Se você for um cliente do Azure com um Enterprise Agreement (cliente EA) e o Administrador corporativo, você pode conceder permissões para os Administradores de departamento e os Proprietários de conta no Portal empresarial.

## <a name="give-access-to-billing"></a>Conceder acesso à cobrança

Todos, exceto os clientes do EA podem conceder acesso a informações de cobrança do Azure atribuindo uma das seguintes funções de usuário para os membros da equipe:

- Administrador de conta
- Administrador de serviços
- Coadministrador
- Proprietário
- Colaborador
- Leitor
- Leitor de cobrança

Para atribuir funções, confira [Gerenciar acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).

Essas funções têm acesso à cobrança no [portal do Azure](https://portal.azure.com/). As pessoas que são atribuídas a essas funções também podem usar as [APIs de Cobrança](billing-usage-rate-card-overview.md) para obter programaticamente os detalhes de uso e faturas. Para obter mais informações, veja [Funções no RBAC do Azure](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Permitir que os usuários façam download de faturas

Depois de atribuir as funções apropriadas para os membros da equipe, o administrador da conta deve ativar o acesso ao download de faturas no portal do Azure. Faturas anteriores a dezembro de 2016 ficam disponíveis apenas para o Administrador de Conta por enquanto.

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Como Administrador da Conta, selecione sua assinatura da [folha Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no Portal do Azure.

1. Selecione **Faturas** e **Acesso às notas fiscais**.

    ![Captura de tela mostrando como delegar acesso a faturas](./media/billing-manage-access/AA-optin.png)

1. Selecione **Em** e salve.

    ![Captura de tela mostrando o comando liga/delsiga para delegar acesso a faturas](./media/billing-manage-access/AA-optinAllow.png)

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, consulte [Obter sua fatura no e-mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Conceda acesso somente leitura à cobrança

Atribua a função de Leitor de Cobrança para alguém que precisa de acesso somente leitura às informações de cobrança da assinatura, mas não a capacidade de gerenciar os serviços do Azure. Essa função é apropriada para os usuários em uma organização que são responsáveis pelo gerenciamento de custos e financeiro de assinaturas do Azure.

Se você for um cliente EA, um proprietário de conta ou o administrador de departamento pode atribuir a função de leitor de cobrança para os membros da equipe. Mas, para esse Leitor de Cobrança exibir informações de cobrança do departamento ou da conta, o Administrador corporativo deve habilitar as políticas **Exibir encargos AO**  ou **Exibir encargos DA** no Portal empresarial.

O recurso Leitor de Cobrança está na visualização prévia e ainda não oferece suporte a nuvens não globais.

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Selecione sua assinatura na [folha de Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure.

1. Selecione **IAM (Controle de acesso)**.
1. Selecione **Atribuições de função** para exibir todas as atribuições de função para esta assinatura.
1. Selecione **Adicionar atribuição de função**.
1. Na lista suspensa **Função**, escolha **Leitor de Cobrança**.
1. Na caixa de texto **Selecionar**, digite o nome ou email para o usuário que você deseja adicionar.
1. Selecione o usuário.
1. Clique em **Salvar**.
1. Após alguns instantes, o usuário é atribuído à função Leitor de Cobrança no escopo da assinatura.
1. O Leitor de Cobrança recebe um email com um link para entrar.

    ![A captura de tela que mostra o que o Leitor de Cobrança pode ver no portal do Azure](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Permitir o acesso de cobrança do Administrador de departamento ou Proprietário da conta

O Administrador corporativo pode permitir que os Administradores de departamento e os Proprietários de conta exibam detalhes de uso e os custos associados aos Departamentos e Contas que eles gerenciam.

1. Entrar como Administrador corporativo, para o [Portal de EA](https://ea.azure.com/).
1. Selecione **Gerenciar**.
1. Em **Inscrever**, altere **Exibir encargos DA** para **Habilitado** para o Administrador de departamento para exibir uso e custos.
1. Altere **Exibir encargos VA** para **Habilitado** para o proprietário da conta para exibir o uso e custos.


Para obter mais informações, consulte [Entender as funções administrativas do Azure Enterprise Agreement no Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Somente os administradores de conta podem acessar o Centro de Contas

O Administrador da conta é o proprietário legal da assinatura. Por padrão, a pessoa que assinou ou comprou a assinatura do Azure é o Administrador da Conta, a menos que [a propriedade de assinatura foi transferida](billing-subscription-transfer.md) para outra pessoa. O Administrador da Conta pode criar assinaturas, cancelar inscrições, alterar o endereço de cobrança para uma assinatura e gerenciar políticas de acesso para a assinatura no [Centro de Contas](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Próximas etapas

- Outras funções, como o Proprietário ou Colaborador, os usuários podem acessar as informações de cobrança não apenas, mas também os serviços do Azure. Para saber mais detalhes, confira [Gerenciar acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre as funções, consulte [Funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
