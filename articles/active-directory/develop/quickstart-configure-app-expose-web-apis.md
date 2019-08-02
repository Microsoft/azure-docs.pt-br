---
title: Configurar um aplicativo para expor APIs Web – plataforma de identidade da Microsoft
description: Saiba como configurar um aplicativo para expor uma nova permissão ou escopo e função a fim de disponibilizar o aplicativo para aplicativos cliente.
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
ms.openlocfilehash: f770539e2f60a960a52a877dd4eaa79c60a1af4a
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482667"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>Início Rápido: Configurar um aplicativo para expor APIs Web

Você pode desenvolver uma API Web e disponibilizá-la aos aplicativos cliente expondo [permissões/escopos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) de acesso. Uma API Web corretamente configurada é disponibilizada assim como outras APIs Web da Microsoft, incluindo a API do Graph e as APIs do Office 365.

Neste início rápido, você aprenderá a configurar um aplicativo para expor um novo escopo e disponibilizá-lo para aplicativos cliente.

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
1. Escolha qual método você deseja usar, a interface do usuário ou o aplicativo de manifesto, para expor um novo escopo:
    * [Expor um novo escopo pela interface do usuário](#expose-a-new-scope-through-the-ui)
    * [Expor um novo escopo ou função pelo manifesto do aplicativo](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Expor um novo escopo pela interface do usuário

[![Mostra como expor uma API usando a interface do usuário](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Para expor um novo escopo pela interface do usuário:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Expor uma API**.

1. Selecione **Adicionar um escopo**.

1. Se você não tiver definido um **URI da ID do aplicativo**, verá um prompt para inserir um. Insira o URI da ID do aplicativo ou use o fornecido e selecione **Salvar e continuar**.

1. Quando a página **Adicionar um escopo** for exibida, insira as informações do seu escopo:

    | Campo | DESCRIÇÃO |
    |-------|-------------|
    | **Nome do Escopo** | Insira um nome relevante para seu escopo.<br><br>Por exemplo, `Employees.Read.All`. |
    | **Quem pode consentir** | Selecione se esse escopo pode ser consentido por usuários, ou se é necessário ter o consentimento do administrador. Selecione **Somente administradores** para permissões com privilégios mais elevados. |
    | **Nome de exibição de consentimento do administrador** | Insira uma descrição significativa para o escopo, que os administradores verão.<br><br>Por exemplo, `Read-only access to Employee records` |
    | **Descrição do consentimento do administrador** | Insira uma descrição significativa para o escopo, que os administradores verão.<br><br>Por exemplo, `Allow the application to have read-only access to all Employee data.` |

    Se os usuários podem consentir com seu escopo, adicione também valores para os seguintes campos:

    | Campo | DESCRIÇÃO |
    |-------|-------------|
    | **Nome para exibição do consentimento do usuário** | Insira um nome significativo para seu escopo, que os usuários verão.<br><br>Por exemplo, `Read-only access to your Employee records` |
    | **Descrição de consentimento do usuário** | Insira uma descrição significativa para o escopo, que os usuários verão.<br><br>Por exemplo, `Allow the application to have read-only access to your Employee data.` |

1. Defina o **Estado** e selecione **Adicionar escopo** quando você terminar.

1. Siga as etapas para [Verificar se que a API Web está exposta a outros aplicativos](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Expor um novo escopo ou função pelo manifesto do aplicativo

[![Expor um novo escopo usando a coleção oauth2Permissions no manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Para expor um novo escopo ou função pelo manifesto do aplicativo:

1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Abre um editor de manifesto baseado na Web, permitindo a você **Editar** o manifesto dentro do portal. Opcionalmente, você pode selecionar **Baixar** e editar o manifesto localmente e, em seguida, usar **Carregar** para reaplicá-lo ao seu aplicativo.
    
    O exemplo a seguir mostra como expor um novo escopo chamado `Employees.Read.All` no recurso ou na API adicionando o seguinte elemento JSON à coleção `oauth2Permissions`.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

   > [!NOTE]
   > O valor da `id` precisa ser gerado de modo programático ou usando uma ferramenta de geração de GUID, como [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). O `id` representa um identificador exclusivo para o escopo exposto pela API Web. Depois que um cliente está configurado adequadamente com permissões para acessar a sua API Web, o Azure AD emite um token de acesso OAuth 2.0. Quando o cliente chama a API Web, ele apresenta o token de acesso que tem a declaração de escopo (scp) definida para as permissões solicitadas no seu registro de aplicativo.
   >
   > É possível expor escopos adicionais posteriormente conforme a necessidade. Lembre-se de que a API Web pode expor vários escopos associados a uma variedade de funções diferentes. O recurso pode controlar o acesso à API Web em tempo de execução, avaliando declarações de escopo (`scp`) no token de acesso OAuth 2.0 recebido.

1. Ao terminar, clique em **Salvar**. Agora sua API Web está configurada para ser usada por outros aplicativos do seu diretório.
1. Siga as etapas para [Verificar se que a API Web está exposta a outros aplicativos](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificar se a API Web está exposta a outros aplicativos

1. Volte para o locatário do Azure AD, clique em **Registros de aplicativo** e localize e selecione o aplicativo cliente que você deseja configurar.
1. Repita as etapas descritas em Configurar um aplicativo cliente para acessar APIs Web.
1. Quando chegar à etapa **Selecionar uma API**, selecione o recurso. Você deve ver o novo escopo, disponível para solicitações de permissão do cliente.

## <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto do aplicativo

O manifesto do aplicativo funciona como um mecanismo para atualizar a entidade do aplicativo, que define todos os atributos de configuração de identidade de um aplicativo do Azure AD. Para obter mais informações sobre a entidade Aplicativo e seu esquema, confira a [Documentação da entidade do Aplicativo da API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completas sobre os membros da entidade do Aplicativo usados para especificar permissões para a sua API, incluindo:  

* O membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), usado para definir [permissões de aplicativo](developer-glossary.md#permissions) para uma API Web.
* O membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), usado para definir as [permissões delegadas](developer-glossary.md#permissions) para uma API Web.

Para obter mais informações sobre os conceitos do manifesto do aplicativo em geral, consulte [Entendendo o manifesto de aplicativo do Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre esses outros inícios rápidos de gerenciamento de aplicativos relacionados para aplicativos:

* [Registrar um aplicativo com a Microsoft Identity Platform](quickstart-register-app.md)
* [Configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
* [Modificar as contas que têm suporte por um aplicativo](quickstart-modify-supported-accounts.md)
* [Remover um aplicativo registrado na Microsoft Identity Platform](quickstart-remove-app.md)

Saiba mais sobre os dois objetos do Azure AD que representam um aplicativo registrado e a relação entre eles, confira [Objetos de aplicativo e objetos de entidade de serviço](app-objects-and-service-principals.md).

Para saber mais sobre as diretrizes de identidade visual que você deve usar ao desenvolver aplicativos com o Azure Active Directory, confira [Diretrizes de identidade visual para aplicativos](howto-add-branding-in-azure-ad-apps.md).
