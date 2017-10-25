---
title: "Adicionar ou alterar funções de administração de assinatura do Azure | Microsoft Docs"
description: "Descreve como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure "
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 38742fd84dafc3fb68829a55ab7be0827ad0b451
ms.contentlocale: pt-br
ms.lasthandoff: 09/22/2017

---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços

Altere o administrador do Azure que gerencia sua assinatura do Azure ou que gerencia os serviços do Azure usados em sua assinatura. Para exibir as informações de cobrança do Azure e gerenciar as assinaturas, entre no Centro de Contas como o Administrador da Conta. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adicionar um administrador RBAC proprietário para uma assinatura no Portal do Azure 

Para adicionar alguém como administrador para uma assinatura no Portal do Azure, recomendamos atribuir à pessoa a função de proprietário [RBAC](../active-directory/role-based-access-control-configure.md). A função de proprietário pode gerenciar os recursos na assinatura que você atribuiu e não tem o privilégio de acesso a outras assinaturas. Os proprietários adicionados por meio do [Portal do Azure](https://portal.azure.com) não podem gerenciar recursos no [Portal Clássico do Azure](https://manage.windowsazure.com).

1. Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja que o administrador acesse.
1. Selecione **Controle de Acesso (IAM)** no menu.
1. Selecione **Adicionar** > **Função** > **Proprietário**. Digite o endereço de email do usuário que você deseja adicionar como proprietário, selecione o usuário e, em seguida, selecione **Salvar**.

    ![Captura de tela que mostra a função Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Adicionar ou alterar coadministrador

Somente um proprietário pode ser adicionado como um coadministrador. Outros usuários com funções como colaborador e leitor não podem ser adicionados como coadministradores.

1. Se você ainda não o fez, adicione alguém como proprietário seguindo as instruções acima.
2. **Clique com botão direito do mouse** no usuário proprietário você acabou de adicionar e, em seguida, selecione **Adicionar como coadministrador**. Se a opção **Adicionar como coadministrador** não for exibida, atualize a página ou tente usar outro navegador da Internet. 

     ![Captura de tela que adiciona o coadministrador](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Você precisará adicionar a conta “Proprietário” como coadministrador se o usuário precisar gerenciar os serviços do Azure no [Portal Clássico do Azure](https://manage.windowsazure.com/).

    Para remover a permissão de coadministrador, **clique com o botão direito do mouse** no usuário “coadministrador” e, em seguida, selecione **Remover coadministrador**.

    ![Captura de tela que remove o coadministrador](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Alterar o Administrador de Serviços de uma assinatura do Azure

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura. Por padrão, ao criar uma conta, o Administrador de Serviços também é o Administrador da Conta.

1. Verifique se há suporte para o seu cenário, verificando os [limites para alterar os Administradores de Serviço](#limits).
1. Entre no [Centro de Contas](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.
1. Selecione uma assinatura.
1. No lado direito, selecione **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no Centro de Contas](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do Administrador de Serviços](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitações para alterar os Administradores de Serviços

* Cada assinatura está associada a um diretório do Azure AD. Para localizar o diretório ao qual a assinatura está associada, acesse o [Portal Clássico do Azure](https://manage.windowsazure.com/), selecione **Configurações** > **Assinaturas**. Verifique a ID da assinatura para localizar o diretório.
* Se você estiver conectado a uma conta corporativa ou de estudante, poderá adicionar outras contas em sua organização como Administrador de Serviços. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços, mas não pode adicionar john@notcontoso.com, a menos que john@notcontoso.com esteja no diretório contoso.com. Usuários conectados usando contas corporativas ou de estudante podem continuar adicionando usuários da conta da Microsoft como Administradores de Serviços.

  | Método de entrada | Adicionar usuário da Conta da Microsoft como SA? | Adicionar conta corporativa ou de estudante na mesma organização que SA? | Adicionar conta corporativa ou de estudante em uma organização diferente de SA? |
  | --- | --- | --- | --- |
  |  Conta da Microsoft |Sim |Não |Não |
  |  Conta corporativa ou de estudante |Sim |Sim |Não |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Alterar o Administrador da Conta de uma assinatura do Azure

Para alterar o Administrador de Conta de uma assinatura, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Não tem certeza sobre quem é Administrador da Conta?** Siga estas etapas:

1. Entre em [Modo de exibição Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
1. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  

## <a name="types-of-azure-admin-accounts"></a>Tipos de contas de administrador do Azure

 Administrador da Conta, Administrador de Serviços e Coadministrador são os três tipos de função de administrador no Microsoft Azure. A tabela a seguir descreve a diferença entre essas três funções administrativas.

| Função administrativa | Limite | Descrição |
| --- | --- | --- |
| AA (Administrador da Conta) |1 por conta do Azure |Essa é a pessoa que se inscreveu ou que comprou assinaturas do Azure e que está autorizada a acessar o [Centro de Contas](https://account.azure.com/Subscriptions) e realizar várias tarefas de gerenciamento. A capacidade de criar assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços são algumas dessas tarefas. |
| SA (Administrador de Serviços) |1 por assinatura do Azure |Essa função está autorizada a gerenciar serviços no [Portal do Azure](https://portal.azure.com). Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços. |
| Coadministrador (CA) no [portal clássico do Azure](https://manage.windowsazure.com) |200 por assinatura |Essa função tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure. |

O RBAC (Controle de Acesso Baseado em Função) do Active Directory do Azure permite que os usuários sejam adicionados a várias funções. Para obter mais informações, confira [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Saiba mais sobre o controle de acesso a recursos e o Active Directory

* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, consulte [Noções básicas sobre o acesso aos recursos do Azure](../active-directory/active-directory-understanding-resource-access.md).
* Para saber mais sobre como o Azure Active Directory, confira [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Atribuição de funções de administrador no Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

