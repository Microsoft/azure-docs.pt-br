---
title: Nova experiência de Registros de aplicativo no Azure AD B2C
description: Uma introdução à nova experiência de registro de aplicativo no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 430da23986fc36a0e94c049512ef716aff1fed5c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660243"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>A nova experiência de Registros de aplicativo para Azure Active Directory B2C

A nova experiência de **[registros de aplicativo](https://aka.ms/b2cappregistrations)** para Azure Active Directory B2C (Azure ad B2C) já está disponível para o público em geral. Se você estiver mais familiarizado com a experiência de **aplicativos** para registrar aplicativos para Azure ad B2C, mencionado aqui como a "experiência herdada", este guia ajudará você a começar a usar a nova experiência.

## <a name="overview"></a>Visão geral
Anteriormente, era necessário gerenciar seus Azure AD B2C aplicativos voltados para o consumidor separadamente do restante dos seus aplicativos usando a experiência herdada. Isso significava experiências de criação de aplicativo diferentes em locais diferentes no Azure.

A nova experiência mostra todos os registros de aplicativo Azure AD B2C e registros de aplicativo do Azure AD em um único local e fornece uma maneira consistente de gerenciá-los. De criar um aplicativo voltado para o cliente para gerenciar um aplicativo com permissões de Microsoft Graph para gerenciamento de recursos, você só precisa aprender uma maneira de fazer coisas.

Você pode acessar a nova experiência navegando até **registros de aplicativo** em um locatário Azure ad B2C tanto do **Azure ad B2C** quanto dos serviços **Azure Active Directory** no portal do Azure.

A experiência de Registros de aplicativo Azure AD B2C é baseada na experiência geral de [registro de aplicativo](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) para qualquer locatário do Azure AD, mas é adaptada para locatários Azure ad B2C.

## <a name="whats-not-changing"></a>O que não está mudando?
- Seus aplicativos e configurações relacionadas podem ser encontrados no estado em que se encontram na nova experiência. Você não precisa registrar os aplicativos novamente e os usuários de seus aplicativos não precisarão entrar novamente.

> [!NOTE]
> Para exibir todos os aplicativos criados anteriormente, navegue até a folha **registros de aplicativo** e selecione a guia **todos os aplicativos** . Isso exibirá os aplicativos criados na experiência herdada, a nova experiência e os criados no serviço do Azure AD.

## <a name="key-new-features"></a>Principais recursos novos

-   Uma **lista de aplicativos unificados** mostra todos os aplicativos que se autenticam com o Azure ad B2C e o Azure AD em um único local conveniente. Além disso, você pode aproveitar os recursos já disponíveis para aplicativos do Azure AD, incluindo a data de **criação** , os **certificados &** o status dos segredos, a barra de pesquisa e muito mais.

-   O **registro de aplicativo combinado** permite que você registre rapidamente um aplicativo, seja um aplicativo voltado para o cliente ou um aplicativo para acessar Microsoft Graph.

- O painel **pontos de extremidade** permite identificar rapidamente os pontos de extremidade relevantes para seu cenário, incluindo a configuração do OpenID Connect, os metadados SAML, a API do Microsoft Graph e os pontos de extremidade do fluxo de usuário do [OAuth 2,0](tokens-overview.md#endpoints).

- **As permissões de API** e **expõem uma API fornecem um** escopo, permissão e gerenciamento de consentimento mais amplos. Agora, você também pode atribuir permissões do MS Graph e do Azure AD Graph a um aplicativo.

-   Os **proprietários** e o **manifesto** agora estão disponíveis para aplicativos que se autenticam com Azure ad B2C. Você pode adicionar proprietários para seus registros e editar diretamente as propriedades do aplicativo [usando o editor de manifesto](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Novos tipos de conta com suporte

Na nova experiência, você seleciona um tipo de conta de suporte das seguintes opções:
- Contas somente neste diretório organizacional
- Contas em qualquer diretório organizacional (qualquer diretório do Azure AD – multilocatário)
- Contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)

Para entender os tipos de conta diferentes, selecione **ajude-me a escolher** na experiência de criação.

Na experiência herdada, os aplicativos eram sempre criados como aplicativos voltados para o cliente. Para esses aplicativos, o tipo de conta é definido como **contas em qualquer provedor de identidade ou diretório organizacional (para autenticar usuários com fluxos de usuário)**.
> [!NOTE]
> Essa opção é necessária para poder executar Azure AD B2C fluxos de usuário para autenticar usuários para este aplicativo. Saiba [como registrar um aplicativo para uso com fluxos de usuário.](tutorial-register-applications.md)

Você também pode usar essa opção para usar Azure AD B2C como um provedor de serviços SAML. [Saiba mais](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>Aplicativos para cenários de DevOps

Você pode usar os outros tipos de conta para criar um aplicativo para gerenciar seus cenários de DevOps, como usar Microsoft Graph para carregar políticas de estrutura de experiência de identidade ou provisionar usuários. Saiba [como registrar um aplicativo Microsoft Graph para gerenciar Azure ad B2C recursos](microsoft-graph-get-started.md).

Talvez você não veja todas as permissões de Microsoft Graph, pois muitas dessas permissões não se aplicam aos usuários do consumidor do Azure B2C. [Leia mais sobre como gerenciar usuários usando Microsoft Graph](microsoft-graph-operations.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Consentimento de administrador e offline_access + escopos de OpenID
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

O escopo de **OpenID** é necessário para que Azure ad B2C possa conectar usuários a um aplicativo. O escopo de **offline_access** é necessário para emitir tokens de atualização para um usuário. Esses escopos foram adicionados anteriormente e recebem consentimento de administrador por padrão. Agora, você pode adicionar facilmente permissões para esses escopos durante o processo de criação, garantindo que a opção **conceder administrador consentimento para OpenID e permissões de offline_access** esteja selecionada. Caso contrário, as permissões de Microsoft Graph podem ser adicionadas com consentimento de administrador nas configurações de **permissões de API** para um aplicativo existente.

Saiba mais sobre [permissões e consentimento](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plataformas/autenticação: URLs de resposta/URIs de redirecionamento
Na experiência herdada, os vários tipos de plataforma foram gerenciados em **Propriedades** como URLs de resposta para aplicativos Web/APIs e URI de redirecionamento para clientes nativos. "Clientes nativos" também são conhecidos como "clientes públicos" e incluem aplicativos para iOS, macOS, Android e outros tipos de aplicativos móveis e de desktop.

Na nova experiência, as URLs de resposta e os URIs de redirecionamento são chamados de URIs de redirecionamento e podem ser encontrados na seção **autenticação** de um aplicativo. Os Registros de aplicativo não estão limitados a ser um aplicativo Web ou nativo. Você pode usar o mesmo registro de aplicativo para todos esses tipos de plataforma registrando os respectivos URIs de redirecionamento.

Os URIs de redirecionamento devem ser associados a um tipo de aplicativo, Web ou público (móvel e área de trabalho). [Saiba mais sobre URIs de redirecionamento](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

As plataformas **Ios/MacOS** e **Android** são um tipo de cliente público. Eles fornecem uma maneira fácil de configurar aplicativos iOS/macOS ou Android com URIs de redirecionamento correspondentes para uso com MSAL. Saiba mais sobre [as opções de configuração do aplicativo](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Certificados de aplicativo & segredos

Na nova experiência, em vez de **chaves**, você usa a folha **certificados & segredos** para gerenciar certificados e segredos. Os certificados & segredos permitem que os aplicativos se identifiquem para o serviço de autenticação ao receber tokens em um local endereçável da Web (usando um esquema HTTPS). É recomendável usar um certificado em vez de um segredo do cliente para cenários de credencial do cliente ao autenticar no Azure AD. Os certificados não podem ser usados para autenticação no Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Recursos não aplicáveis em locatários Azure AD B2C
Os seguintes recursos de registro de aplicativo do Azure AD não são aplicáveis ou estão disponíveis em locatários Azure AD B2C:
- **Funções e administradores** – não disponíveis no momento para Azure ad B2C.
- **Identidade visual** – a personalização da interface do usuário/UX é configurada na experiência de **identidade visual da empresa** ou como parte de um fluxo de usuário. Saiba como [Personalizar a interface do usuário no Azure Active Directory B2C](customize-ui-with-html.md).
- **Verificação de domínio do Publicador** -seu aplicativo está registrado em *. onmicrosoft.com*, que não é um domínio verificado. Além disso, o domínio do Publicador é usado principalmente para conceder o consentimento do usuário, que não se aplica a aplicativos Azure AD B2C para autenticação de usuário. [Saiba mais sobre o domínio do Publicador](../active-directory/develop/howto-configure-publisher-domain.md).
- **Configuração de token** -o token é configurado como parte de um fluxo de usuário em vez de um aplicativo.
- A experiência de **guias de início rápido** não está disponível atualmente para locatários Azure ad B2C.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Limitações
A nova experiência tem as seguintes limitações:
- Neste momento, Azure AD B2C não diferencia a capacidade de emitir tokens de acesso ou de ID para fluxos implícitos; os dois tipos de tokens estarão disponíveis para o fluxo de concessão implícito se a opção de **tokens de ID** estiver selecionada na folha **autenticação** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Não há suporte para a alteração do valor de contas com suporte na interface do usuário. Você precisará usar o manifesto do aplicativo, a menos que esteja alternando entre o Azure AD single-locatário e multilocatário.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar a nova experiência de registro de aplicativo:
* Saiba [como registrar um aplicativo Web](tutorial-register-applications.md).
* Saiba [como registrar uma API da Web](add-web-api-application.md).
* Saiba [como registrar um aplicativo cliente nativo](add-native-application.md).
* Saiba [como registrar um aplicativo Microsoft Graph para gerenciar Azure ad B2C recursos](microsoft-graph-get-started.md).
* Saiba [como usar Azure ad B2C como um provedor de serviços SAML.](identity-provider-adfs.md)
* Saiba mais sobre os [tipos de aplicativos](application-types.md).