---
title: Criar um aplicativo daemon que chama APIs Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo daemon que chama APIs da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 00a70b585ddf522a25e81703fe5bdf55efbcb7e1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582767"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicativo daemon que chama APIs Web

Saiba tudo o que você precisa para criar um aplicativo daemon que chama APIs da Web.

## <a name="overview"></a>Visão geral

Seu aplicativo pode adquirir um token para chamar uma API da Web em nome de si mesmo (não em nome de um usuário). Esse cenário é útil para aplicativos de daemon. Ele usa a concessão das [credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) padrão do OAuth 2.0.

![Aplicativos Daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicativos de daemon:

- Aplicativos Web que são usados para provisionar ou administrar usuários ou executar processos em lote em um diretório
- Aplicativos de área de trabalho (como serviços do Windows em processos do Windows ou daemon no Linux) que executam trabalhos em lotes ou um serviço do sistema operacional executado em segundo plano
- APIs Web que precisam manipular diretórios, não usuários específicos

Há outro caso comum em que aplicativos não daemon usam credenciais de cliente: mesmo quando eles atuam em nome dos usuários, eles precisam acessar uma API da Web ou um recurso sob sua própria identidade por motivos técnicos. Um exemplo é o acesso a segredos no Azure Key Vault ou no banco de dados SQL do Azure para um cache.

Aplicativos que adquirem um token para suas próprias identidades:

- São aplicativos cliente confidenciais. Esses aplicativos, Considerando que acessam recursos independentemente dos usuários, precisam provar sua identidade. Eles também são aplicativos confidenciais. Eles precisam ser aprovados pelos administradores de locatário do Azure Active Directory (AD do Azure).
- Registrou um segredo (senha de aplicativo ou certificado) com o Azure AD. Esse segredo é passado durante a chamada para o Azure AD para obter um token.

## <a name="specifics"></a>Especificações

> [!IMPORTANT]
>
> - Os usuários não podem interagir com um aplicativo daemon. Um aplicativo daemon requer sua própria identidade. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID de aplicativo, credencial (senha ou certificado) e URI de ID do aplicativo para o Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso (e um token de atualização) da plataforma de identidade da Microsoft. Esse token é usado para chamar a API da Web (e é atualizado conforme necessário).
> - Como os usuários não podem interagir com aplicativos daemon, o consentimento incremental não é possível. Todas as permissões de API necessárias precisam ser configuradas no registro do aplicativo. O código do aplicativo apenas solicita permissões definidas estaticamente. Isso também significa que os aplicativos daemon não oferecerão suporte a consentimento incremental.

Para os desenvolvedores, a experiência de ponta a ponta para esse cenário tem os seguintes aspectos:

- Os aplicativos daemon podem funcionar apenas em locatários do Azure AD. Não faz sentido criar um aplicativo daemon que tente manipular contas pessoais da Microsoft. Se você for um desenvolvedor de aplicativos LOB (linha de negócios), criará seu aplicativo daemon em seu locatário. Se você for um ISV, talvez queira criar um aplicativo de daemon multilocatário. Cada administrador de locatários precisará fornecer consentimento.
- Durante o [registro do aplicativo](./scenario-daemon-app-registration.md), o URI de resposta não é necessário. Compartilhe segredos ou certificados ou declarações assinadas com o Azure AD. Você também precisa solicitar permissões de aplicativo e conceder consentimento de administrador para usar essas permissões de aplicativo.
- A [configuração do aplicativo](./scenario-daemon-app-configuration.md) precisa fornecer credenciais de cliente como compartilhadas com o Azure ad durante o registro do aplicativo.
- O [escopo](scenario-daemon-acquire-token.md#scopes-to-request) usado para adquirir um token com o fluxo de credenciais do cliente precisa ser um escopo estático.

## <a name="recommended-reading"></a>Leitura recomendada

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, registro de [aplicativo](./scenario-daemon-app-registration.md).
