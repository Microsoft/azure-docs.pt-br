---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133407"
---
## <a name="set-up-your-web-server-or-project"></a>Configurar o servidor Web ou o projeto

> Prefere baixar este projeto de exemplo? Faça uma das opções a seguir:
> 
> - [Baixe os arquivos do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para executar o projeto com um servidor web local, como Node.js.
>
> - (Opcional) [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), para executar o projeto com o servidor IIS.
>
> Em seguida, pule para a [etapa de configuração](#register-your-application) a fim de configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, é necessário um servidor Web local como [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou a integração do IIS Express com o [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se estiver usando o Node.js para executar o projeto, instale um ambiente de desenvolvimento integrado (IDE), como o [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos de projeto.

* As instruções neste guia são baseadas no Node.js e no Visual Studio 2017, mas você pode usar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar seu projeto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opção 1: Node.js ou outros servidores Web
> Verifique se você instalou o [Node.js](https://nodejs.org/en/download/) e faça o seguinte:
> - Crie uma pasta para hospedar o aplicativo.
>
> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se você estiver usando o Visual Studio e criar um novo projeto, faça o seguinte:
> 1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
> 1. Em **Visual C#\Web**, selecione **Aplicativo Web ASP.NET (.NET Framework)** .
> 1. Insira um nome para o aplicativo e selecione **OK**.
> 1. Em **Novo aplicativo Web do ASP.NET**, selecione **Vazio**.

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA
1. Crie um arquivo *index.html* para seu JavaScript SPA. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto), clique com o botão direito do mouse e selecione: **Adicionar** > **Novo Item** > **Página HTML** e nomeie-o *index.html*.

1. No arquivo *index.html*, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Você pode substituir a versão de MSAL.js no script anterior pela versão mais recente em [versões de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
