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
ms.openlocfilehash: 494f5fb6f2bfdec86cad01a37e57c3ec219a77f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133231"
---
## <a name="test-your-code"></a>Testar seu código

Teste seu código usando qualquer um dos ambientes a seguir.

### <a name="test-with-nodejs"></a>Testar com Node.js

Se você não estiver usando o Visual Studio, verifique se o servidor Web está iniciado.

1. Configure o servidor para escutar uma porta TCP com base no local do seu arquivo *index.html*. Para o Node.js, inicie o servidor Web para escutar a porta executando os comandos a seguir em um prompt de linha de comando da pasta de aplicativo:

    ```bash
    npm install
    node server.js
    ```
1. No seu navegador, digite **http://\<span>\</span>localhost:30662** ou **http://\<span>\</span>localhost:{port}** , em que *port* é a porta que o seu servidor Web está escutando. Você deve ver o conteúdo do seu arquivo *index.html* e o botão **Entrar**.

### <a name="test-with-visual-studio"></a>Testar com Visual Studio

Se você estiver usando o Visual Studio, verifique se selecionou a solução de projeto e pressione F5 para executar o projeto. O navegador abre para o local http://<span></span>localhost:{port} e o botão **Entrar** deve estar visível.

## <a name="test-your-application"></a>Teste seu aplicativo

Depois que o navegador carregar seu arquivo *index.html*, selecione **Entrar**. Você será solicitado a entrar com o ponto de extremidade da plataforma de identidade da Microsoft:

![A janela de entrada do conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que entrar no aplicativo, você será solicitado a fornecer acesso ao seu perfil e a conectá-lo:

![A janela de "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, as informações de perfil do usuário são retornadas na resposta da API do Microsoft Graph exibida na página.

![Resultados esperados da chamada da API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Este escopo é automaticamente adicionado por padrão em cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar a *clientId* como o escopo nas chamadas para obter tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
