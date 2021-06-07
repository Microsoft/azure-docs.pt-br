---
title: 'Início Rápido: Adicionar a entrada do usuário a um aplicativo Web Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você aprenderá a implementar a autenticação em um aplicativo Web Node.js usando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103491"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Início Rápido: Adicionar entrada usando o OpenID Connect para um aplicativo Web do Node.js

Neste guia de início rápido, você baixará e executará um exemplo de código que demonstra como configurar a autenticação do OpenID Connect em um aplicativo Web criado usando o Node.js com o Express. O exemplo foi projetado para ser executado em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registre seu aplicativo

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual você deseja registrar um aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **Gerenciar**, selecione **Registros de aplicativo** > **Novo registro**.
1. Insira um **Nome** para seu aplicativo, por exemplo, `MyWebApp`. Os usuários do seu aplicativo podem ver esse nome e você pode alterá-lo mais tarde.
1. Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .

    Se houver mais de um URI de redirecionamento, adicione-os por meio da guia **Autenticação** posteriormente depois que o aplicativo for criado com êxito.

1. Selecione **Registrar** para criar o aplicativo.
1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará desse valor para configurar o aplicativo posteriormente neste projeto.
1. Em **Gerenciar**, selecione **Autenticação**.
1. Selecione **Adicionar uma plataforma** > **Web**. 
1. Na seção **URIs de Redirecionamento**, insira `http://localhost:3000/auth/openid/return`.
1. Insira uma **URL de logoff de front-channel** igual a `https://localhost:3000`.
1. Na seção **Concessão implícita e fluxos híbridos**, selecione **Tokens de ID**, pois este exemplo exige que o [Fluxo de concessão implícita](./v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário.
1. Selecione **Configurar**.
1. Em **Gerenciar**, selecione **Certificados e segredos** > **Novo segredo do cliente**.
1. Insira uma descrição de chave (para o segredo do aplicativo da instância).
1. Selecione uma duração de chave de **Em 1 ano, Em 2 anos** ou **Nunca expira**.
1. Selecione **Adicionar**. O valor da chave será exibido. Copie o valor da chave e salve-o em uma localização segura para uso posterior.


## <a name="download-the-sample-application-and-modules"></a>Baixar os módulos e aplicativo de exemplo

Em seguida, clone o repositório de exemplo e instale os módulos NPM.

No shell ou na linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

No diretório raiz do projeto, execute o comando:

`$ npm install`

## <a name="configure-the-application"></a>Configurar o aplicativo

Forneça os parâmetros em `exports.creds` em config.js conforme instruído.

* Atualize `<tenant_name>` em `exports.identityMetadata` com o nome do locatário do Azure AD do formato \*.onmicrosoft.com.
* Atualize `exports.clientID` com a ID do Aplicativo anotada no registro do aplicativo.
* Atualize `exports.clientSecret` com o Segredo do aplicativo anotado no registro do aplicativo.
* Atualize `exports.redirectUrl` com o URI de Redirecionamento anotado no registro do aplicativo.

**Configuração opcional para aplicativos de produção:**

* Atualize `exports.destroySessionUrl` em config.js se quiser usar um `post_logout_redirect_uri` diferente.

* Defina `exports.useMongoDBSessionStore` em config.js como true se quiser usar [mongoDB](https://www.mongodb.com) ou outros [repositórios de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
O repositório de sessão padrão neste exemplo é `express-session`. O repositório de sessão padrão não é adequado para produção.

* Atualize `exports.databaseUri` se quiser usar o repositório de sessão do mongoDB e um URI de banco de dados diferente.

* Atualizar `exports.mongoDBSessionMaxAge`. Aqui você pode especificar por quanto tempo deseja manter uma sessão no mongoDB. A unidade é segundos (s).

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Inicie o serviço mongoDB. Se você estiver usando o repositório de sessão do mongoDB neste aplicativo, será necessário [instalar o mongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se você estiver usando o repositório de sessão padrão, poderá ignorar esta etapa.

Execute o aplicativo usando o comando a seguir na linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil de entender?:** usamos `bunyan` para registro em log neste exemplo. O console não fará muito sentido para você, a menos que você também instale o Bunyan e execute o servidor, como acima, mas redirecione-o por meio do binário Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Pronto!

Você terá um servidor em execução com êxito no `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cenário do aplicativo Web compatível com a plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Aplicativo Web que conecta cenários dos usuários](scenario-web-app-sign-user-overview.md)