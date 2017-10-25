---
title: Regras no Azure AD Privileged Identity Management | Microsoft Docs
description: "Aprenda quais funções são usadas para identidades com privilégios com a extensão de Privileged Identity Management do Azure."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: c20aca4202319154b01d6398570f745636120f49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Função administrativa diferente no PIM do Azure Active Directory
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Você pode atribuir usuários na sua organização a diferentes funções administrativas no Azure AD. Essas atribuições de função controlam quais tarefas, como adicionar ou remover usuários ou alterar configurações do serviço, os usuários podem executar no Azure AD, Office 365 e outros aplicativos conectados e Microsoft Online Services.  

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo.

Um administrador global pode atualizar quais usuários são **permanentemente** atribuídos às funções no Azure AD, usando cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`, ou por meio do portal clássico, como descrito em [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

O Azure AD PIM (Privileged Identity Management) gerencia políticas para o acesso privilegiado para usuários no Azure AD. O PIM atribui usuários a uma ou mais funções no Azure AD e você pode atribuir uma pessoa para estar permanentemente na função ou qualificada para a função. Quando um usuário é atribuído permanentemente a uma função ou ativa uma atribuição de função qualificada, ele pode gerenciar o Azure Active Directory, o Office 365 e outros aplicativos com as permissões atribuídas às suas funções.

Não há nenhuma diferença no modo de acesso concedido a uma pessoa com uma atribuição de função permanente em comparação com uma qualificada. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. Elas são qualificadas para a função e podem ativá-la ou desativá-la sempre que precisarem.

## <a name="roles-managed-in-pim"></a>Funções gerenciadas no PIM
O Privileged Identity Management permite que você atribua usuários às funções de administrador comuns, incluindo:

* **Administrador global** (também conhecido como Administrador da empresa) tem acesso a todos os recursos administrativos. Você pode ter mais de um administrador global na sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente se torna um administrador global.
* **Administrador com função com privilégios** gerencia o Azure AD PIM e atualiza as atribuições de função para outros usuários.  
* **Administrador de cobrança** faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
* **Administrador de senhas** redefine as senhas, gerencia as solicitações de serviço e monitora a integridade do serviço. Os administradores de senha são limitados à redefinição de senhas para usuários.
* **Administrador de serviços** gerencia as solicitações de serviço e monitora a integridade do serviço.
  
  > [!NOTE]
  > Se você estiver usando o Office 365, antes de atribuir a função de administrador de serviço a um usuário, primeiro atribua permissões administrativas ao usuário para um serviço, como o Exchange Online.
  > 
  > 
* **Administrador de gerenciamento de usuários** redefine as senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. O administrador de gerenciamento de usuários não pode excluir um administrador global, criar outras funções de administrador ou redefinir senhas para administradores de cobrança, globais e de serviço.
* **Administrador do Exchange** tem acesso administrativo ao Exchange Online por meio do EAC (Centro de Administração do Exchange) e pode executar quase qualquer tarefa no Exchange Online.
* **Administrador do SharePoint** tem acesso administrativo ao SharePoint Online por meio do Centro de Administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online.
* **Administrador do Skype for Business** tem acesso administrativo ao Skype for Business por meio do Centro de Administração do Skype for Business e pode executar quase qualquer tarefa no Skype for Business Online.

Leia estes artigos para obter mais detalhes sobre como [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md) e [atribuir funções de administrador no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


No PIM, você pode [atribuir essas funções para um usuário](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que ele possa [ativar a função quando necessário](active-directory-privileged-identity-management-how-to-activate-role.md).

Se você desejar conceder acesso para gerenciar no próprio PIM a outro usuário, as funções que o PIM exige que o usuário tenha serão descritas detalhadamente em [como conceder acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Funções não gerenciadas no PIM
Funções no Exchange Online ou SharePoint Online, exceto por aquelas mencionadas acima, não são representadas no Azure AD e, portanto, não são visíveis no PIM. Para obter mais informações sobre como alterar as atribuições de função refinadas nesses serviços do Office 365, consulte [Permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

As assinaturas e grupos de recursos do Azure também não são representados no Azure AD. Para gerenciar as assinaturas do Azure, consulte [Como adicionar ou alterar as funções de administrador do Azure](../billing/billing-add-change-azure-subscription-administrator.md) e para obter mais informações sobre o RBAC do Azure, consulte [Controle de Acesso Baseado em Funções do Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Funções de usuário e entrada
Para alguns serviços e aplicativos da Microsoft, atribuir um usuário a uma função pode não ser suficiente para permitir que aquele usuário seja um administrador.

O acesso ao portal clássico do Azure requer que o usuário seja um administrador ou coadministrador de serviços em uma assinatura do Azure, mesmo se o usuário não precisar gerenciar as assinaturas do Azure.  Por exemplo, para gerenciar as definições de configuração do Azure AD no portal clássico, um usuário deve ser um administrador global no Azure AD e um coadministrador de assinatura em uma assinatura do Azure.  Para saber como adicionar usuários às assinaturas do Azure, consulte [Como adicionar ou alterar funções de administrador do Azure](../billing/billing-add-change-azure-subscription-administrator.md).

O acesso ao Microsoft Online Services pode exigir que o usuário também tenha uma licença atribuída antes de poder abrir o portal do serviço ou realizar tarefas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Atribuir uma licença a um usuário no Azure AD
1. Entre no [Portal Clássico do Azure](http://manage.windowsazure.com) com uma conta de administrador global ou uma conta de coadministrador.
2. Selecione **Todos os Itens** no menu principal.
3. Selecione o diretório com o qual você deseja trabalhar e que tem licenças associadas a ele.
4. Selecione **Licenças**. A lista de licenças disponíveis será exibida.
5. Selecione o plano de licença que contém as licenças que você deseja distribuir.
6. Selecione **Atribuir Usuários**.
7. Selecione o usuário ao qual você deseja atribuir uma licença.
8. Clique no botão **Atribuir** .  O usuário agora pode entrar no Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

