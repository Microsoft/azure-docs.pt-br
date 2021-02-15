---
title: Gerenciar o acesso à cobrança do Azure
description: Saiba como permitir acesso às suas informações de cobrança do Azure aos membros da sua equipe.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/26/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 23e3adad12a726b03f6efe309dde0ec4a93bcd5b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897418"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Gerenciar acesso a informações de cobrança do Azure

Você pode fornecer a outros usuários o acesso às informações de cobrança de sua conta no portal do Azure. O tipo de funções de cobrança e as instruções para fornecer acesso às informações de cobrança variam de acordo com o tipo de sua conta de cobrança. Para determinar o tipo de sua conta de cobrança, confira [Verificar o tipo de sua conta de cobrança](#check-the-type-of-your-billing-account).

O artigo se aplica aos clientes que têm contas do programa Microsoft Online Services. Se você for cliente do Azure com um EA (Contrato Enterprise) e o administrador corporativo, pode conceder permissões aos administradores do departamento e aos proprietários da conta no Enterprise Portal. Para obter mais informações, consulte [Entendendo as funções administrativas do Azure Enterprise Agreement no Azure](understand-ea-roles.md). Se você for cliente do Contrato de Cliente da Microsoft, confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Administradores das contas do programa Microsoft Online Services

Um administrador da conta é o único proprietário de uma conta de cobrança do Programa Microsoft Online Services. A função é atribuída a uma pessoa que se inscreveu no Azure. Os administradores da conta estão autorizados a executar várias tarefas de cobrança, como criar assinaturas, exibir faturas ou alterar a cobrança de uma assinatura.

## <a name="give-others-access-to-view-billing-information"></a>Fornecer a outros usuários o acesso para exibir informações de cobrança

O administrador da conta pode permitir acesso a outras pessoas às informações de cobrança do Azure atribuindo uma das funções a seguir em uma assinatura na conta dele.

- Administrador de serviços
- Coadministrador
- Proprietário
- Colaborador
- Leitor
- Leitor de cobrança

Essas funções têm acesso às informações de cobrança no [portal do Azure](https://portal.azure.com/). As pessoas que recebem essas funções também podem usar as [APIs de Cobrança](consumption-api-overview.md#usage-details-api) para obter os detalhes de uso e faturas de forma programática.

Para atribuir funções, confira [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

** Se você é um cliente do EA, um proprietário da conta pode atribuir a função acima a outros usuários de sua equipe. No entanto, para que esses usuários exibam informações de cobrança, o administrador corporativo precisa habilitar a exibição de encargos pelo AO no Enterprise Portal.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Permitir que os usuários façam download de faturas

Depois que um administrador da conta atribuir as funções apropriadas a outros usuários, ele precisará ativar o acesso para baixar faturas no portal do Azure. Faturas anteriores a dezembro de 2016 ficam disponíveis apenas para o Administrador de Conta por enquanto.

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador da conta.

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que realça Gerenciamento de Custos e Cobrança na seção Serviços.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecione **Assinaturas** no painel à esquerda. Dependendo de seu acesso, talvez você precise selecionar um escopo do orçamento e, em seguida, selecionar **Assinaturas**.

    ![Captura de tela que mostra a seleção de assinaturas](./media/manage-billing-access/billing-select-subscriptions.png)

1. Selecione **Faturas** e, em seguida, **Acesso à fatura**.

    ![Captura de tela mostrando como delegar acesso a faturas](./media/manage-billing-access/aa-optin01.png)

1. Selecione **Em** e salve.

    ![Captura de tela mostrando o comando liga/delsiga para delegar acesso a faturas](./media/manage-billing-access/aa-optinallow01.png)

O Administrador de Conta também pode configurar para que as faturas sejam enviadas por e-mail. Para obter mais informações, consulte [Obter sua fatura no e-mail](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Conceda acesso somente leitura à cobrança

Atribua a função de Leitor de Cobrança para alguém que precisa de acesso somente leitura às informações de cobrança da assinatura, mas não a capacidade de gerenciar os serviços do Azure. Essa função é apropriada para os usuários em uma organização que são responsáveis pelo gerenciamento de custos e financeiro de assinaturas do Azure.

O recurso Leitor de Cobrança está na visualização prévia e ainda não oferece suporte a nuvens não globais.

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador da conta.

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecione **Assinaturas** no painel à esquerda. Dependendo de seu acesso, talvez você precise selecionar um escopo do orçamento e, em seguida, selecionar **Assinaturas**.

    ![Captura de tela que mostra a seleção de assinaturas](./media/manage-billing-access/billing-select-subscriptions.png)

1. Selecione **IAM (Controle de acesso)** .
1. Selecione **Adicionar** na parte superior da página.

    ![Captura de tela que mostra o clique em Adicionar atribuição de função](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. Na lista suspensa **Função**, escolha **Leitor de Cobrança**.
1. Na caixa de texto **Selecionar**, digite o nome ou email para o usuário que você deseja adicionar.
1. Selecione o usuário.
1. Clique em **Salvar**.
    ![Captura de tela que realça o botão Salvar.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Após alguns instantes, o usuário é atribuído à função Leitor de Cobrança na assinatura.

** Se você é um cliente do EA, um proprietário da conta ou um administrador do departamento pode atribuir a função Leitor de Cobrança aos membros da equipe. Mas, para esse Leitor de Cobrança exibir informações de cobrança do departamento ou da conta, o Administrador corporativo deve habilitar as políticas **Exibir encargos AO**  ou **Exibir encargos DA** no Portal empresarial.

## <a name="check-the-type-of-your-billing-account"></a>Verificar o tipo de sua conta de cobrança
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Próximas etapas

- Outras funções, como o Proprietário ou Colaborador, os usuários podem acessar as informações de cobrança não apenas, mas também os serviços do Azure. Para gerenciar essas funções, confira [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
- Para obter mais informações sobre funções, confira [Funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
