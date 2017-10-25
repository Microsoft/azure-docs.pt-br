---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory B2C - Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como autorizar usuários que usam o Azure Active Directory B2C no Gerenciamento de API."
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: d99dbbd834cb8f067b88b765ccddcd7f4eb44a1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
> [!WARNING]
> A integração do Azure Active Directory B2C está disponível somente nas camadas [Desenvolvedor e Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o Azure Active Directory B2C no Gerenciamento de API do Azure
## <a name="overview"></a>Visão geral
O Azure Active Directory B2C é uma solução de gerenciamento de identidade na nuvem para os seus aplicativos móveis e Web voltados para o consumidor. Você pode usá-lo para gerenciar o acesso ao portal do desenvolvedor. Este guia mostra a configuração necessária no seu serviço de gerenciamento de API para integrar com o Azure Active Directory B2C. Para saber mais sobre como habilitar o acesso ao portal do desenvolvedor usando o Azure Active Directory clássico, veja [Como autorizar contas de desenvolvedor usando o Azure Active Directory].

> [!NOTE]
> Para completar as etapas deste guia, primeiro tenha um locatário do Azure Active Directory B2C no qual criar um aplicativo. Além disso, você precisa ter políticas de entrada e de inscrição prontas. Para saber mais, veja [Visão geral do Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizar contas de desenvolvedor usando o Azure Active Directory B2C

1. Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

   ![Portal do editor][api-management-management-console]

   > [!NOTE]
   > Se você ainda não tiver criado uma instância do serviço de Gerenciamento de API, veja [Criar uma instância do serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].

2. No menu **Gerenciamento de API**, clique em **Segurança**. Na guia **Identidades**, escolha **Azure Active Directory B2C**.

  ![Identidades externas 1][api-management-howto-aad-b2c-security-tab]

3. Anote a **URL de Redirecionamento** e alterne para seu Azure Active Directory B2C no Portal do Azure.

  ![Identidades externas 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Clique no botão **Aplicativos**.

  ![Registrar um novo aplicativo 1][api-management-howto-aad-b2c-portal-menu]

5. Clique o **adicionar** botão para criar um novo aplicativo do Azure Active Directory B2C.

  ![Registrar um novo aplicativo 2][api-management-howto-aad-b2c-add-button]

6. Na folha **Novo aplicativo**, insira um nome para o aplicativo. Escolha **Sim** em **API da Web do aplicativo Web**e escolha **Sim** em **permitir fluxo implícito**. Em seguida, copie a **URL de redirecionamento** da seção **Azure Active Directory B2C** da guia **Identidades** no portal do editor e cole-a na caixa de texto **URL de resposta**.

  ![Registrar um novo aplicativo 3][api-management-howto-aad-b2c-app-details]

7. Selecione o botão **Criar** . Quando o aplicativo é criado, ele aparece no **aplicativos** folha. Clique no nome do aplicativo para ver seus detalhes.

  ![Registrar um novo aplicativo 4][api-management-howto-aad-b2c-app-created]

8. Do **propriedades** folha, copie o **ID do aplicativo** na área de transferência.

  ![ID do aplicativo 1][api-management-howto-aad-b2c-app-id]

9. Voltar ao portal do editor e cole a ID na caixa de texto **ID do Cliente**.

  ![ID do aplicativo 2][api-management-howto-aad-b2c-client-id]

10. Alterne para o Portal do Azure, clique no botão **Chaves** e clique em **Gerar chave**. Clique em **Salvar** para salvar a configuração e exibir a **chave de Aplicativo**. Copie a chave para a área de transferência.

  ![Chave do aplicativo 1][api-management-howto-aad-b2c-app-key]

11. Voltar ao portal do editor e cole a chave na caixa de texto **Segredo do Cliente** .

  ![Chave do aplicativo 2][api-management-howto-aad-b2c-client-secret]

12. Especifique o campo **Locatário Permitido** com o nome de domínio do locatário do Azure Active Directory B2C.

  ![Locatário permitido][api-management-howto-aad-b2c-allowed-tenant]

13. Especifique o **Política de Inscrição** e **Política de Entrada**. Opcionalmente, você também pode fornecer **Política de Edição de Perfil** e **Política de Redefinição de Senha**.

  ![Políticas][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Para saber mais sobre políticas, veja [Azure Active Directory B2C: estrutura de política extensível].

14. Depois que a configuração desejada for especificada, clique em **Salvar**.

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
*  [Azure Active Directory B2C: estrutura de política extensível]
*  [Usar a conta da Microsoft como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Google como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Linkedin como provedor de identidade no Azure Active Directory B2C]
*  [Usar a conta do Facebook como provedor de identidade no Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
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
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Visão geral do Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Como autorizar contas de desenvolvedor usando o Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: estrutura de política extensível]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
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
