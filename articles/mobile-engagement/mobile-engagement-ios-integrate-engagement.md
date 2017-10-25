---
title: "Integração do SDK para iOS do Azure Mobile Engagement | Microsoft Docs"
description: "Atualizações e procedimentos mais recentes para o SDK do iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 01fdbb43c21ac6932e8462f4a6507fc63e50542d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-ios"></a>Como integrar o Engagement no iOS
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

Este procedimento descreve a maneira mais simples de ativar as funções de Analítica e Monitoramento do Engagement em seu aplicativo iOS.

O SDK do Engagement exige iOS7+ e Xcode 8+: o destino da implantação do seu aplicativo deve ter pelo menos o iOS 7.

> [!NOTE]
> Se você realmente depende do XCode 7, pode usar o [SDK do iOS Engagement v3.2.4](https://aka.ms/r6oouh). Há um bug conhecido no módulo de alcance desta versão anterior durante a execução em dispositivos com iOS 10. Consulte [a integração do módulo de alcance](mobile-engagement-ios-integrate-engagement-reach.md) para obter mais detalhes. Caso você opte por usar o SDK v3.2.4, basta ignorar a importação `UserNotifications.framework` na próxima etapa.
>
>

As etapas a seguir são suficientes para ativar o relatório de logs necessários para calcular todas as estatísticas sobre usuários, sessões, atividades, falhas e técnicas. O relatório de logs necessários para calcular outras estatísticas, como Trabalhos, Erros e Eventos deve ser feito manualmente usando a API do Engagement (consulte [How to use the advanced Mobile Engagement tagging API in your iOS app (Como usar a marcação avançada de API do Mobile Engagement no seu aplicativo iOS)](mobile-engagement-ios-use-engagement-api.md) já que essas estatísticas dependem do aplicativo.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Incorporar o SDK do Engagement em seu projeto do iOS
* Baixe o SDK do iOS [daqui](http://aka.ms/qk2rnj).
* Adicione o SDK do Engagement ao seu projeto do iOS: no Xcode, clique com o botão direito do mouse no seu projeto e selecione **"Adicionar arquivos a..."** e escolha a pasta `EngagementSDK`.
* O Engagement exige estruturas adicionais para funcionar: no Explorador de projeto, abra o painel de projeto e selecione o destino correto. Em seguida, abra a guia **"Criar fases"** e no menu **"Vincular Binário com Bibliotecas"**, adicione estas estruturas:

  * `UserNotifications.framework` -defina o link como opcional `Optional`
  * `AdSupport.framework` -defina o link como opcional `Optional`
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> A estrutura AdSupport pode ser removida. O Engagement precisa dessa estrutura para coletar o IDFA. No entanto, a coleção de IDFA pode ser desabilitada \<ios-sdk-engagement-idfa\> para cumprir a nova política Apple em relação a essa ID.
>
>

## <a name="initialize-the-engagement-sdk"></a>Inicialize o SDK do Engagement
Você precisa modificar seu representante de Aplicativo:

* Na parte superior do seu arquivo de implementação, importe o agente do Engagement:

      [...]
      #import "EngagementAgent.h"
* Inicialize o Engagement dentro do método '**applicationDidFinishLaunching:**' ou '**application:didFinishLaunchingWithOptions:**':

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Relatórios básicos
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Método recomendado: sobrecarregar suas classes `UIViewController`
Para ativar o relatório de todos os logs exigidos pelo Engagement para calcular os Usuários, Sessões, Atividades, Falhas e Estatísticas técnicas, você pode simplesmente fazer todas as suas subclasses `UIViewController` herdarem as classes `EngagementViewController` (mesma regra para `UITableViewController` -\> `EngagementTableViewController`).

**Sem o Engagement :**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Com o Engagement :**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: chame `startActivity()` manualmente
Se você não pode ou não quer sobrecarregar as suas classes `UIViewController`, em vez disso, é possível iniciar suas atividades chamando os métodos de `EngagementAgent` diretamente.

> [!IMPORTANT]
> O SDK do iOS chama automaticamente o `endActivity()` método quando o aplicativo é fechado. Desse modo, será *ALTAMENTE* recomendável chamar o método `startActivity` sempre que a atividade do usuário for alterada e *NUNCA* chamar o método `endActivity`, uma vez que chamar esse método faz com que a sessão atual seja encerrada.
>
>

## <a name="location-reporting"></a>Relatórios de local
Os termos de serviço da Apple não permitem que os aplicativos usem o local apenas para fins de estatísticas de acompanhamento. Assim, é recomendável habilitar relatórios locais somente se o seu aplicativo também usar o acompanhamento de local por outro motivo.

A partir do iOS 8, é necessário fornecer uma descrição de como o seu aplicativo usa os serviços de localização, definindo uma cadeia de caracteres para a chave [NSLocationWhenInUseUsageDescription] ou [NSLocationAlwaysUsageDescription] no arquivo Info.plist do seu aplicativo. Se você quiser o local do relatório em segundo plano com o Engagement, adicione a chave NSLocationAlwaysUsageDescription. Em outros casos, adicione a chave NSLocationWhenInUseUsageDescription. Observe que você precisa NSLocationAlwaysAndWhenInUseUsageDescription e NSLocationWhenInUseUsageDescription para relatar o local em segundo plano no iOS 11.

### <a name="lazy-area-location-reporting"></a>Relatórios de local de área lenta
O relatório de local de área lenta permite relatar o país, a região e a localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem pouco impacto (ou quase nenhum) sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach. A última área conhecida relatada para um dispositivo pode ser recuperada graças à [API do dispositivo].

Para habilitar o relatório de local de área lenta, adicione a seguinte linha depois de inicializar o agente de Engagement:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Relatórios de local em tempo real
Os relatórios de local em tempo real permitem relatar a latitude e a longitude associados aos dispositivos. Por padrão, esse tipo de relatório local usa apenas os locais de rede (com base na ID da célula ou WIFI) e o relatório estará disponível apenas quando o aplicativo for executado em primeiro plano (ou seja, durante uma sessão).

Os locais em tempo real *NÃO* são usados para calcular estatísticas. Sua única finalidade é permitir o uso do critério de isolamento geográfico em tempo real \<Reach-Audience-geofencing\> em campanhas de alcance.

Para habilitar o relatório de local em tempo real, adicione a seguinte linha depois de inicializar o agente do Engagement:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>Relatórios com base em GPS
Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), adicione:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Relatório de segundo plano
Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (ou seja, durante uma sessão). Para habilitar o relatório também em segundo plano, adicione:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.
>
>

A implementação dessa função chamará [startMonitoringSignificantLocationChanges] quando o aplicativo entra em segundo plano. Lembre-se de que ele reinicia automaticamente o seu aplicativo em segundo plano caso chegue um novo evento local.

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se você deseja relatar trabalhos, erros e eventos específicos do aplicativo, você precisa usar a API do Engagement por meio dos métodos da classe `EngagementAgent` . Um objeto dessa classe pode ser recuperado chamando o método estático `[EngagementAgent shared]` .

A API do Engagement permite usar todos os recursos avançados do Engagement e é detalhada em Como usar a API do Engagement no iOS (além da documentação técnica da classe `EngagementAgent` ).

## <a name="disable-idfa-collection"></a>Desabilitar a coleta de IDFA
Por padrão, o Engagement usará o [IDFA] para identificar exclusivamente um usuário. Mas se não estiver usando anúncios em outro lugar no aplicativo, você pode ser rejeitado pelo processo de revisão do App Store. A coleção IDFA pode ser desabilitada, adicionando a macro do pré-processador `ENGAGEMENT_DISABLE_IDFA` em seu arquivo pch (ou em `Build Settings` de seu aplicativo). Isso garantirá que não há nenhuma referência a `ASIdentifierManager`, a `advertisingIdentifier` ou a `isAdvertisingTrackingEnabled` na compilação do aplicativo.

Integração no arquivo **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Você pode verificar se a coleção IDFA está corretamente desabilitada em seu aplicativo, verificando os logs de teste do Engagement. Consulte a documentação de teste de integração \<ios-sdk-engagement-test-idfa\> para obter mais informações.

## <a name="disable-log-reporting"></a>Desabilitar o relatório de log
### <a name="using-a-method-call"></a>Usando uma chamada de método
Se desejar que o Engagement pare de enviar logs, você pode chamar:

    [[EngagementAgent shared] setEnabled:NO];

Essa chamada é persistente: usa `NSUserDefaults` para armazenar as informações.

Você pode habilitar o log de relatórios novamente chamando a mesma função com `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integração no pacote de configurações
Em vez de chamar essa função, você também pode integrar essa configuração diretamente no seu arquivo `Settings.bundle` . A cadeia de caracteres `engagement_agent_enabled` deve ser usada como um identificador de preferência e ele deve estar associado a um switch de alternância(`PSToggleSwitchSpecifier`).

O exemplo a seguir de `Settings.bundle` mostra como implementá-lo:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[API do dispositivo]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
