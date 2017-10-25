---
title: "Integração do SDK do Windows Phone Silverlight para o Reach"
description: Como integrar o Azure Mobile Engagement com aplicativos do Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0738f33df94d14fbb393bfaaf09e94c6560213cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Integração do SDK do Windows Phone Silverlight para o Reach
Você deve seguir o procedimento de integração descrito na [Integração do SDK do Engagement do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) , antes de seguir este guia.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorpore o SDK do Engagement Reach em seu projeto do Windows Phone Silverlight
Você não tem nada a adicionar. `EngagementReach` já estão em seu projeto.

> [!TIP]
> Você pode personalizar imagens localizadas na pasta `Resources` do seu projeto, especialmente o ícone de marca (esse padrão para o ícone do Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Adicione recursos
O SDK do Engagement Reach precisa de alguns recursos adicionais.

Abra o seu arquivo `WMAppManifest.xml` e certifique-se de que os seguintes recursos estão declarados:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

O primeiro deles é usado pelo serviço MPNS para permitir a exibição de notificação do sistema. O segundo é usado para inserir uma tarefa de navegador no SDK.

Editar o arquivo `WMAppManifest.xml` e adicione dentro da marca `<Capabilities />` :

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Habilitar o serviço de notificação por push da Microsoft
Para usar o **Serviço de Notificação por Push da Microsoft** (chamado MPNS), seu arquivo `WMAppManifest.xml` deverá ter uma marca `<App />` com um atributo `Publisher` definido como o nome do seu projeto.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar o SDK do Engagement Reach
### <a name="engagement-configuration"></a>Configuração do Engagement
A configuração do Engagement está centralizada no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar a configuração de alcance:

* *Opcional*, indique se o push nativo (MPNS) está ativado ou não entre as marcas `<enableNativePush>` e `</enableNativePush>`, (`true` por padrão).
* *Opcional*, indique o nome do canal push entre as marcas `<channelName>` e `</channelName>`, forneça o mesmo que seu aplicativo possa usar no momento ou deixo-o vazio.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Você pode especificar o nome do canal de notificação MPNS do seu aplicativo. Por padrão, o Engagement cria um nome baseado na appId. Você não precisa especificar o nome, exceto se você planeja usar o canal de notificação fora do Engagement.
> 
> 

### <a name="engagement-initialization"></a>Inicialização do Engagement
Modifique o `App.xaml.cs`:

* Adicione às suas instruções `using`:
  
      using Microsoft.Azure.Engagement;
* Insira `EngagementReach.Instance.Init` logo após `EngagementAgent.Instance.Init` e `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Insira `EngagementReach.Instance.OnActivated` no método `Application_Activated` :
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> O `EngagementReach.Instance.Init` é executado em um thread dedicado. Você não precisa fazê-lo.
> 
> 

## <a name="app-store-submission-considerations"></a>Considerações de envio de armazenamento de aplicativo
A Microsoft impõe algumas regras ao usar as notificações de push:

Na documentação de [Políticas de aplicativo] da Microsoft, seção 2.9:

1) Você deve perguntar ao usuário para aceitar para receber notificações por push. Em seguida, em suas configurações, adicione uma forma de desativar as notificações de envio.

O objeto EngagementReach fornece dois métodos para gerenciar o opt-in/opt-out, `EnableNativePush()` e `DisableNativePush()`. Você pode, por exemplo, criar uma opção nas configurações com uma alternância para desabilitar ou habilitar o MPNS.

Também é possível decidir desativar o MPNS por meio da configuração do Engajamento \<windows-phone-sdk-reach-configuration\>.

> 2.9.1) O aplicativo deve primeiro descrever as notificações a serem fornecidas e **obter a permissão expressa do usuário (opt-in)** e **deve fornecer um mecanismo pelo qual o usuário pode optar por parar de receber notificações por push**. Todas as notificações fornecidas usando o Serviço de Notificação por Push da Microsoft devem ser consistentes com a descrição fornecida ao usuário e devem estar em conformidade com todas as [Políticas de aplicativo][Content Policies] e [Requisitos adicionais para tipos específicos de aplicativo] aplicáveis.
> 
> 

2) Você não deve usar muitas notificações por push. O Engagement manipulará notificações para você.

> 2.9.2) O aplicativo e o uso do Serviço de notificação por push da Microsoft não devem usar excessivamente a capacidade de rede ou de largura de banda do serviço de notificação por push da Microsoft, ou caso contrário indevidamente sobrecarregam o Windows Phone ou outros dispositivos Microsoft ou serviço com notificações por push excessivo, conforme determinado pela Microsoft a seu critério lógico e não devem prejudicar ou interferir com nenhuma rede Microsoft ou servidores ou quaisquer servidores de terceiros ou redes conectadas ao do serviço de notificação por push da Microsoft.
> 
> 

3) Não confie no MPNS para enviar informações muito sérias. O Engagement usa o MPNS, portanto, essa regra também se aplica para campanhas criadas dentro do projeto front-end.

> 2.9.3) O serviço de notificação por push da Microsoft não pode ser usado para enviar notificações que são de missão crítica ou que poderiam afetar assuntos de vida ou morte, incluindo, sem limitação, relacionadas a um dispositivo médico ou condição de notificações críticas. A MICROSOFT EXPRESSAMENTE SE ISENTA DE QUAISQUER GARANTIAS DE QUE O USO DO SERVIÇO DE NOTIFICAÇÃO POR PUSH OU ENVIO DE NOTIFICAÇÕES DO SERVIÇO DE NOTIFICAÇÃO POR PUSH DA MICROSOFT SERÁ ININTERRUPTO, SEM ERRO, OU CASO CONTRÁRIO GARANTIDO QUE OCORRA EM TEMPO REAL.
> 
> 

**Não podemos garantir que seu aplicativo passará o processo de validação se você não respeita essas recomendações.**

## <a name="handle-data-push-optional"></a>Manipular o push de dados (opcional)
Se desejar que o aplicativo seja capaz de receber push de dados do Reach, você precisa implementar dois eventos da classe EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Você pode ver que o retorno de chamada de cada método retorna um valor booleano. O Engagement envia um comentário ao seu back-end após distribuir o push de dados. Se o retorno de chamada retorna false, o comentário `exit` será enviado. Caso contrário, ele será `action`. Se nenhum retorno de chamada for definido para os eventos, o comentário `drop` retornará ao Engagement.

> [!WARNING]
> O Engagement não é capaz de receber múltiplos comentários para um push de dados. Se você pretende definir vários manipuladores em um evento, lembre-se de que os comentários corresponderá ao último enviado. Nesse caso, é recomendável sempre retornar o mesmo valor para evitar que os comentários sejam confusos no front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizar a interface do usuário (opcional)
### <a name="first-step"></a>Primeira etapa
Permitir que você personalize a interface do usuário do reach.

Para fazer isso, você precisa criar uma subclasse da classe `EngagementReachHandler` .

**Exemplo de código :**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Depois, defina o conteúdo do campo `EngagementReach.Instance.Handler` com seu objeto personalizado em sua classe `App.xaml.cs` dentro do método `Application_Launching`.

**Exemplo de código :**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Por padrão, o Engagement usa a sua própria implementação de `EngagementReachHandler`. Você não precisa criar sua própria e se você fizer isso, você não precisa substituir cada método. O comportamento padrão é selecionar o objeto base do Engagement.
> 
> 

### <a name="layouts"></a>Layouts
Por padrão, o Reach usará os recursos incorporados da DLL para exibir as notificações e páginas.

No entanto, você pode optar por usar seus próprios recursos para refletir sua marca desses componentes.

Você pode substituir os métodos `EngagementReachHandler` em sua subclasse para informar o Engagement para usar seus layouts:

**Exemplo de código :**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> O método `CreateNotification` pode retornar nulo. A notificação não será exibida e a campanha de alcance será descartada.
> 
> 

Para simplificar a implementação de layout, também fornecemos nossa própria xaml que pode servir como base para o seu código. Eles estão localizados no arquivo do SDK do Engagement (/src/reach/).

> [!WARNING]
> As fontes que fornecemos são exatamente as mesmas que usamos. Portanto, se você quiser modificá-las diretamente, não se esqueça de alterar o namespace e o nome.
> 
> 

### <a name="notification-position"></a>Posição de notificação
Por padrão, uma notificação no aplicativo é exibida na parte inferior esquerda do aplicativo. Você pode alterar esse comportamento, substituindo o método `GetNotificationPosition` do objeto `EngagementReachHandler`.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

No momento, você pode escolher entre as posições `BOTTOM`(padrão) e `TOP`.

### <a name="launch-message"></a>Iniciar mensagem
Quando um usuário clica em um sistema de notificação (um toast), o Engagement inicia o aplicativo, carrega o conteúdo das mensagens de envio por push e exibe a página da campanha correspondente.

Há um atraso entre a inicialização do aplicativo e a exibição da página (dependendo da velocidade da sua rede).

Para indicar ao usuário que algo está sendo carregado, você deve fornecer informações visuais, como uma barra de progresso ou um indicador de progresso. O Engagement não pode manipular isto por conta própria, mas oferece alguns manipuladores para você.

Para implementar o retorno de chamada, execute:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Você pode definir o retorno de chamada em seu método `Application_Launching` de seu arquivo `App.xaml.cs`, de preferência antes da chamada de `EngagementReach.Instance.Init()`.

> [!TIP]
> Cada manipulador é chamado pelo Thread de interface do usuário. Você não precisa se preocupar ao usar uma MessageBox ou algo relacionado à interface do usuário.
> 
> 

[Políticas de aplicativo]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Requisitos adicionais para tipos específicos de aplicativo]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

