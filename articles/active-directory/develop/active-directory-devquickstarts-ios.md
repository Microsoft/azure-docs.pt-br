---
title: Integrar o Azure AD em um aplicativo iOS | Microsoft Docs
description: Como criar um aplicativo para iOS que se integre ao Azure AD para fazer entrar e que chame as APIs protegidas do Azure AD usando o OAuth.
services: active-directory
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 57f465df99ac234466459b8031f61805d8334b59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar o Azure AD em um aplicativo iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Experimente a demonstração do nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/iOS) que ajuda você a executar o Azure Active Directory em apenas alguns minutos!  O portal do desenvolvedor orienta você pelo processo de registro de um aplicativo e integração do Azure AD em seu código.  Quando terminar, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que pode aceitar tokens e executar a validação. 
> 
> 

Para clientes iOS que precisam acessar recursos protegidos, o Azure AD (Azure Active Directory) fornece a biblioteca de autenticação do Active Directory ou ADAL. O ADAL simplifica o processo que seu aplicativo usa para obter tokens de acesso. Para demonstrar como é fácil, criaremos neste guia um aplicativo de lista de tarefas pendentes Objective-C que:

* Obtém tokens de acesso para chamar a API do Graph do Azure AD usando o [protocolo de autenticação OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Pesquisa um diretório para usuários com um determinado alias.

Para criar o aplicativo em funcionamento completo, será necessário:

1. Registrar seu aplicativo no Azure AD.
2. Instalar e configurar a ADAL.
3. Usar a ADAL para obter tokens do AD do Azure.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Você também precisará de um locatário do Azure AD no qual possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).


> [!TIP]
> Experimente a versão de visualização de nosso novo [portal do desenvolvedor](https://identity.microsoft.com/Docs/iOS), que ajudará você a executar o Azure AD em apenas alguns minutos. O portal do desenvolvedor orienta você pelo processo de registro de um aplicativo e integração do Azure AD em seu código. Quando terminar, você terá um aplicativo simples que pode autenticar os usuários em seu locatário e um back-end que pode aceitar tokens e executar a validação. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Determine qual é seu URI de redirecionamento para iOS
Para iniciar com segurança os aplicativos em determinados cenários SSO, você deve criar um *URI de redirecionamento* em um formato específico. Um URI de redirecionamento é usado para garantir que os tokens retornem para o aplicativo correto que os solicitam.


O formato do iOS para um URI de redirecionamento é:

```
<app-scheme>://<bundle-id>
```

* **app-scheme** – isso é registrado no projeto XCode. É como os outros aplicativos podem chamar você. Você pode encontrar isso em Info.plist -> tipos de URL -> identificador de URL. Você deve criar um se você ainda não tiver um ou mais configurados.
* **bundle-id** - esse é o identificador de pacote localizado em "identidade" nas configurações do seu projeto no XCode.

Um exemplo para este código de Início Rápido é: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Registrar o aplicativo DirectorySearcher
Para configurar o aplicativo para obter tokens, primeiro será necessário registrá-lo no seu locatário do Azure AD e conceder permissão para acessar a API do Graph do Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Sob o **diretório** , escolha onde você deseja registrar seu aplicativo de locatário do Active Directory.
3. Clique em **Mais Serviços** no painel de navegação mais à esquerda e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo**e, em seguida, selecione **Adicionar**.
5. Siga os prompts para criar um novo **Aplicativo Cliente Nativo**.
  * O **Nome** do aplicativo descreve o seu aplicativo aos usuários finais.
  * O **URI de redirecionamento** é uma combinação de esquema e de cadeia de caracteres que o Azure AD usa para retornar respostas de tokens.  Insira um valor específico para seu aplicativo e baseado nas informações de URI de redirecionamento anteriores.
6. Depois de você concluir o registro, o Azure AD atribui uma ID do aplicativo exclusiva ao aplicativo.  Você precisará desse valor nas próximas seções, portanto, copie-o da guia do aplicativo.
7. Na página **Configurações**, selecione **Permissões Necessárias** e escolha **Adicionar**. Escolha o **Microsoft Graph** como a API e adicione a permissão **Ler Dados do Diretório** em **Permissões Delegadas**.  Isso permitirá que o aplicativo consulte a API do Graph do Azure AD para os usuários.

## <a name="3-install-and-configure-adal"></a>3. Instalar e configurar a ADAL
Agora que você tem um aplicativo no AD do Azure, você pode instalar a ADAL e escrever seu código relacionado à identidade.  Para que a ADAL possa se comunicar com o Azure AD, é necessário fornecer a ela algumas informações sobre o registro do aplicativo.

1. Inicie adicionando a ADAL ao projeto DirectorySearcher usando o CocoaPods.

    ```
    $ vi Podfile
    ```
2. Adicione o seguinte a esse podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Carregue o podfile usando o CocoaPods. Esta etapa cria um novo espaço de trabalho do XCode que você carrega.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. No projeto do Guia de início rápido, abra o arquivo plist `settings.plist`.  Substitua os valores dos elementos na seção para refletir os valores que você inseriu no Portal do Azure. Seu código faz referência a esses valores sempre que ele usar a ADAL.
  * O `tenant` é o domínio do seu locatário do Azure AD, por exemplo, contoso.onmicrosoft.com.
  * O `clientId` é a ID do cliente do seu aplicativo que você copiou do portal.
  * O `redirectUri` é a URL de redirecionamento que você registrou no portal.

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.    Usar a ADAL para obter tokens do Azure AD
O princípio básico da ADAL é que sempre que seu aplicativo precisar de um token de acesso, ele simplesmente chama um completionBlock `+(void) getToken : `, e a ADAL faz o resto.  

1. No projeto `QuickStart`, abra `GraphAPICaller.m` e localize o comentário `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` perto da parte de cima.  É aqui que você passa à ADAL as coordenadas necessárias, por um CompletionBlock, para se comunicar com o Azure AD e informar a ele como armazenar tokens em cache.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Agora, precisamos usar esse token para pesquisar usuários no gráfico. Encontre o comentário `// TODO: implement SearchUsersList`. Esse método faz uma solicitação GET para que a Graph API do AD do Azure procure por usuários cujo UPN começa com o termo de pesquisa fornecido.  Para consultar a API do Graph do Azure AD, você precisa incluir um access_token no cabeçalho `Authorization` da solicitação. É aí que a ADAL entra em cena.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```


3. Quando o aplicativo solicita um token chamando `getToken(...)`, a ADAL tenta retornar um token sem pedir as credenciais ao usuário.  Se a ADAL determina que o usuário precisa entrar para obter um token, ela exibirá uma caixa de diálogo para entrada, coletará as credenciais do usuário e retornará um token após uma autenticação bem-sucedida.  Se a ADAL não puder retornar um token por qualquer motivo, ela lançará um `AdalException`.

> [!Note] 
> O objeto `AuthenticationResult` contém um objeto `tokenCacheStoreItem` que pode ser usado para coletar as informações de que seu aplicativo pode precisar. No Guia de Início Rápido, `tokenCacheStoreItem` é usado para determinar se a autenticação já ocorreu.
>
>

## <a name="5-build-and-run-the-application"></a>5. Compile e execute o aplicativo
Parabéns! Agora você tem um aplicativo iOS que tem a capacidade de autenticar usuários, chamar APIs Web com segurança usando OAuth 2.0 e obter informações básicas sobre o usuário.  Se você ainda não fez isso, agora é o momento de preencher seu locatário com alguns usuários.  Inicie o aplicativo do Guia de início rápido e entre com um desses usuários.  Procure por outros usuários com base em seus UPNs.  Feche o aplicativo e, em seguida, inicie-o novamente.  Observe que a sessão do usuário permanece intacta.

A ADAL facilita a incorporar todos esses recursos comuns de identidade em seu aplicativo.  Ele se encarrega de todo o trabalho difícil para você, por exemplo, gerenciamento de cache, suporte a protocolo OAuth, apresentação de uma interface do usuário de entrada ao usuário e atualização de tokens expirados.  Tudo o que você realmente precisa saber é uma única chamada à API, `getToken`.

Para referência, o exemplo concluído (sem seus valores de configuração) é fornecido no [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="next-steps"></a>Próximas etapas
Agora você pode passar para cenários de adicionais.  Você pode desejar experimentar:

* [Proteger uma API Web Node.js com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Saiba [como habilitar o SSO entre aplicativos no iOS usando a ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

