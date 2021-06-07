---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory B2C
titleSuffix: Azure API Management
description: Saiba como autorizar usuários que usam o Azure Active Directory B2C no Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013525"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o Azure Active Directory B2C no Gerenciamento de API do Azure

## <a name="overview"></a>Visão geral

O Azure Active Directory B2C é uma solução de gerenciamento de identidade na nuvem para os seus aplicativos móveis e Web voltados para o consumidor. Você pode usá-lo para gerenciar o acesso ao portal do desenvolvedor. Este guia mostra a configuração necessária no seu serviço de gerenciamento de API para integrar com o Azure Active Directory B2C. Para saber mais sobre como habilitar o acesso ao portal do desenvolvedor usando o Azure Active Directory clássico, veja [Como autorizar contas de desenvolvedor usando o Azure Active Directory].

> [!NOTE]
> Para completar as etapas deste guia, primeiro tenha um locatário do Azure Active Directory B2C no qual criar um aplicativo. Além disso, você precisa ter políticas de entrada e de inscrição prontas. Para saber mais, veja [Visão geral do Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de desenvolvedor usando o Azure Active Directory B2C

1. Para começar, entre no [portal do Azure](https://portal.azure.com) e localize a instância de Gerenciamento de API.

   > [!NOTE]
   > Se você ainda não criou uma instância do serviço de gerenciamento de API, consulte [criar uma instância do serviço de gerenciamento de API][Create an API Management service instance] no [tutorial introdução ao gerenciamento de API do Azure][Get started with Azure API Management].

1. Em **identidades**. Clique em **+Adicionar**, na parte superior.

   O painel **Adicionar provedor de identidade** aparece à direita. Escolha **Azure Active Directory B2C**.
    
   ![Adicione AAD B2C como o provedor de identidade][api-management-howto-add-b2c-identity-provider]

1. Copie a **URL de redirecionamento**.

   ![URL de redirecionamento do provedor de identidade do AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Em uma nova guia, acesse seu locatário do Azure Active Directory B2C no portal do Azure e abra a folha **Aplicativos**.

   ![Registrar um novo aplicativo 1][api-management-howto-aad-b2c-portal-menu]

1. Clique o **adicionar** botão para criar um novo aplicativo do Azure Active Directory B2C.

   ![Registrar um novo aplicativo 2][api-management-howto-aad-b2c-add-button]

1. Na folha **Novo aplicativo**, insira um nome para o aplicativo. Escolha **Sim** em **API da Web do aplicativo Web** e escolha **Sim** em **permitir fluxo implícito**. Em seguida, cole a **URL de redirecionamento** copiada na etapa 3 na caixa de texto **URL de resposta**.

   ![Registrar um novo aplicativo 3][api-management-howto-aad-b2c-app-details]

1. Se você estiver usando o novo portal do desenvolvedor (não o portal do desenvolvedor herdado), inclua o **nome**, o **sobrenome** e a **ID de objeto do usuário** em declarações do aplicativo.

    ![Declarações do aplicativo](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Selecione o botão **Criar**. Quando o aplicativo é criado, ele aparece no **aplicativos** folha. Clique no nome do aplicativo para ver seus detalhes.

   ![Registrar um novo aplicativo 4][api-management-howto-aad-b2c-app-created]

1. Do **propriedades** folha, copie o **ID do aplicativo** na área de transferência.

   ![ID do aplicativo 1][api-management-howto-aad-b2c-app-id]

1. Volte para o painel **Adicionar provedor de identidade** do Gerenciamento de API e cole a ID na caixa de texto **ID do cliente**.
    
1.  Volte para o registro do aplicativo B2C, clique no botão **Chaves** e clique em **Gerar chave**. Clique em **Salvar** para salvar a configuração e exibir a **chave de Aplicativo**. Copie a chave para a área de transferência.

    ![Chave do aplicativo 1][api-management-howto-aad-b2c-app-key]

1.  Volte para o painel **Adicionar provedor de identidade** do Gerenciamento de API e cole a chave na caixa de texto **Segredo do cliente**.
    
1.  Especifique o nome de domínio do locatário de Azure Active Directory B2C no **locatário de entrada**.

1.  O campo **autoridade** permite que você controle a URL de logon do Azure ad B2C a ser usada. Defina o valor para **<your_b2c_tenant_name>. b2clogin.com**.

1. Especifique a **Política de inscrição** e a **Política de entrada** das políticas do locatário B2C. Opcionalmente, você também pode fornecer **Política de Edição de Perfil** e **Política de Redefinição de Senha**.

1. Depois que a configuração desejada for especificada, clique em **Salvar**.

    Depois que as alterações forem salvas, os desenvolvedores poderão criar novas contas e entrar portal do desenvolvedor usando Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal do desenvolvedor-adicionar Azure AD B2C autenticação de conta

No portal do desenvolvedor, entre com AAD B2C é possível com o botão de **entrada: widget OAuth** . O widget já está incluído na página de entrada do conteúdo do portal do desenvolvedor padrão.

Embora uma nova conta seja criada automaticamente sempre que um novo usuário entrar com AAD B2C, você pode considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição:** o widget OAuth representa um formulário usado para se inscrever com o OAuth.

> [!IMPORTANT]
> Você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações do AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Portal do desenvolvedor herdado-como se inscrever com Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Para se inscrever para uma conta de desenvolvedor usando o Azure Active Directory B2C, abra uma nova janela do navegador e vá para o portal do desenvolvedor. Clique no botão **Inscrever**.

   ![Portal do desenvolvedor 1][api-management-howto-aad-b2c-dev-portal]

2. Opte por inscrever-se no **Azure Active Directory B2C**.

   ![Portal do desenvolvedor 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Você será redirecionado para a política de inscrição configurada na seção anterior. Escolha inscrever-se usando seu endereço de email ou uma de suas contas sociais existentes.

   > [!NOTE]
   > Se o Azure Active Directory B2C for a única opção habilitada no **identidades** guia no portal do Editor, você será redirecionado para a política de inscrição diretamente.

   ![Portal do desenvolvedor][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Quando a inscrição for concluída, você será redirecionado para o portal do desenvolvedor. Agora você está conectado no portal do desenvolvedor para a instância de serviço de Gerenciamento de API.

    ![Registro concluído][api-management-registration-complete]

## <a name="next-steps"></a>Próximas etapas

*  [Visão geral do Azure Active Directory B2C]
*  [Azure Active Directory B2C: estrutura de política extensível]
*  [Usar a conta da Microsoft como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Google como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Linkedin como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Facebook como provedor de identidade no Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Visão geral do Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[Como autorizar contas de desenvolvedor usando o Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: estrutura de política extensível]: ../active-directory-b2c/user-flow-overview.md
[Usar a conta da Microsoft como provedor de identidade no Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Usar a conta do Google como provedor de identidade no Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Usar a conta do Facebook como provedor de identidade no Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Usar a conta do Linkedin como provedor de identidade no Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
