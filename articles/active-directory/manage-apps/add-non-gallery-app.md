---
title: Adicionar um aplicativo não Galeria-plataforma de identidade da Microsoft | Microsoft Docs
description: Adicione um aplicativo que não seja da galeria ao seu locatário do Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477255"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Adicionar um aplicativo não listado (não Galeria) à sua organização do Azure AD

Além das opções na [Galeria de aplicativos do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), você tem a opção de adicionar um aplicativo inexistente na **Galeria**. Você pode adicionar qualquer aplicativo que já existe em sua organização ou qualquer aplicativo de terceiros de um fornecedor que ainda não faz parte da galeria do Azure AD. Dependendo do seu [contrato de licença](https://azure.microsoft.com/pricing/details/active-directory/), os seguintes recursos estão disponíveis:

- Integração de autoatendimento de qualquer aplicativo que ofereça suporte a provedores de identidade [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (iniciado pelo SP ou pelo IDP)
- Integração de autoatendimento de qualquer aplicativo Web que tenha uma página de entrada baseada em HTML usando o [SSO baseado em senha](what-is-single-sign-on.md#password-based-sso)
- Conexão de autoatendimento de aplicativos que usam o [sistema para o protocolo scim (gerenciamento de identidade entre domínios) para provisionamento de usuário](use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links aos aplicativos no [inicializador de aplicativos do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [Painel de acesso do Azure AD](what-is-single-sign-on.md#linked-sign-on)

Este artigo descreve como adicionar um aplicativo que não seja da galeria a **aplicativos empresariais** no portal do Azure sem escrever código. Se, em vez disso, você estiver procurando orientação para desenvolvedores sobre como integrar aplicativos personalizados ao Azure AD, consulte [cenários de autenticação do Azure ad](../develop/authentication-scenarios.md). Ao desenvolver um aplicativo que usa um protocolo moderno como [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar usuários, você pode registrá-lo com a plataforma de identidade da Microsoft usando a experiência de [registros de aplicativo](../develop/quickstart-register-app.md) no portal do Azure.

## <a name="add-a-non-gallery-application"></a>Adicionar um aplicativo inexistente na galeria

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com/) usando sua conta de administrador da plataforma de identidade da Microsoft.
1. Selecione **aplicativos** > empresariais**novo aplicativo**.
2. (Opcional, mas recomendado) Na caixa de pesquisa **Adicionar da Galeria** , insira o nome de exibição do aplicativo. Se o aplicativo aparecer nos resultados da pesquisa, selecione-o e ignore o restante deste procedimento.
3. Selecione **aplicativo**inexistente na galeria. A página **Adicionar seu próprio aplicativo** é exibida.

   ![Adicionar aplicativo](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Insira o nome de exibição para o novo aplicativo.
6. Selecione **Adicionar**. A página **visão geral** do aplicativo é aberta.

## <a name="configure-user-sign-in-properties"></a>Configurar propriedades de logon do usuário

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-application-portal/edit-properties.png)

1. Defina as opções a seguir para determinar como os usuários atribuídos ou não atribuídos ao aplicativo poderão entrar no aplicativo e se um usuário puder ver o aplicativo no painel de acesso.

    - **Habilitado para que os usuários entrem** determina se os usuários atribuídos ao aplicativo podem entrar nele.
    - A **atribuição de usuário obrigatória** determina se os usuários não atribuídos ao aplicativo podem entrar nele.
    - **Visível para usuário** determina se os usuários atribuídos a um aplicativo podem vê-lo no painel de acesso e no inicializador do O365.

      Comportamento de usuários **atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência do usuário atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário obrigatória? | Visível para os usuários? | Os usuários atribuídos podem entrar? | Os usuários atribuídos podem ver o aplicativo?* |
       | sim | sim | sim | sim | sim  |
       | sim | sim | não  | sim | não   |
       | sim | não  | sim | sim | sim  |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

      Comportamento de usuários **não atribuídos**:

       | Configurações de propriedade do aplicativo | | | Experiência de usuário não atribuído | |
       |---|---|---|---|---|
       | Habilitado para os usuários entrarem? | Atribuição de usuário requerida? | Visível para os usuários? | Os usuários não atribuídos podem entrar? | Os usuários não atribuídos podem ver o aplicativo?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O usuário pode ver o aplicativo no painel de acesso e no iniciador de aplicativos do Office 365?

1. Para usar um logotipo personalizado, crie um logotipo que seja 215 por 215 pixels e salve-o no formato PNG. Em seguida, navegue até o logotipo e carregue-o.

    ![Alterar o logotipo](media/add-application-portal/change-logo.png)

1. Quando terminar, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você adicionou o aplicativo à sua organização do Azure AD, [escolha um método de logon único](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que você deseja usar e consulte o artigo apropriado abaixo:

- [Configurar o logon único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o logon único com senha](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar logon vinculado](configure-linked-sign-on.md)
