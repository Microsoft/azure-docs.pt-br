---
title: "Como adicionar ou remover uma função de usuário | Microsoft Docs"
description: "Saiba como adicionar funções a identidades privilegiadas com o aplicativo Azure Active Directory Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim;oldportal;it-pro;
ms.openlocfilehash: 3ac07bb7b070f44595c099a454b3d0dbc66126c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Privileged Identity Management do Azure AD: como adicionar ou remover uma função de usuário
Com o Azure AD (Active Directory), um administrador global (ou o administrador da empresa) pode atualizar os usuários que estão **permanentemente** atribuídos a funções no Azure AD. Isso é feito com os cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`. Ou eles podem usar o portal clássico do Azure conforme descrito em [atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

O aplicativo Azure AD Privileged Identity Management permite que os administradores de função com privilégios façam atribuições de função permanentes também. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para funções de administrador. Um administrador elegível pode ativar a função quando necessário e, em seguida, suas permissões expirarão assim ele tiver terminado.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gerenciar funções com PIM no portal do Azure
Na sua organização, você pode atribuir usuários a diferentes funções administrativas no Azure AD, Office 365 e outros serviços e aplicativos Microsoft.  É possível encontrar mais detalhes sobre as funções disponíveis em [Roles in Azure AD PIM](active-directory-privileged-identity-management-roles.md)(Funções no Azure AD PIM).

Para adicionar ou remover um usuário em uma função usando o Privileged Identity Management, abra o painel PIM. Em seguida, clique no botão **Usuários em Funções de Administrador** ou selecione uma função específica (como Administrador Global) na tabela de funções.

> [!NOTE]
> Se você ainda não habilitou o PIM no portal do Azure, acesse [Get started with Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) (Introdução ao Azure AD PIM) para obter detalhes.

Se você desejar conceder acesso ao próprio PIM a outro usuário, as funções que o PIM exige que o usuário tenha serão descritas detalhadamente em [como conceder acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Adicionar um usuário a uma função
1. No [Portal do Azure](https://portal.azure.com/), selecione o bloco **Azure AD Privileged Identity Management** no painel.
2. Selecione **Gerenciar funções privilegiadas**.
3. Na tabela **Resumo da função** , selecione a função que você deseja gerenciar.
4. Na folha de função, selecione **Adicionar**.
5. Clique em **Selecionar usuários** e pesquise pelo usuário na folha **Selecionar usuários**.  
6. Selecione o usuário na lista de resultados da pesquisa e clique em **Concluído**.
7. Clique em **OK** para salvar sua seleção. O usuário que você selecionou aparecerá na lista como elegível para essa função.

> [!NOTE]
> Novos usuários em uma função só são elegíveis para a função por padrão. Se você quiser tornar a função permanente, clique no usuário na lista. As informações do usuário serão exibidas em uma nova folha. Selecione **Tornar perm.** no menu de informações do usuário.  
> Se um usuário não puder se registrar para a MFA (Autenticação Multifator do Azure) ou estiver usando uma conta da Microsoft (geralmente, @outlook.com), será preciso torná-lo permanente em todas as suas funções. Os administradores qualificados são solicitados a se registrar no MFA durante a ativação.

Agora que o usuário está qualificado para uma função, avise-o que ele pode ativá-la de acordo com as instruções em [Como ativar ou desativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função
Você pode remover usuários de atribuições de função elegíveis, mas certifique-se de que sempre haja pelo menos um usuário que seja um administrador global permanente.

Siga estas etapas para remover um usuário específico de uma função:

1. Navegue até a folha na lista de funções, seja selecionando uma função no painel do Azure AD PIM ou clicando no botão **Usuários em Funções de Administrador** .
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**. Uma mensagem solicitará que você confirme.
4. Clique em **Sim** para remover a função do usuário.

Se não tiver certeza de quais os usuários ainda precisam de suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

