---
title: Configurar um aplicativo para acessar APIs Web – plataforma de identidade da Microsoft
description: Saiba como configurar um aplicativo registrado na Microsoft Identity Platform para incluir URIs de redirecionamento, credenciais ou permissões para acessar as APIs Web.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e8b0e17248dff3c53b96fd240a7566f09b22fae
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482691"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Início Rápido: Configure um aplicativo cliente para acessar APIs Web

Para que um aplicativo cliente Web/confidencial possa participar de um fluxo de concessão de autorização que requer autenticação (e obter um token de acesso), ele deverá estabelecer credenciais seguras. O método de autenticação padrão com suporte no portal do Azure é a ID do Cliente + chave secreta.

Além disso, antes que um cliente possa acessar uma API Web exposta por um aplicativo de recurso (como a API do Microsoft Graph), a estrutura de autorização garante que o cliente obtenha a concessão de permissão necessária com base nas permissões solicitadas. Por padrão, todos os aplicativos podem escolher permissões da API do Microsoft Graph. A [permissão "Entrar e ler perfil do usuário" da API do Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) está selecionada por padrão. Você pode selecionar entre [dois tipos de permissões](developer-glossary.md#permissions) para cada API Web desejada:

* **Permissões de aplicativo**: o aplicativo cliente precisa acessar a API Web diretamente por si só (sem contexto de usuário). Esse tipo de permissão requer o consentimento do administrador e também não está disponível para aplicativos clientes públicos (área de trabalho e móveis).
* **Permissões delegadas**: o aplicativo cliente precisa acessar a API Web como o usuário conectado, mas com acesso limitado pela permissão selecionada. Esse tipo de permissão pode ser concedido por um usuário, a menos que a permissão exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a um aplicativo não dá automaticamente consentimento aos usuários no locatário. Os usuários devem consentir manualmente para as permissões delegadas adicionadas em tempo de execução, a menos que o administrador dê consentimento em nome de todos os usuários.

Neste início rápido, mostraremos como configurar seu aplicativo para:

* [Adicionar URIs de redirecionamento ao aplicativo](#add-redirect-uris-to-your-application)
* [Adicionar credenciais ao aplicativo Web](#add-credentials-to-your-web-application)
* [Adicionar permissões para acessar APIs Web](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, atenda aos seguintes pré-requisitos:

* Leia sobre [permissões e consentimentos](v2-permissions-and-consent.md), o que é importante saber na hora de criar aplicativos que precisam ser usados por outros usuários ou aplicativos.
* Obtenha um locatário com aplicativos registrados.
  * Se você não tiver aplicativos registrados, [saiba como registrar aplicativos com a Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Entrar no portal do Azure e selecionar o aplicativo

Antes de configurar o aplicativo, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, **Registros de aplicativo**.
1. Encontre e selecione o aplicativo que você deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** ou a página de registro principal.
1. Siga as etapas para configurar seu aplicativo e acessar APIs Web: 
    * [Adicionar URIs de redirecionamento ao aplicativo](#add-redirect-uris-to-your-application)
    * [Adicionar credenciais ao aplicativo Web](#add-credentials-to-your-web-application)
    * [Adicionar permissões para acessar APIs Web](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Adicionar URIs de redirecionamento ao aplicativo

[![Adicionar URIs de redirecionamento personalizados para aplicativos clientes públicos e Web](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Para adicionar um URI de redirecionamento ao seu aplicativo:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Autenticação**.

1. Para adicionar um URI de redirecionamento personalizado para aplicativos clientes públicos e Web, siga estas etapas:

   1. Localize a seção **URI de redirecionamento**.
   1. Selecione o tipo de aplicativo que você está criando, **Web** ou **Cliente público (móvel e área de trabalho)** .
   1. Insira o URI de redirecionamento para seu aplicativo.
      * Para aplicativos Web, informe a URL base do aplicativo. Por exemplo, `http://localhost:31544` pode ser uma URL para um aplicativo Web em execução no seu computador local. Os usuários usarão essa URL para entrar em um aplicativo cliente Web.
      * Para aplicativos públicos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para o aplicativo, por exemplo, https://MyFirstApp.

1. Para escolher os URIs de redirecionamento sugeridos para clientes públicos (móvel, área de trabalho), siga estas etapas:

    1. Encontre a seção **URIs de redirecionamento sugeridos para clientes públicos (móvel, área de trabalho)** sugerida.
    1. Selecione os URIs de redirecionamento apropriados para seu aplicativo usando as caixas de seleção.

## <a name="add-credentials-to-your-web-application"></a>Adicionar credenciais ao aplicativo Web

[![Adicionar certificados e segredos do cliente](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Para adicionar uma credencial ao aplicativo Web:

1. Na página **Visão Geral**, selecione a seção **Certificados e segredos**.

1. Para adicionar um certificado, siga estas etapas:

    1. Selecione **Carregar certificado**.
    1. Selecione o arquivo que você gostaria de carregar. Ele deve ser um dos seguintes tipos de arquivo: .cer, .pem, .crt.
    1. Selecione **Adicionar**.

1. Para adicionar um segredo do cliente, siga estas etapas:

    1. Selecione **Novo segredo do cliente**.
    1. Adicione uma descrição para o segredo do cliente.
    1. Selecione uma duração.
    1. Selecione **Adicionar**.

> [!NOTE]
> Depois que você salvar as alterações de configuração, a coluna mais à direita conterá o valor do segredo do cliente. **Copie o valor** para uso no código do aplicativo cliente, já que ele não ficará acessível depois que você sair da página.

## <a name="add-permissions-to-access-web-apis"></a>Adicionar permissões para acessar APIs Web

[![Mostra a tela em que você pode adicionar as permissões de API](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Para adicionar permissões de acesso às APIs do recurso no cliente:

1. Na página **Visão Geral** do aplicativo, selecione **Permissões de API**.
1. Selecione o botão **Adicionar uma permissão**.
1. Por padrão, o modo de exibição permite que você selecione das **APIs da Microsoft**. Selecione a seção de APIs desejada:
    * **APIs da Microsoft**: permite que você selecione permissões para APIs da Microsoft, como o Microsoft Graph.
    * **APIs que minha organização usa**: permite que você selecione as permissões para as APIs que foram expostas pela sua organização, ou para APIs com que sua organização esteja integrada.
    * **Minhas APIs**: permite que você selecione as permissões para as APIs expostas.
1. Depois de selecionar as APIs, você verá a página **Solicitar Permissões de API**. Se a API expõe permissões delegadas e de aplicativo, selecione o tipo de permissão de que seu aplicativo precisa.
1. Quando terminar, selecione **Adicionar permissões**. Você voltará para a página **Permissões de API**, onde as permissões foram salvas e adicionadas à tabela.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre esses outros inícios rápidos de gerenciamento de aplicativos relacionados para aplicativos:

* [Registrar um aplicativo com a Microsoft Identity Platform](quickstart-register-app.md)
* [Configurar um aplicativo para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Modificar as contas que têm suporte por um aplicativo](quickstart-modify-supported-accounts.md)
* [Remover um aplicativo registrado na Microsoft Identity Platform](quickstart-remove-app.md)

Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
