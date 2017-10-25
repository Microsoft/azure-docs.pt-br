---
title: "Introdução ao iOS no Azure AD v2 – Teste | Microsoft Docs"
description: Como aplicativos iOS (Swift) podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2
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
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 4a88096d2b0a23708acdbc1798eac528599b4f71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>Testar a consulta da API do Microsoft Graph de seu aplicativo iOS

Pressione `Command` + `R` para executar o código no simulador.

![Captura de tela de exemplo](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

Quando estiver pronto para testar, toque em *“Chamar API do Microsoft Graph”* e você será solicitado a digitar seu nome de usuário e senha.

### <a name="consent"></a>Consentimento
Na primeira vez que você entrar no aplicativo, será apresentada uma tela de consentimento semelhante à tela abaixo, na qual você precisa aceitar explicitamente:

![Tela de consentimento](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>Resultados esperados
Você deverá ver as informações de perfil do usuário retornadas pela chamada à API do Microsoft Graph na seção *Registro em Log*.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo `user.read` para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado em nosso portal de registro. Algumas outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, para o Microsoft Graph, o escopo `Calendars.Read` é necessário para listar os calendários do usuário. Para acessar o calendário do usuário no contexto de um aplicativo, é necessário adicionar a permissão delegada `Calendars.Read` às informações de registro do aplicativo e, em seguida, adicionar o escopo `Calendars.Read` à chamada `acquireTokenSilent`. Talvez o usuário precise fornecer consentimentos adicionais à medida que o número de escopos aumentar.

<!--end-collapse-->



