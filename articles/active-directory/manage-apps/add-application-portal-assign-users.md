---
title: 'Início Rápido: Atribuir usuários a um aplicativo que usa o Azure Active Directory como um provedor de identidade'
description: Este guia de início rápido explica o processo de permitir que os usuários usem um aplicativo que você configurou para usar o Azure AD como um provedor de identidade.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: iangithinji
ms.openlocfilehash: eb6797fdfb57a503b6dece9f2dfdc3cb0c67feef
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375331"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Início Rápido: Atribuir usuários a um aplicativo que está usando o Azure AD como um provedor de identidade

No guia de início rápido anterior, você configurou as propriedades de um aplicativo. Ao definir as propriedades, você configurou a experiência para usuários atribuídos e não atribuídos. Este guia de início rápido percorre o processo de atribuição de usuários ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para atribuir usuários a um aplicativo que você adicionou ao seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).
- Opcional: conclusão de [Configurar um aplicativo](add-application-portal-configure.md).

>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste guia de início rápido.

## <a name="assign-users-to-an-app"></a>Atribuir usuários a um aplicativo
1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo que você deseja configurar.
2. No menu de navegação esquerdo, selecione **Usuários e grupos**.
   > [!NOTE]
   > Alguns dos aplicativos Microsoft 365 exigem o uso do PowerShell. 
3. Selecione o botão **Adicionar usuário**.
4. No painel **Adicionar Atribuição**, selecione **Usuários e grupos**.
5. Selecione os usuários ou o grupo que deseja atribuir ao aplicativo. Você também pode começar a digitar o nome do usuário ou grupo na caixa de pesquisa. É possível escolher vários usuários e grupos. As seleções aparecerão em **Itens selecionados**.
    > [!IMPORTANT]
    > Quando você atribuir um grupo a um aplicativo, somente os usuários no grupo terão acesso. A atribuição não se propaga para grupos aninhados.

    > [!NOTE]
    > A atribuição baseada em grupo exige o Azure Active Directory Premium edição P1 ou P2. A atribuição baseada em grupo é compatível somente com os grupos de segurança. Atualmente, não há compatibilidade com associações de grupo aninhado e grupos do Microsoft 365. Para ver mais requisitos de licenciamento dos recursos discutidos neste artigo, confira a [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Quando terminar, escolha **Selecionar**.
   ![Atribuir um usuário ou um grupo ao aplicativo](./media/assign-user-or-group-access-portal/assign-users.png)
7. No painel **Usuários e grupos**, selecione um ou mais usuários ou grupos na lista. Em seguida, pressione o botão **Selecionar** na parte inferior do painel.
8. Se o aplicativo for compatível, você poderá atribuir uma função ao usuário ou grupo. Na painel **Adicionar Atribuição**, escolha **Selecionar Função**. Em seguida, no painel **Selecionar Função**, escolha uma função para aplicar aos usuários ou grupos selecionados e clique no botão **OK** na parte inferior do painel. 
    > [!NOTE]
    > Se o aplicativo não for compatível com a seleção de função, a função de acesso padrão será atribuída. Nesse caso, o aplicativo gerencia o nível de acesso dos usuários.
9. No painel **Adicionar Atribuição**, clique no botão **Atribuir** na parte inferior do painel.

Você pode cancelar a atribuição de usuários ou grupos usando o mesmo procedimento. Selecione o usuário ou grupo cuja atribuição você deseja cancelar e selecione **Remover**. Alguns dos aplicativos Microsoft 365 e Office 365 exigem o uso do PowerShell. 

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o guia de início rápido, considere excluir o aplicativo. Dessa forma, você pode manter seu locatário de teste limpo. A exclusão do aplicativo é abordada no último guia de início rápido desta série; confira [Excluir um aplicativo](delete-application-portal.md).

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para aprender a configurar o logon único para um aplicativo.
> [!div class="nextstepaction"]
> [Configurar o logon único baseado em SAML](add-application-portal-setup-sso.md)

OU

> [!div class="nextstepaction"]
> [Configurar o logon único baseado em OIDC](add-application-portal-setup-oidc-sso.md)
