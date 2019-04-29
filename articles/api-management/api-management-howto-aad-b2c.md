---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory B2C - Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como autorizar usuários que usam o Azure Active Directory B2C no Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 644cc2a4175043b523d53b39f17483c6f3acfe96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558297"
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
   > Se você ainda não tiver criado uma instância do serviço de Gerenciamento de API, veja [Criar uma instância do serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].

2. Sob **identidades**. Clique em **+Adicionar**, na parte superior.

   O painel **Adicionar provedor de identidade** aparece à direita. Escolha **Azure Active Directory B2C**.
    
   ![Adicione AAD B2C como o provedor de identidade][api-management-howto-add-b2c-identity-provider]

3. Copie a **URL de Redirecionamento**.

   ![URL de redirecionamento do provedor de identidade do AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Em uma nova guia, acesse seu locatário do Azure Active Directory B2C no portal do Azure e abra a folha **Aplicativos**.

   ![Registrar um novo aplicativo 1][api-management-howto-aad-b2c-portal-menu]

5. Clique o **adicionar** botão para criar um novo aplicativo do Azure Active Directory B2C.

   ![Registrar um novo aplicativo 2][api-management-howto-aad-b2c-add-button]

6. Na folha **Novo aplicativo**, insira um nome para o aplicativo. Escolha **Sim** em **API da Web do aplicativo Web**e escolha **Sim** em **permitir fluxo implícito**. Em seguida, cole a **URL de redirecionamento** copiada na etapa 3 na caixa de texto **URL de resposta**.

   ![Registrar um novo aplicativo 3][api-management-howto-aad-b2c-app-details]

7. Selecione o botão **Criar**. Quando o aplicativo é criado, ele aparece no **aplicativos** folha. Clique no nome do aplicativo para ver seus detalhes.

   ![Registrar um novo aplicativo 4][api-management-howto-aad-b2c-app-created]

8. Do **propriedades** folha, copie o **ID do aplicativo** na área de transferência.

   ![ID do aplicativo 1][api-management-howto-aad-b2c-app-id]

9. Volte para o painel **Adicionar provedor de identidade** do Gerenciamento de API e cole a ID na caixa de texto **ID do cliente**.
    
10. Volte para o registro do aplicativo B2C, clique no botão **Chaves** e clique em **Gerar chave**. Clique em **Salvar** para salvar a configuração e exibir a **chave de Aplicativo**. Copie a chave para a área de transferência.

    ![Chave do aplicativo 1][api-management-howto-aad-b2c-app-key]

11. Volte para o painel **Adicionar provedor de identidade** do Gerenciamento de API e cole a chave na caixa de texto **Segredo do cliente**.
    
12. Especifique o nome de domínio do locatário no Azure Active Directory B2C **entrar no locatário**.

13. O **autoridade** campo permitem que você controle a URL de logon do Azure AD B2C para usar. Defina o valor como **< your_b2c_tenant_name >. b2clogin.com**.

14. Especifique a **Política de inscrição** e a **Política de entrada** das políticas do locatário B2C. Opcionalmente, você também pode fornecer **Política de Edição de Perfil** e **Política de Redefinição de Senha**.

15. Depois que a configuração desejada for especificada, clique em **Salvar**.

    Depois que as alterações forem salvas, os desenvolvedores poderão criar novas contas e entrar portal do desenvolvedor usando Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Inscrever-se para uma conta de desenvolvedor usando o Azure Active Directory B2C

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
*  [Azure Active Directory B2C: Estrutura de política extensível]
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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Visão geral do Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Como autorizar contas de desenvolvedor usando o Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Estrutura de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Usar a conta da Microsoft como provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Usar a conta do Google como provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Usar a conta do Facebook como provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Usar a conta do Linkedin como provedor de identidade no Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
