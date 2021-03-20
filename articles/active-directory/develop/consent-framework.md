---
title: Estrutura de consentimento do Azure AD
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a estrutura de consentimento no Azure Active Directory e como ela facilita o desenvolvimento de aplicativos Web multilocatários e cliente nativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2020
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.openlocfilehash: 74321bc75fa760727e7896f47cdfc5b2929047e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366081"
---
# <a name="azure-active-directory-consent-framework"></a>Estrutura de consentimento do Azure Active Directory

A estrutura de consentimento do Azure AD (Azure Active Directory) facilita o desenvolvimento de aplicativos da Web multilocatários e clientes nativos. Esses aplicativos permitem a entrada de contas de usuário de um locatário do Azure AD diferente daquele no qual o aplicativo está registrado. Eles também podem precisar acessar APIs da Web, como a API de Microsoft Graph (para acessar o Azure AD, o Intune e os serviços no Microsoft 365) e outras APIs de serviços da Microsoft, além de suas próprias APIs Web.

A estrutura se baseia em um usuário ou administrador que dá autorização a um aplicativo que solicita seu registro no diretório, o que pode envolver acesso aos dados do diretório. Por exemplo, se um aplicativo cliente Web precisar ler informações de calendário sobre o usuário do Microsoft 365, esse usuário precisará dar consentimento ao aplicativo cliente primeiro. Depois que o autorização for dado, o aplicativo cliente poderá chamar a API do Microsoft Graph em nome do usuário e usar as informações de calendário conforme a necessidade. A [API de Microsoft Graph](https://developer.microsoft.com/graph) fornece acesso a dados em Microsoft 365 (como calendários e mensagens do Exchange, sites e listas do SharePoint, documentos do onedrive, notebooks do OneNote, tarefas do Planner e pastas de trabalho do Excel), bem como usuários e grupos do Azure AD e outros objetos de dados de outros serviços de nuvem da Microsoft.

A estrutura de consentimento foi criada com base no OAuth 2.0 e seus vários fluxos, como concessão de código de autorização e concessão de credenciais de cliente, usando clientes públicos ou confidenciais. Com o OAuth 2.0, o Azure AD permite criar muitos tipos diferentes de aplicativo cliente, como em um telefone, tablet, servidor ou um aplicativo Web, assim como obter acesso aos recursos necessários.

Para saber mais sobre como usar a estrutura de consentimento com concessões de autorização do OAuth2.0, confira [Autorizar o acesso a aplicativos Web usando OAuth 2.0 e Azure AD](v2-oauth2-auth-code-flow.md) e [Cenários de autenticação do Azure AD](./authentication-vs-authorization.md). Para obter informações sobre como obter acesso autorizado ao Microsoft 365 por meio do Microsoft Graph, consulte [autenticação de aplicativo com Microsoft Graph](/graph/).

## <a name="consent-experience---an-example"></a>Experiência de consentimento, um exemplo

As etapas a seguir mostram como a experiência de consentimento funciona para o desenvolvedor e o usuário do aplicativo.

1. Suponha que você tenha um aplicativo cliente Web que precise solicitar permissões específicas para acessar uma recurso/API. Você aprenderá como fazer essa configuração na próxima seção, mas o portal do Azure é usado essencialmente para declarar as solicitações de permissão no momento da configuração. Como outras definições de configuração, elas se tornam parte do registro do Azure AD do aplicativo:

    ![Permissões para outros aplicativos](./media/consent-framework/permissions.png)

1. Pense na possibilidade de que as permissões do aplicativo tenham sido atualizadas, de que o aplicativo esteja em execução e de que um usuário esteja prestes a usá-lo pela primeira vez. Primeiro, o aplicativo deve obter um código de autorização do ponto de extremidade `/authorize` do Azure AD. O código de autorização então pode ser usado para adquirir um novo acesso e token de atualização.

1. Se o usuário ainda não tiver sido autenticado, o ponto de extremidade `/authorize` do Azure AD solicitará o logon.

    ![Conexão do usuário ou administrador no AD do Azure](./media/consent-framework/usersignin.png)

1. Depois que o usuário tiver se conectado, o AD do Azure determinará se o usuário precisará ver uma página de consentimento. Essa decisão depende do fato de o usuário (ou do administrador da organização) já ter dado o consentimento de aplicativo. Se o consentimento ainda não tiver sido dado, o Azure AD solicitará o consentimento ao usuário e exibirá as permissões necessárias de que ele precisa para funcionar. O conjunto de permissões exibido na caixa de diálogo de autorização corresponde àqueles selecionados nas **Permissões delegadas** no portal do Azure.

    ![Mostra um exemplo de permissões exibidas na caixa de diálogo de consentimento](./media/consent-framework/consent.png)

1. Depois que o usuário dá consentimento, um código de autorização é retornado ao aplicativo, que é resgatado para aquisição de um token de acesso e de um token de atualização. Para obter mais informações sobre esse fluxo, consulte [fluxo de código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md).

1. Como administrador, você pode também concorda com permissões do aplicativo em nome de todos os usuários em seu locatário. O consentimento administrativo impede que a caixa de diálogo de consentimento apareça para cada usuário no locatário e pode ser feita no [portal do Azure](https://portal.azure.com) por usuários com a função de administrador. Para saber quais funções de administrador podem consentir as permissões delegadas, consulte [permissões de função de administrador no Azure ad](../roles/permissions-reference.md).

    **Para consentir com as permissões delegadas de um aplicativo**

   1. Vá para a página de **permissões de API** para seu aplicativo
   1. Clique no botão **conceder consentimento do administrador** .

      ![Conceda permissões para consentimento explícito de admin](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > A concessão explícita de consentimento usando o botão **Conceder permissões** é necessária atualmente para SPAs (aplicativos de página única) que usam o ADAL.js. Caso contrário, o aplicativo falhará quando o token de acesso for solicitado.

## <a name="next-steps"></a>Próximas etapas

Veja [como converter um aplicativo em multilocatário](howto-convert-app-to-be-multi-tenant.md)
