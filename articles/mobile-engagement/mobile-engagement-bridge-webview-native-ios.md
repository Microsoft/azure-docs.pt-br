---
title: "Faça a ponte entre o iOS WebView com SDK do Mobile Engagement iOS"
description: Descreve como criar uma ponte entre o WebView com Javascript e o SDK do Mobile Engagement iOS nativo
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Faça a ponte entre o iOS WebView com SDK do Mobile Engagement iOS
> [!div class="op_single_selector"]
> * [Ponte do Android](mobile-engagement-bridge-webview-native-android.md)
> * [Ponte do iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Alguns aplicativos móveis são projetados como um aplicativo híbrido onde o aplicativo foi desenvolvido usando o desenvolvimento do iOS Objective-C nativo, mas algumas ou todas as telas são renderizadas em um WebView iOS. Você ainda pode consumir o SDK do Mobile Engagement iOS em tais aplicativos e este tutorial descreve como fazer isso. 

Há duas abordagens para fazer isso, embora nenhuma esteja documentada:

* A primeira é descrita neste [link](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) e envolve registrar um `UIWebViewDelegate` no modo de exibição da Web e pegar e cancelar imediatamente uma alteração de local feita no Javascript. 
* A segunda se baseia nesta [sessão WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615), essa abordagem é mais simples do que a primeira e é ela que vamos seguir neste guia. Observe que essa abordagem funciona apenas no iOS7 e posterior. 

Siga as etapas abaixo para a ponte de iOS de exemplo:

1. Em primeiro lugar, você precisa concluir nosso [Tutorial de introdução](mobile-engagement-ios-get-started.md) para integrar o SDK do Mobile Engagement iOS ao aplicativo híbrido. Opcionalmente, você também pode habilitar o registro em log de teste da forma indicada a seguir para que possa ver os métodos do SDK conforme vamos disparando-os do Webview. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Agora, verifique se seu aplicativo híbrido tem uma tela com WebView. Você pode adicioná-lo ao `Main.storyboard` do aplicativo. 
3. Associe esse modo de exibição da Web ao seu **ViewController** clicando e arrastando o modo de exibição da Web da Cena de Controlador de Exibição para a tela de edição do `ViewController.h`, colocando-o logo abaixo da linha `@interface`. 
4. Quando você fizer isso, uma caixa de diálogo será exibida solicitando um nome. Forneça o nome como **webView**. Seu arquivo `ViewController.h` deverá ter a seguinte aparência:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Atualizaremos o arquivo `ViewController.m` mais tarde, mas primeiro vamos criar o arquivo de ponte que cria um wrapper sobre alguns métodos comumente usados do SDK do Mobile Engagement iOS. Crie um novo arquivo de cabeçalho chamado **EngagementJsExports.h** que usa o mecanismo do `JSExport` descrito na [sessão](https://developer.apple.com/videos/play/wwdc2013/615) mencionada anteriormente para expor os métodos iOS nativos. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Em seguida, criaremos a segunda parte do arquivo de ponte. Crie um arquivo chamado **EngagementJsExports.m** que conterá a implementação que cria os wrappers reais chamando métodos do SDK do Mobile Engagement iOS. Observe também que estamos analisando os `extras` que são passados do Webview do Javascript e colocando isso em um objeto `NSMutableDictionary` a ser passado com as chamadas de método do SDK do Engagement.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Agora podemos voltar para o **viewcontroller.m** e atualizá-lo com o seguinte código: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Observe os seguintes pontos sobre o arquivo **viewcontroller. M** :
   
   * No método `loadWebView` , estamos carregando um arquivo HTML local chamado **LocalPage.html** cujo código examinaremos em seguida. 
   * No método `webViewDidFinishLoad`, pegamos o `JsContext` e associamos nossa classe de wrapper a ele. Isso permitirá chamar métodos SDK do wrapper usando o identificador **EngagementJs** do webView. 
9. Crie um arquivo chamado **LocalPage.html** com o seguinte código:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Observe os seguintes pontos sobre o arquivo HTML acima:
    
    * Ele contém um conjunto de caixas de entrada onde você pode fornecer dados a serem usados como nomes de Evento, Erro, Trabalho, AppInfo. Quando você clica no botão ao lado dele, é feita uma chamada para o Javascript que eventualmente chama os métodos do arquivo de ponte para passar essa chamada para o SDK do Mobile Engagement iOS. 
    * Estamos marcando algumas informações estáticas adicionais nos eventos, nos trabalhos e até mesmo nos erros para demonstrar como isso pode ser feito. Essa informação adicional é enviada como uma cadeia de caracteres JSON que, se você olhar o arquivo `EngagementJsExports.m` , é analisada e passada juntamente com envios de Eventos, Trabalhos, Erros. 
    * O Trabalho do Mobile Engagement é inicializado com o nome que você especifica na caixa de entrada, executado por 10 segundos e, em seguida, desligado. 
    * Um appinfo ou marca do Mobile Engagement é passada com “customer_name” como a chave estática e com o valor que você inseriu na entrada como o valor da marca. 
11. Execute o aplicativo e você verá o seguinte: Agora, forneça um nome para um evento de teste conforme mostrado a seguir e clique em **Enviar** ao lado. 
    
     ![][1]
12. Agora, se você acessar a guia **Monitor** do aplicativo e procurar em **Eventos -> Detalhes**, verá esse evento aparecer junto ao app-info estático que estamos enviando. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
