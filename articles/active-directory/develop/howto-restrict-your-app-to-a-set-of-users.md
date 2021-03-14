---
title: Restringir o aplicativo Azure AD a um conjunto de usuários | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a restringir o acesso a aplicativos registrados no Azure AD a um conjunto selecionado de usuários.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199593"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Como restringir seu aplicativo do Azure AD a um conjunto de usuários em um locatário do Azure AD

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito.

Da mesma forma, no caso de um aplicativo [multilocatário](howto-convert-app-to-be-multi-tenant.md), todos os usuários no locatário do Azure AD em que esse aplicativo é provisionado conseguirão acessá-lo após a autenticação bem-sucedida em seu respectivo locatário.

Os desenvolvedores e administradores de locatários geralmente têm requisitos, como aplicativos que devem ser restritos a um determinado conjunto de usuários. Os desenvolvedores podem realizar o mesmo usando padrões de autorização populares, como o Azure RBAC (controle de acesso baseado em função), mas essa abordagem requer uma quantidade significativa de trabalho em parte do desenvolvedor.

Administradores e desenvolvedores de locatários podem restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário usando esse recurso interno do Azure AD também.

## <a name="supported-app-configurations"></a>Configurações de aplicativo com suporte

A opção de restringir um aplicativo a um conjunto específico de usuários ou grupos de segurança em um locatário funciona com os seguintes tipos de aplicativos:

- Aplicativos configurados para logon único federado com autenticação baseada em SAML.
- Aplicativos de proxy de aplicativo que usam pré-autenticação do Azure AD.
- Aplicativos criados diretamente na plataforma de aplicativos do Azure AD que usam a autenticação OAuth 2.0/OpenID Connect depois que um usuário ou administrador autoriza o aplicativo.

     > [!NOTE]
     > Esse recurso está disponível para aplicativos Web/API Web e aplicativos corporativos apenas. Aplicativos que são registrados como [nativos](./quickstart-register-app.md) não podem ficar restritos a um conjunto de usuários ou grupos de segurança no locatário.

## <a name="update-the-app-to-enable-user-assignment"></a>Atualizar o aplicativo para habilitar a atribuição de usuários

Há duas maneiras de criar um aplicativo com atribuição de usuário habilitada. Uma delas requer a função de **administrador global** , a segunda não.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Aplicativos empresariais (requer a função de administrador global)

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> como um **Administrador Global**.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **gerenciar**, selecione **aplicativos empresariais**  >  **todos os aplicativos**.
1. Selecione o aplicativo para o qual você deseja atribuir um usuário ou um grupo de segurança da lista. 
    Use os filtros na parte superior da janela para procurar um aplicativo específico.
1. Na página **visão geral** do aplicativo, em **gerenciar**, selecione **Propriedades**.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários no locatário devem primeiro ser atribuídos a esse aplicativo ou não poderão entrar neste aplicativo.
1. Selecione **Salvar**.

### <a name="app-registration"></a>Registro do aplicativo

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo**.
1. Crie ou selecione o aplicativo que você deseja gerenciar. Você precisa ser o **proprietário** deste aplicativo.
1. Na página **visão geral** do aplicativo, selecione o link **aplicativo gerenciado no diretório local** na seção **Essentials** .
1. Em **Gerenciar**, selecione **Propriedades**.
1. Localize a configuração **Atribuição de usuário obrigatória?** e defina-a como **Sim**. Quando essa opção é definida como **Sim**, os usuários no locatário devem primeiro ser atribuídos a esse aplicativo ou não poderão entrar neste aplicativo.
1. Selecione **Salvar**.

## <a name="assign-users-and-groups-to-the-app"></a>Atribuir usuários e grupos ao aplicativo

Após configurar seu aplicativo para habilitar a atribuição de usuários, prossiga e atribua usuários e grupos ao aplicativo.

1. Em **gerenciar**, selecione os **usuários e grupos**  >  **Adicionar usuário/grupo** .
1. Selecione o seletor **usuários** . 

     Uma lista de usuários e grupos de segurança será exibida em conjunto com uma caixa de texto para pesquisar e localizar um determinado usuário ou grupo. Esta tela permite que você escolha vários usuários e grupos de uma só vez.

1. Quando terminar de selecionar os usuários e grupos, selecione **selecionar**.
1. Adicional Se você tiver definido funções de aplicativo em seu aplicativo, poderá usar a opção **selecionar função** para atribuir os usuários e grupos selecionados a uma das funções do aplicativo. 
1. Selecione **atribuir** para concluir as atribuições de usuários e grupos para o aplicativo. 
1. Confirme que os usuários e grupos que você adicionou estão aparecendo na lista atualizada de **Usuários e grupos**.

## <a name="more-information"></a>Mais informações

- [Como: adicionar funções de aplicativo em seu aplicativo](./howto-add-app-roles-in-azure-ad-apps.md)
- [Adicionar autorização usando funções de aplicativo e declarações de funções a um aplicativo Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Usando grupos de segurança e funções de aplicativo em seus aplicativos (vídeo)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, agora com declarações de grupo e funções de aplicativo](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifesto de aplicativo do Azure Active Directory](./reference-app-manifest.md)
