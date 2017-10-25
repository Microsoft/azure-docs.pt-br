---
title: "Introdução à Área de Trabalho do Windows no Azure AD v2 – Teste | Microsoft Docs"
description: "Como os aplicativos .NET da Área de Trabalho do Windows (XAML) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Testar seu código

Para testar seu aplicativo, pressione `F5` para executar o projeto no Visual Studio. A Janela Principal deve ser exibida:

![Captura de tela de exemplo](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Quando você estiver pronto para testar, clique em *Chamar API do Microsoft Graph* e use uma conta do Microsoft Azure Active Directory (conta organizacional) ou uma Conta da Microsoft (live.com, outlook.com) para se conectar. Se esta for a primeira vez, você verá uma janela solicitando o usuário a entrar:

![Conexão](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, na qual você precisa aceitar explicitamente:

![Tela de consentimento](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Você deverá ver as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na tela Resultados da Chamada à API.

Você também deverá ver informações básicas sobre o token adquirido por meio de `AcquireTokenAsync` ou `AcquireTokenSilentAsync` na caixa Informações de Token:

|Propriedade  |Formatar  |Descrição |
|---------|---------|---------|
|Nome | {Nome completo do usuário} |Nome e sobrenome do usuário|
|Nome de Usuário |<span>user@domain.com</span> |O nome de usuário usado para identificar o usuário|
|O Token Expira |{DateTime}         |A data e a hora em que o token expira. A MSAL estenderá a data de expiração para você renovando o token quando necessário|
|Token de acesso |{String}         |A cadeia de caracteres do token enviada que será enviada para solicitações HTTP que exigem um cabeçalho de autorização|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas
A API do Graph precisa do escopo `user.read` para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado em nosso portal de registro. Algumas outras APIs do Graph, bem como APIs personalizadas do servidor de back-end, exigem escopos adicionais. Por exemplo, para o Graph, `Calendars.Read` é necessário para listar os calendários do usuário. Para acessar o calendário do usuário no contexto de um aplicativo, você precisa adicionar as informações de registro do aplicativo delegado `Calendars.Read` e, em seguida, adicionar `Calendars.Read` à chamada `AcquireTokenAsync`. O usuário pode precisar fornecer consentimentos adicionais à medida que o número de escopos aumenta.

<!--end-collapse-->



