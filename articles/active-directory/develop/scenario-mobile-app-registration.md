---
title: Aplicativo móvel que chama as APIs da Web – configuração de código do aplicativo | Plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bebaa5d35876d562e567a8398cc7a9ce7e6f488
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413586"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Aplicativo móvel que chama APIs Web-registro de aplicativo

Este artigo contém as instruções de registro do aplicativo para criar um aplicativo móvel.

## <a name="supported-accounts-types"></a>Tipos de contas com suporte

Os tipos de conta com suporte em aplicativos móveis dependem da experiência que você deseja habilitar e dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para aquisição de token interativo

A maioria dos aplicativos móveis usa a autenticação interativa. Se esse for o seu caso, você poderá conectar usuários de qualquer [tipo de conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Público-alvo para autenticação integrada, nome de usuário/senha e B2C

- Se você pretende usar a autenticação integrada do Windows (possível em aplicativos UWP) ou nome de usuário/senha, seu aplicativo precisará conectar usuários em seu próprio locatário (desenvolvedor de LOB) ou em organizações do Azure Active Directory (cenário de ISV). Esses fluxos de autenticação não têm suporte para contas pessoais da Microsoft
- Se você conectar usuários com identidades sociais passando uma política e uma autoridade B2C, você só poderá usar a autenticação interativa e de nome de usuário-senha. O nome de usuário-senha atualmente só tem suporte em Xamarin. iOS, Xamarin. Android e UWP.

Para obter uma visão geral, consulte [cenários e fluxos de autenticação com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) e [cenários e plataformas e linguagens com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Configuração de plataforma e URIs de redirecionamento  

### <a name="interactive-authentication"></a>Autenticação interativa

Ao criar um aplicativo móvel usando a autenticação interativa, a etapa de registro mais crítica é o URI de redirecionamento. Isso pode ser definido por meio da [configuração de plataforma na folha autenticação](https://aka.ms/MobileAppReg).

Essa experiência permitirá que seu aplicativo obtenha SSO (logon único) por meio do Microsoft Authenticator (e Portal da Empresa do Intune no Android), bem como suporte a políticas de gerenciamento de dispositivos.

Observe que há uma experiência de visualização no portal de registro de aplicativo para ajudá-lo a calcular o URI de resposta orientado para aplicativos iOS e Android:

1. No registro do aplicativo, escolha **autenticação** e seleção **experimentar a nova imagem de experiência**
   ![](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecione **Adicionar**
   imagem de plataforma![](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando houver suporte para a lista de plataformas, selecione imagem do **Ios**
   ![](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Insira a ID do pacote conforme solicitado e pressione a imagem do **registro**
   ![](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. O URI de redirecionamento é calculado para você.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Se preferir configurar manualmente o URI de redirecionamento, você poderá fazer isso por meio do manifesto do aplicativo. O formato recomendado é o seguinte:

- ***Ios***: `msauth.<BUNDLE_ID>://auth` (por exemplo, "msauth. com. suaempresa. AppName://auth")
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - O hash de assinatura do Android pode ser gerado usando as chaves Release ou debug por meio do comando keytool.

### <a name="username-password"></a>Senha do nome de usuário

Se seu aplicativo estiver usando apenas o nome de usuário/senha, você não precisará registrar um URI de redirecionamento para seu aplicativo. De fato, esse fluxo faz uma viagem de ida e volta para o ponto de extremidade v 2.0 da plataforma de identidade da Microsoft e seu aplicativo não será chamado novamente em qualquer URI específico. No entanto, você precisa expressar que seu aplicativo é um aplicativo cliente público. Essa configuração é obtida acessando a seção de **autenticação** do seu aplicativo e, na subseção **Configurações avançadas** , escolha **Sim**, para o **aplicativo pergunta tratar como um cliente público** (no **padrão** parágrafo de tipo de cliente)

## <a name="api-permissions"></a>Permissões de APIs

Os aplicativos móveis chamam APIs em nome do usuário conectado. Seu aplicativo precisa solicitar permissões delegadas, também chamadas de escopos. Dependendo da experiência desejada, isso pode ser feito estaticamente por meio do portal do Azure ou dinamicamente em tempo de execução. O registro estático das permissões permite que os administradores aprovem facilmente seu aplicativo e é recomendado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configuração de código](scenario-mobile-app-configuration.md)
