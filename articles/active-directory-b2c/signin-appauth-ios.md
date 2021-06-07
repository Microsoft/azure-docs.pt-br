---
title: Usar o AppAuth em um aplicativo iOS
titleSuffix: Azure AD B2C
description: Como criar um aplicativo iOS que usa o AppAuth com Azure Active Directory B2C para gerenciar identidades de usuário e autenticar usuários.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953297"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: entrar usando um aplicativo iOS

A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. O uso de um protocolo de padrão aberto oferece mais opções de desenvolvedor ao selecionar uma biblioteca para integrar aos nossos serviços. Fornecemos este passo a passo e outros como ele para ajudar os desenvolvedores a escrever aplicativos que se conectam à plataforma Microsoft Identity. A maioria das bibliotecas que implementam [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) pode conectar-se à plataforma Microsoft Identity.

> [!WARNING]
> A Microsoft não fornece correções para bibliotecas de terceiros e não as analisou. Esse exemplo usa uma biblioteca de terceiros chamada AppAuth que foi testada com relação à compatibilidade em cenários básicos com o Azure AD B2C. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca. Para obter mais informações, consulte [este artigo](../active-directory/develop/reference-v2-libraries.md).
>
>

Se você ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo poderá não fazer muito sentido para você. Recomendamos que você examine uma breve [visão geral do protocolo que documentamos aqui](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. Se você ainda não tiver um, [crie um diretório B2C](tutorial-create-tenant.md) antes de prosseguir.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, registre um aplicativo no locatário do Azure AD B2C. Isso fornece ao Azure AD as informações de que ele precisa para se comunicar de forma segura com seu aplicativo.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.

Registre também seu URI de Redirecionamento Personalizado para uso em uma etapa posterior. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Criar seus fluxos de usuário
No Azure AD B2C, toda experiência do usuário é definida por um [fluxo de usuário](user-flow-overview.md). Esse aplicativo contém uma experiência de identidade: uma combinação de entrada e inscrição. Ao criar as duas políticas, não se esqueça de:

* Em **Atributos de inscrição**, selecione o atributo **Nome de exibição**.  É possível selecionar outros atributos também.
* Em **Declarações do aplicativo**, selecione as declarações **Nome de exibição** e **ID de Objeto do Usuário**. É possível selecionar outras declarações também.
* Copie o **Nome** de cada usuário após criá-lo. Seu nome de fluxo de usuário é prefixado com `b2c_1_` quando você salva o fluxo de usuário.  Você precisará do nome de fluxo do usuário mais tarde.

Depois de criar os fluxos de usuário, você estará pronto para compilar o aplicativo.

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
* Nome de fluxo do usuário (por exemplo, B2C\_1\_SignUpIn)

O URI do ponto de extremidade de token pode ser gerado substituindo a ID\_do locatário e o nome\_da política na seguinte URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

O URI do ponto de extremidade de autorização pode ser gerado substituindo a ID\_do locatário e o nome\_da política na seguinte URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Execute o seguinte código para criar o objeto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizando

Depois de configurar ou recuperar uma configuração de serviço de autorização, uma solicitação de autorização pode ser criada. Para criar a solicitação, você precisará das seguintes informações:

* ID do Cliente (ID DO APLICATIVO) que você registrou anteriormente. Por exemplo, `00000000-0000-0000-0000-000000000000`.
* URI de Redirecionamento Personalizado que você registrou anteriormente. Por exemplo, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

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
* Na coluna 'Valor', há um campo em branco à esquerda do “Item 0”, abaixo de 'Esquemas de URL'.  Defina o valor com o esquema exclusivo do aplicativo.  O valor deve corresponder ao esquema usado na redirectURL ao criar o objeto OIDAuthorizationRequest.  No exemplo, usa-se o esquema 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Consulte o [guia AppAuth](https://openid.github.io/AppAuth-iOS/) para concluir o processo. Se quiser trabalhar logo com o seu aplicativo, veja o [exemplo](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Siga as etapas em [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) para inserir sua própria configuração do Azure AD B2C.

Estamos sempre abertos a comentários e sugestões! Caso você tenha alguma dúvida sobre este artigo ou recomendações para melhorar o conteúdo, agradecemos seus comentários na parte inferior da página. Para solicitações de recursos, adicione-os ao [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).