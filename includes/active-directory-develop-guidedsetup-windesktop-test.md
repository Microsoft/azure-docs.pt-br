---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172100"
---
## <a name="test-your-code"></a>Testar seu código

Para executar o projeto no Visual Studio, selecione **F5**. Seu aplicativo **MainWindow** é exibido, conforme mostrado aqui:

![Teste seu aplicativo](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Na primeira vez em que você executar o aplicativo e selecionar o botão **Chamar a API do Microsoft Graph**  você será solicitado a entrar. Use uma conta do Azure Active Directory (conta corporativa ou de estudante) ou uma conta da Microsoft (live.com, outlook.com) para testá-lo.

![Entrar no aplicativo](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que você entrar no seu aplicativo, você também será solicitado a fornecer autorização para permitir que o aplicativo acesse seu perfil e o conecte, como mostrado aqui:

![Forneça sua autorização para acesso do aplicativo](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Depois de entrar, você deverá ver as informações do perfil do usuário retornadas pela chamada da API do Microsoft Graph. Os resultados são exibidos na caixa **Resultados da Chamada da API**. As informações básicas sobre o token que foi adquirido através da chamada para `AcquireTokenInteractive` ou `AcquireTokenSilent` devem estar visíveis na caixa **Informações de Token**. Os resultados contêm as propriedades a seguir:

|Propriedade  |Formatar  |DESCRIÇÃO |
|---------|---------|---------|

|**Nome de usuário** |<span>user@domain.com</span> | O nome de usuário usado para identificar o usuário.| |**Token Expira** |DateTime |A hora em que o token expira. MSAL estende a data de validade renovando o token, conforme necessário.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Esse escopo é adicionado automaticamente, por padrão, a cada aplicativo que é registrado no Portal de Registro de Aplicativos. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. A API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
