---
title: "Instalação guiada do JS SPA no Azure AD v2 – teste | Microsoft Docs"
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
ms.openlocfilehash: c888760ab311e8ac08b1e625bb837f91047db645
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testar seu código

> ### <a name="testing-with-visual-studio"></a>Testes com o Visual Studio
> Se estiver usando o Visual Studio, pressione `F5` para executar o projeto: o navegador abrirá e direcionará você para *http://localhost:{porta}*, no qual você vê o botão *Chamar a API do Microsoft Graph*.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Testando com Python ou outro servidor Web
> Se não estiver usando o Visual Studio, certifique-se de que seu servidor Web tenha sido iniciado e de que esteja configurado para escutar uma porta TCP baseada na parte que contém seu arquivo *index.html*. Para o Python, você pode começar a escutar a porta executando o prompt de comando/ terminal, na pasta do aplicativo:
> 
> ```bash
> python -m http.server 8080
> ```
>  Em seguida, abra o navegador e digite *http://localhost:8080* ou *http://localhost:{porta}*, em que *porta* corresponde à porta que o servidor Web está escutando. Você deve ver o conteúdo da página index.html com o botão *Chamar a API do Microsoft Graph*.

## <a name="test-your-application"></a>Teste seu aplicativo

Após o navegador carregar seu *index.html*, clique no botão *Chamar a API do Microsoft Graph*. Se esta for a primeira vez, o navegador o redirecionará para o ponto de extremidade do Microsoft Azure Active Directory v2, em que você precisará entrar.
 
![Captura de tela de exemplo](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, na qual você precisa aceitar:

 ![Tela de consentimento](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Resultados esperados
Você deverá ver as informações de perfil do usuário retornadas pela resposta à chamada à API do Microsoft Graph.
 
 ![Resultados](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Você também verá informações básicas sobre o token adquirido nas caixas *Token de Acesso* e *Declarações do Token de ID*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado em nosso portal de registro. Algumas outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, para o Microsoft Graph, o escopo `Calendars.Read` é necessário para listar os calendários do usuário. Para acessar o calendário do usuário no contexto de um aplicativo, é necessário adicionar a permissão delegada `Calendars.Read` às informações de registro do aplicativo e, em seguida, adicionar o escopo `Calendars.Read` à chamada `acquireTokenSilent`. Talvez o usuário precise fornecer consentimentos adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não exigir um escopo (não recomendado), será possível usar o `clientId` como o escopo nas chamadas `acquireTokenSilent` e/ou `acquireTokenRedirect`.

<!--end-collapse-->
