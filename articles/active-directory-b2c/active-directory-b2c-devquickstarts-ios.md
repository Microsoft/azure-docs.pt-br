---
title: Adquirindo um token usando um aplicativo iOS - Azure AD B2C | Microsoft Docs
description: "Este artigo mostra como criar um aplicativo iOS que usa AppAuth com o Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários."
services: active-directory-b2c
documentationcenter: ios
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objectivec
ms.topic: article
ms.date: 03/07/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: ebec5d910b8987dcc8155cd4ead00f87d219941c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: entrar usando um aplicativo iOS

A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. O uso de um protocolo de padrão aberto oferece mais opções de desenvolvedor ao selecionar uma biblioteca para integrar aos nossos serviços. Fornecemos este passo a passo e outros como ele para ajudar os desenvolvedores a escrever aplicativos que se conectam à plataforma Microsoft Identity. A maioria das bibliotecas que implementam [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) pode conectar-se à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não as analisou. Esse exemplo usa uma biblioteca de terceiros chamada AppAuth que foi testada com relação à compatibilidade em cenários básicos com o Azure AD B2C. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca. Para obter mais informações, consulte [este artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se você ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo poderá não fazer muito sentido para você. Recomendamos que você examine uma breve [visão geral do protocolo que documentamos aqui](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. O registro do aplicativo dá ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo móvel, siga [estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Inclua um **Cliente nativo** no aplicativo.
* Copie a **ID de aplicativo** atribuída ao aplicativo. Você precisará desse GUID posteriormente.
* Configure um **URI de redirecionamento** com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Você precisará desse URI posteriormente.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém uma experiência de identidade: uma combinação de entrada e inscrição. Crie essa política conforme descrito no [artigo de referência da política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ao criar a política, não se esqueça de fazer o seguinte:

* Em **Atributos de inscrição**, selecione o atributo **Nome de exibição**.  É possível selecionar outros atributos também.
* Em **Declarações do aplicativo**, selecione as declarações **Nome de exibição** e **ID de Objeto do Usuário**. É possível selecionar outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. O nome da política é prefixado com `b2c_1_` quando a política é salva.  Posteriormente, você precisará do nome da política.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as políticas, você estará pronto para compilar o aplicativo.

## <a name="download-the-sample-code"></a>Baixe o código de exemplo
Nós fornecemos um exemplo funcional que usa o AppAuth com o Azure AD B2C [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Você pode baixar o código e executá-lo. Para usar seu próprio locatário Azure AD B2C, siga as instruções de [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Este exemplo foi criado seguindo as instruções do LEIAME no [projeto AppAuth iOS no GitHub](https://github.com/openid/AppAuth-iOS). Para obter mais detalhes sobre como o exemplo e a biblioteca funcionam, consulte o LEIAME do AppAuth no GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modificando seu aplicativo para usar o Azure AD B2C com AppAuth

> [!NOTE]
> O AppAuth dá suporte a iOS 7 e versões superiores.  No entanto, para dar suporte a logons sociais no Google, é necessário ter o SFSafariViewController, que exige iOS 9 ou versão superior.
>

### <a name="configuration"></a>Configuração

Você pode configurar a comunicação com o Azure AD B2C especificando o ponto de extremidade de autorização e os URIs de ponto de extremidade de token.  Para gerar esses URIs, você precisa das seguintes informações:
* ID do locatário (por exemplo, contoso.onmicrosoft.com)
* Nome da política (por exemplo, B2C\_1\_SignUpIn)

O URI do ponto de extremidade de token pode ser gerado substituindo a ID\_do locatário e o nome\_da política na seguinte URL:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

O URI do ponto de extremidade de autorização pode ser gerado substituindo a ID\_do locatário e o nome\_da política na seguinte URL:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Execute o seguinte código para criar o objeto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizando

Depois de configurar ou recuperar uma configuração de serviço de autorização, uma solicitação de autorização pode ser criada. Para criar a solicitação, você precisará das seguintes informações:  
* ID do cliente (por exemplo, 00000000-0000-0000-0000-000000000000)
* URI de redirecionamento com um esquema personalizado (por exemplo, com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Os dois itens devem ter sido salvos quando você estava [registrando seu aplicativo](#create-an-application).

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Para configurar seu aplicativo a fim de manipular o redirecionamento para o URI com o esquema personalizado, você precisará atualizar a lista de 'Esquemas de URL' em seu Info.pList:
* Abra Info.pList.
* Focalize uma linha como 'Código do tipo do Sistema Operacional do Pacote' e clique no símbolo \+.
* Renomeie a nova linha 'Tipos de URL'.
* Clique na seta à esquerda de 'Tipos de URL' para abrir a árvore.
* Clique na seta à esquerda do 'Item 0' para abrir a árvore.
* Renomeie o primeiro item sob o Item 0 como 'Esquemas de URL'.
* Clique na seta à esquerda de 'Esquemas de URL' para abrir a árvore.
* Na coluna 'Valor', há um campo em branco à esquerda do “Item 0”, abaixo de 'Esquemas de URL'.  Defina o valor com o esquema exclusivo do aplicativo.  O valor deve corresponder ao esquema usado na redirectURL ao criar o objeto OIDAuthorizationRequest.  Em nosso exemplo, usamos o esquema 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Consulte o [guia AppAuth](https://openid.github.io/AppAuth-iOS/) para concluir o processo. Se quiser trabalhar logo com o seu aplicativo, veja o [nosso exemplo](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga as etapas em [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para inserir sua própria configuração do Azure AD B2C.

Estamos sempre abertos a comentários e sugestões! Caso você tenha alguma dúvida sobre este tópico ou recomendações para melhorar o conteúdo, agradecemos seus comentários na parte inferior da página. Para solicitações de recursos, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
