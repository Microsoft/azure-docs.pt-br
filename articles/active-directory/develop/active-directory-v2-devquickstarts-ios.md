---
title: Adicionar entrada a um aplicativo iOS usando o ponto de extremidade do Azure AD v2.0 | Microsoft Docs
description: "Como criar um aplicativo iOS que conecte usuários com a conta pessoal e as contas corporativas ou de estudante da Microsoft usando bibliotecas de terceiros."
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf1455dc3d55ea3581195f7a315556d134c23a26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adicionar entrada a um aplicativo iOS usando uma biblioteca de terceiros com a API do Graph usando o ponto de extremidade v2.0
A plataforma de identidade da Microsoft usa padrões abertos, como OAuth2 e OpenID Connect. Os desenvolvedores podem usar qualquer biblioteca desejada para integrar aos nossos serviços. Para ajudar os desenvolvedores a usar nossa plataforma com outras bibliotecas, escrevemos alguns guias passo a passo como este para demonstrar como configurar bibliotecas de terceiros que se conectam à plataforma de identidade da Microsoft. A maioria das bibliotecas que implementa [a especificação RFC6749 do OAuth2](https://tools.ietf.org/html/rfc6749) pode se conectar à plataforma de identidade da Microsoft.

Com o aplicativo que esse passo a passo cria, os usuários podem entrar na respectiva organização e pesquisar outros na organização usando a API do Graph.

Se ainda não conhece o OAuth2 ou o OpenID Connect, grande parte desta configuração de exemplo pode não fazer muito sentido para você. Recomendamos ler o artigo [Protocolos v 2.0 – Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md) para saber mais.

> [!NOTE]
> Alguns recursos da nossa plataforma que possuem uma expressão nos padrões OAuth2 ou OpenID Connect, como o Acesso Condicional e o gerenciamento de políticas do Intune, exigem que você use nossas Bibliotecas de Identidade do Microsoft Azure de software livre.
> 
> 

O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory.

> [!NOTE]
> Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Baixar o código do GitHub
O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Você também pode apenas baixar o exemplo e começar imediatamente:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrar um aplicativo
Crie um novo aplicativo no [Portal de registro de aplicativos](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga as etapas detalhadas em [Como registrar um aplicativo com o ponto de extremidade v 2.0](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** atribuída ao seu aplicativo, pois você precisará dela em breve.
* Adicione a plataforma **Móvel** de seu aplicativo.
* Copiar o **URI de redirecionamento** do portal. Você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Baixar a biblioteca de terceiros NXOAuth2 e criar um espaço de trabalho
Neste passo a passo, você usará o OAuth2Client do GitHub, que é uma biblioteca do OAuth2 para Mac OS X e iOS (Cocoa e Cocoa touch). Essa biblioteca baseia-se no rascunho 10 da especificação OAuth2. Ela implementa o perfil de aplicativo nativo e dá suporte ao ponto de extremidade de autorização do usuário. Isso é tudo de que você precisa para a integração à plataforma de identidade da Microsoft.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Adicionar a biblioteca ao seu projeto usando o CocoaPods
O CocoaPods é um gerenciador de dependência para projetos do Xcode. Ele gerencia automaticamente as etapas de instalação anteriores.

```
$ vi Podfile
```
1. Adicione o seguinte a esse podfile:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Carregue o podfile usando o CocoaPods. Isso criará um novo espaço de trabalho Xcode que será carregado.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Explorar a estrutura do projeto
Temos a seguinte estrutura configurada para nosso projeto no esqueleto:

* Um modo de exibição mestre com uma pesquisa UPN
* Uma Exibição de Detalhes para os dados sobre o usuário selecionado
* Um Modo de Exibição de Logon onde um usuário pode se conectar ao aplicativo para consultar o gráfico

Passaremos por diversos arquivos no esqueleto para adicionar autenticação. Outras partes do código, como o código visual, não pertencem à identidade, mas são fornecidas para você.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configurar o arquivo settings.plst na biblioteca
* No projeto Início rápido, abra o arquivo `settings.plist` . Substitua os valores dos elementos na seção para refletir os valores que você usou no Portal do Azure. Seu código fará referência a esses valores sempre que ele usar a Biblioteca de Autenticação do Active Directory.
  * O `clientId` é a ID do cliente do seu aplicativo que você copiou do portal.
  * O `redirectUri` é a URL de redirecionamento fornecida pelo portal.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configurar a biblioteca NXOAuth2Client no seu LoginViewController
A biblioteca NXOAuth2Client requer alguns valores para sua configuração. Depois de concluir essa tarefa, você poderá usar o token obtido para chamar a API do Graph. Como `LoginView` será chamado sempre que você precisar autenticar, faz sentido colocar os valores de configuração nesse arquivo.

* Vamos adicionar alguns valores ao arquivo `LoginViewController.m` para definir o contexto para autenticação e autorização. Os detalhes sobre os valores estão abaixo do código.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Vamos examinar os detalhes do código.

A primeira cadeia de caracteres é para `scopes`.  O valor `User.Read` permite que você leia o perfil básico do usuário conectado.

É possível saber mais sobre todos os escopos disponíveis em [Escopos de permissão do Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh` e `tokenURL`, você deve usar os valores fornecidos anteriormente. Se usar as Bibliotecas de Identidades do Microsoft Azure de software livre, obteremos esses dados para você usando nosso ponto de extremidade de metadados. Fizemos o trabalho pesado de extrair esses valores para você.

O valor `keychain` é o contêiner que a biblioteca NXOAuth2Client usará na criação de um conjunto de chaves para armazenar seus tokens. Se quiser obter SSO (logon único) entre vários aplicativos, você poderá especificar o mesmo conjunto de chaves em cada um dos aplicativos, bem como solicitar o uso desse conjunto de chaves em seus direitos de XCode. Isso é explicado na documentação da Apple.

O restante desses valores é necessário para usar a biblioteca e criar locais para transmitir valores ao contexto.

### <a name="create-a-url-cache"></a>Criar um cache de URL
Dentro de `(void)viewDidLoad()`, que sempre é chamado depois que o modo de exibição é carregado, o código a seguir inicia um cache para nosso uso.

Adicione os códigos a seguir:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Criar um Modo de Exibição da Web para conexão
Um Modo de Exibição da Web pode solicitar ao usuário fatores adicionais, como mensagem de texto SMS (se configurada), ou retornar mensagens de erro ao usuário. Aqui, vamos configurar o Modo de Exibição da Web e, posteriormente, escreveremos o código para manipular os retornos de chamada que ocorrerão no Modo de Exibição da Web dos serviços de identidade.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Substituir os métodos do Modo de Exibição da Web para manipular a autenticação
Para informar ao Modo de Exibição da Web o que acontecerá quando um usuário precisa se conectar, conforme discutido anteriormente, você pode colar o código a seguir.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escrever código para manipular o resultado da solicitação de OAuth2
O código a seguir vai manipular a URL de redirecionamento que retorna do Modo de Exibição da Web. Se a autenticação não foi bem-sucedida, o código tentará novamente. Enquanto isso, a biblioteca fornecerá o erro que você pode ver no console ou manipular de modo assíncrono.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configurar o contexto de OAuth (chamado repositório de conta)
Aqui, você pode chamar `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` no repositório de conta compartilhado para cada serviço que deseja que o aplicativo possa acessar. O tipo de conta é uma cadeia de caracteres usada como um identificador para um determinado serviço. Como você está acessando a API do Graph, o código se refere a ele como `"myGraphService"`. Em seguida, configure um observador que informará quando algo mudar no token. Assim que obtiver o token, você retornará o usuário de volta para `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configurar o Modo de Exibição Mestre para pesquisar e exibir os usuários da API do Graph
Um aplicativo MVC (Master-View-Controller) que exibe os dados retornados na grade está além do escopo deste passo a passo, e muitos tutoriais online explicam como criar um. Todo esse código está no arquivo de esqueleto. No entanto, você precisa realizar algumas tarefas nesse aplicativo MVC:

* Interceptar quando um usuário digitar algo no campo de pesquisa
* Fornecer um objeto de dados de volta para o MasterView para poder exibir os resultados na grade

Faremos isso a seguir.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Adicionar uma verificação para ver se você se conectou
O aplicativo fará muito pouco se o usuário não estiver conectado. Portanto, é uma boa ideia verificar se já há um token no cache. Caso contrário, redirecione para o Modo de Exibição de Logon para que o usuário se conecte. Como você sabe, a melhor maneira de realizar ações quando um modo de exibição é carregado é usar o método `viewDidLoad()` fornecido pela Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Atualizar o Modo de Exibição de Tabela após o recebimento de dados
Quando a API do Graph retorna dados, você precisa exibi-los. Para simplificar, aqui está todo o código para atualizar a tabela. Você pode simplesmente colar os valores corretos em seu código de texto clichê do MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Fornecer uma maneira de chamar a API do Graph quando alguém digitar no campo de pesquisa
Quando um usuário digita uma pesquisa na caixa, é preciso transmitir isso para a API do Graph. A classe `GraphAPICaller` , que você criará no código a seguir, separa a funcionalidade de pesquisa da apresentação. Por enquanto, vamos escrever o código que alimenta todos os caracteres de pesquisa na API do Graph. Fazemos isso fornecendo um método chamado `lookupInGraph`, que usa a cadeia de caracteres que queremos pesquisar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escrever uma classe auxiliar para acessar a API do Graph
Essa é a parte principal do nosso aplicativo. Enquanto o restante estava inserindo código no padrão MVC da Apple, aqui vamos escrever código para consultar o gráfico conforme o usuário digita e retornar os dados. O código está abaixo e uma explicação detalhada vem a seguir.

### <a name="create-a-new-objective-c-header-file"></a>Criar um novo arquivo de cabeçalho em Objective-C
Nomeie o arquivo `GraphAPICaller.h`e adicione o código a seguir.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Aqui, você vê que um método especificado usa uma cadeia de caracteres e retorna um completionBlock. Este completionBlock, como você já deve ter adivinhado, atualizará a tabela ao fornecer um objeto com os dados populados em tempo real à medida que o usuário pesquisa.

### <a name="create-a-new-objective-c-file"></a>Criar um novo arquivo Objective-C
Nomeie o arquivo `GraphAPICaller.m`e adicione o método a seguir.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


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

```

Vamos analisar esse método em detalhes.

A parte principal desse código está no método `NXOAuth2Request`, que usa os parâmetros que você já definiu no arquivo settings.plist.

A primeira etapa é construir a chamada correta à da API do Graph. Uma vez que você está chamando `/users`, especifique isso acrescentando-o ao recurso da API do Graph com a versão. Faz sentido colocar tudo em um arquivo de configurações externas, pois eles podem mudar conforme a API evolui.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Em seguida, é preciso especificar parâmetros que você também fornecerá à chamada da API do Graph. É *muito importante* que você não coloque os parâmetros no ponto de extremidade do recurso, já que ele é removido de todos os caracteres fora de conformidade com o URI no tempo de execução. Todo o código de consulta deve ser fornecido nos parâmetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Observe que isso chama um método `convertParamsToDictionary` que você ainda não escreveu. Vamos fazer isso agora no final do arquivo:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Em seguida, vamos usar o método `NXOAuth2Request` para obtermos dados da API no formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Por fim, veremos como é possível retornar os dados para o MasterViewController. Os dados são retornados como serializados e precisam ser desserializados e carregados em um objeto que o MainViewController possa consumir. Para essa finalidade, o esqueleto tem um arquivo `User.m/h` que cria um objeto User. Popule esse objeto User com informações do gráfico.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Execute o exemplo
Se você tiver usado o esqueleto ou seguido junto com o passo a passo, seu aplicativo já poderá ser executado. Inicie o simulador e clique em **Entrar** para usar o aplicativo.

## <a name="get-security-updates-for-our-product"></a>Obter atualizações de segurança para nosso produto
É recomendável obter notificações quando ocorrerem incidentes de segurança visitando a página [Segurança TechCenter](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

