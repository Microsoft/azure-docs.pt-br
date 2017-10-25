---
title: "Instalação guiada do JS SPA no Azure AD v2 - Instalar | Microsoft Docs"
description: Como aplicativos JavaScript SPA podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: fc9f88cc8d23abcfa8ea30e346192732b422ffa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="setting-up-your-web-server-or-project"></a>Configuração do servidor Web ou do projeto

> Prefere baixar este projeto de exemplo? 
> - [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> ou o
> - [Baixar os arquivos do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) para um servidor Web local, como Python
>
> Em seguida, pule para a [Etapa de configuração](#create-an-application-express) para configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos
Um servidor Web local como [Python http.server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core) ou a integração do IIS Express ao [Visual Studio 2017](https://www.visualstudio.com/downloads/) é necessário para executar a instalação guiada. 

As instruções neste guia são baseadas no Python e no Visual Studio 2017, mas fique à vontade para usar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar seu projeto 

> ### <a name="option-1-visual-studio"></a>Opção 1: Visual Studio 
> Se você estiver usando o Visual Studio e estiver criando um novo projeto, siga as etapas abaixo para criar uma nova solução do Visual Studio:
> 1.    No Visual Studio:  `File` > `New` > `Project`
> 2.    Em `Visual C#\Web`, selecione `ASP.NET Web Application (.NET Framework)`
> 3.    Nomeie o aplicativo e clique em *OK*
> 4.    Em `New ASP.NET Web Application`, selecione `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Opção 2: Python/outros servidores Web
> Verifique se você instalou o [Python](https://www.python.org/downloads/) e siga a etapa abaixo:
> - Crie uma pasta para hospedar o aplicativo.


## <a name="create-your-single-page-applications-ui"></a>Criar a interface do usuário do aplicativo de página única
1.  Crie um arquivo *index.html* para seu JavaScript SPA. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com botão direito do mouse e selecione: `Add` > `New Item` > `HTML page` e nomeie-o index.html
2.  Adicione o seguinte código à sua página:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
