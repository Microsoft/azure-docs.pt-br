---
title: Ponte Android WebView com o SDK do Mobile Engagement Android
description: Descreve como criar uma ponte entre o WebView que executa Javascript e o SDK do Mobile Engagement iOS nativo
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Ponte Android WebView com o SDK do Mobile Engagement Android
> [!div class="op_single_selector"]
> * [Ponte do Android](mobile-engagement-bridge-webview-native-android.md)
> * [Ponte do iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Alguns aplicativos móveis são projetados como um aplicativo híbrido onde o aplicativo foi desenvolvido usando desenvolvimento Android nativo, mas algumas ou todas as telas são renderizadas em uma exibição do Android WebView. Você ainda pode consumir o SDK do Mobile Engagement Android em tais aplicativos e este tutorial descreve como fazer isso. O exemplo de código abaixo baseia-se nesta documentação do Android [aqui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Ela descreve como essa abordagem documentada poderia ser usada para implementar o mesmo para métodos mais usados pelo SDK do Mobile Engagement Android de forma que uma Webview de um aplicativo híbrido também possa iniciar solicitações para controlar eventos, trabalhos, erros, informações do aplicativo enquanto os canaliza pelo nosso SDK do Android. 

1. Em primeiro lugar, você precisa concluir nosso [Tutorial de introdução](mobile-engagement-android-get-started.md) para integrar o SDK do Mobile Engagement Android ao aplicativo híbrido. Depois de fazer isso, seu método `OnCreate` será semelhante ao seguinte.  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. Agora, verifique se seu aplicativo híbrido tem uma tela com WebView. O código para ele será semelhante ao que se segue, em que estamos carregando um arquivo HTML local **Sample.html** no Webview no método `onCreate` da sua tela. 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. Agora crie um arquivo de ponte chamado **WebAppInterface** que cria um wrapper sobre alguns métodos comumente usados do SDK do Mobile Engagement Android usando a abordagem `@JavascriptInterface` descrita na [Documentação do Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. Após criar o arquivo de ponte acima, precisamos garantir que ele esteja associado ao nosso Webview. Para que isso aconteça, você precisa editar o método `SetWebview` para que ele se pareça com o seguinte:
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. No trecho acima, chamamos `addJavascriptInterface` para associar a nossa classe de ponte ao nosso Webview e também criamos um identificador chamado **EngagementJs** para chamar os métodos do arquivo de ponte. 
6. Agora, crie o arquivo a seguir chamado **Sample.html** em seu projeto em uma pasta chamada **ativos** que é carregada no Webview e onde poderemos chamar os métodos a partir do arquivo de ponte.
   
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
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. Observe os seguintes pontos sobre o arquivo HTML acima:
   
   * Ele contém um conjunto de caixas de entrada onde você pode fornecer dados a serem usados como nomes de Evento, Erro, Trabalho, AppInfo. Quando você clica no botão ao lado dele, é feita uma chamada para o Javascript que eventualmente chama os métodos do arquivo de ponte para passar essa chamada para o SDK do Mobile Engagement Android. 
   * Estamos marcando algumas informações estáticas adicionais nos eventos, nos trabalhos e até mesmo nos erros para demonstrar como isso pode ser feito. Essa informação adicional é enviada como uma cadeia de caracteres JSON que, se você olhar o arquivo `WebAppInterface`, é analisada, colocada em um Android `Bundle` e passada juntamente com envios de Eventos, Trabalhos, Erros. 
   * O Trabalho do Mobile Engagement é inicializado com o nome que você especifica na caixa de entrada, executado por 10 segundos e, em seguida, desligado. 
   * Um appinfo ou marca do Mobile Engagement é passada com “customer_name” como a chave estática e com o valor que você inseriu na entrada como o valor da marca. 
8. Execute o aplicativo e você verá o seguinte: Agora, forneça um nome para um evento de teste conforme mostrado a seguir e clique em **Enviar**. 
   
    ![][1]
9. Agora, se você acessar a guia **Monitor** do aplicativo e procurar em **Eventos -> Detalhes**, verá esse evento aparecer junto ao app-info estático que estamos enviando. 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
