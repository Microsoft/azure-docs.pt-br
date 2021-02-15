---
title: Assistentes de voz no Windows-diretrizes de implementação de bloqueio acima
titleSuffix: Azure Cognitive Services
description: As instruções para implementar ativação de voz e recursos de bloqueio acima para um aplicativo de agente de voz.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939859"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementando assistentes de voz no Windows

Este guia percorre detalhes importantes de implementação para criar um assistente de voz no Windows.

## <a name="implementing-voice-activation"></a>Implementando ativação de voz

Depois de [configurar seu ambiente](how-to-windows-voice-assistants-get-started.md) e aprender [como a ativação de voz funciona](windows-voice-assistants-overview.md#how-does-voice-activation-work), você pode começar a implementar a ativação de voz para seu próprio aplicativo de assistente de voz.

### <a name="registration"></a>Registro

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Verifique se o microfone está disponível e acessível e, em seguida, monitore seu estado

A MVA precisa de um microfone para estar presente e acessível para poder detectar uma ativação de voz. Use as classes [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)e [MediaCapture](/uwp/api/windows.media.capture.mediacapture) para verificar o acesso de privacidade do microfone, a presença do dispositivo e o status do dispositivo (como volume e mudo), respectivamente.

### <a name="register-the-application-with-the-background-service"></a>Registrar o aplicativo com o serviço em segundo plano

Para que a MVA inicie o aplicativo em segundo plano, o aplicativo precisa ser registrado com o serviço em segundo plano. Veja um guia completo para o registro de serviço em segundo plano [aqui](/windows/uwp/launch-resume/register-a-background-task).

### <a name="unlock-the-limited-access-feature"></a>Desbloquear o recurso de acesso limitado

Use sua chave de recurso de acesso limitado fornecida pela Microsoft para desbloquear o recurso Assistente de voz. Use a classe [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) do SDK do Windows para fazer isso.

### <a name="register-the-keyword-for-the-application"></a>Registrar a palavra-chave para o aplicativo

O aplicativo precisa se registrar, seu modelo de palavra-chave e seu idioma com o Windows.

Comece recuperando o detector de palavra-chave. Neste código de exemplo, recuperamos o primeiro detector, mas você pode selecionar um detector específico selecionando-o em `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Depois de recuperar o objeto ActivationSignalDetector, chame seu `ActivationSignalDetector.CreateConfigurationAsync` método com a ID do sinal, a ID do modelo e o nome de exibição para registrar sua palavra-chave e recuperar o seu aplicativo `ActivationSignalDetectionConfiguration` . As IDs de sinal e modelo devem ser GUIDs decididas pelo desenvolvedor e permanecem consistentes para a mesma palavra-chave.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Verifique se a configuração ativação de voz está habilitada

Para usar a ativação de voz, um usuário precisa habilitar a ativação de voz para seu sistema e habilitar a ativação de voz para seu aplicativo. Você pode encontrar a configuração em "configurações de privacidade de ativação de voz" nas configurações do Windows. Para verificar o status da configuração de ativação de voz em seu aplicativo, use a instância do do `ActivationSignalDetectionConfiguration` registrando a palavra-chave. O campo [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) no `ActivationSignalDetectionConfiguration` contém um valor de enumeração que descreve o estado da configuração de ativação de voz.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Recuperar um ConversationalAgentSession para registrar o aplicativo no sistema MVA

O `ConversationalAgentSession` é uma classe no SDK do Windows que permite ao seu aplicativo atualizar o Windows com o estado do aplicativo (ocioso, detectar, ouvir, trabalhar, falar) e receber eventos, como detecção de ativação e alterações de estado do sistema, como o bloqueio de tela. A recuperação de uma instância do AgentSession também serve para registrar o aplicativo com o Windows como ativável por voz. É uma prática recomendada manter uma referência para o `ConversationalAgentSession` . Para recuperar a sessão, use a `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Ouça os dois sinais de ativação: OnBackgroundActivated e OnSignalDetected

O Windows sinalizará seu aplicativo quando detectar uma palavra-chave de uma das duas maneiras. Se o aplicativo não estiver ativo (ou seja, se você não tiver uma referência a uma instância não descartada do `ConversationalAgentSession` ), ele iniciará seu aplicativo e chamará o método OnBackgroundActivated no arquivo app.XAML.cs do seu aplicativo. Se o campo argumentos do evento `BackgroundActivatedEventArgs.TaskInstance.Task.Name` corresponder à cadeia de caracteres "AgentBackgroundTrigger", a inicialização do aplicativo foi disparada pela ativação de voz. O aplicativo precisa substituir esse método e recuperar uma instância de ConversationalAgentSession para sinalizar para o Windows que agora está ativo. Quando o aplicativo estiver ativo, o Windows sinalizará a ocorrência de uma ativação de voz usando o `ConversationalAgentSession.OnSignalDetected` evento. Adicione um manipulador de eventos a esse evento assim que você recuperar o `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Verificação de palavra-chave

Depois que um aplicativo de agente de voz é ativado por voz, a próxima etapa é verificar se a detecção de palavra-chave era válida. O Windows não fornece uma solução para verificação de palavra-chave, mas permite que os assistentes de voz acessem o áudio da ativação hipotética e concluam a verificação por conta própria.

### <a name="retrieve-activation-audio"></a>Recuperar áudio de ativação

Crie um [AudioGraph](/uwp/api/windows.media.audio.audiograph) e passe-o para o `CreateAudioDeviceInputNodeAsync` do `ConversationalAgentSession` . Isso carregará o buffer de áudio do grafo com o áudio *inicial de aproximadamente 3 segundos antes de a palavra-chave ser detectada*. Esse áudio líder adicional é incluído para acomodar uma ampla gama de comprimentos de palavra-chave e velocidades do palestrante. Em seguida, manipule o evento [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) do grafo de áudio para recuperar os dados de áudio.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Observação: o áudio à esquerda incluído no buffer de áudio pode causar falha na verificação da palavra-chave. Para corrigir esse problema, corte o início do buffer de áudio antes de enviar o áudio para verificação de palavra-chave. Esse corte inicial deve ser personalizado para cada assistente.

### <a name="launch-in-the-foreground"></a>Iniciar em primeiro plano

Quando a verificação de palavra-chave for realizada com sucesso, o aplicativo precisará mudar para o primeiro plano para exibir a interface do usuário. Chame a `ConversationalAgentSession.RequestForegroundActivationAsync` API para mover seu aplicativo para o primeiro plano.

### <a name="transition-from-compact-view-to-full-view"></a>Transição da exibição compacta para a exibição completa

Quando seu aplicativo é ativado pela primeira vez por voz, ele é iniciado em uma exibição compacta. Leia as [diretrizes de design para a visualização de ativação de voz](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) para obter orientação sobre as diferentes exibições e transições entre elas para assistentes de voz no Windows.

Para fazer a transição da exibição compacta para a exibição de aplicativo completa, use a API ApplicationView `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementando a ativação de bloqueio acima

As etapas a seguir abordam os requisitos para permitir que um assistente de voz no Windows seja executado acima do bloqueio, incluindo referências a código de exemplo e diretrizes para gerenciar o ciclo de vida do aplicativo.

Para obter orientação sobre como criar experiências de bloqueio acima, visite o [Guia de práticas recomendadas](windows-voice-assistants-best-practices.md).

Quando um aplicativo mostra uma exibição acima do bloqueio, é considerado que ele está no "modo de quiosque". Para obter mais informações sobre como implementar um aplicativo que usa o modo de quiosque, consulte a [documentação do modo de quiosque](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Transição acima do bloqueio

Uma ativação acima do bloqueio é semelhante a uma ativação abaixo do bloqueio. Se não houver nenhuma instância ativa do aplicativo, uma nova instância será iniciada em segundo plano e `OnBackgroundActivated` em app.XAML.cs será chamado. Se houver uma instância do aplicativo, essa instância receberá uma notificação por meio do `ConversationalAgentSession.SignalDetected` evento.

Se o aplicativo ainda não estiver sendo exibido acima do bloqueio, ele deverá chamar `ConversationalAgentSession.RequestForegroundActivationAsync` . Isso dispara o `OnLaunched` método em app.XAML.cs, que deve navegar até a exibição que será mostrada acima do bloqueio.

### <a name="detecting-lock-screen-transitions"></a>Detectando transições da tela de bloqueio

A biblioteca ConversationalAgent no SDK do Windows fornece uma API para fazer o estado da tela de bloqueio e as alterações no estado da tela de bloqueio facilmente acessíveis. Para detectar o estado atual da tela de bloqueio, verifique o `ConversationalAgentSession.IsUserAuthenticated` campo. Para detectar alterações no estado de bloqueio, adicione um manipulador de eventos ao `ConversationalAgentSession` evento do objeto `SystemStateChanged` . Ele será acionado sempre que a tela for alterada de desbloqueado para bloqueado ou vice-versa. Se o valor dos argumentos do evento for `ConversationalAgentSystemStateChangeType.UserAuthentication` , o estado da tela de bloqueio será alterado.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Detectando a preferência de usuário de ativação de bloqueio acima

A entrada de aplicativo na página de configurações de privacidade de ativação de voz tem uma alternância para a funcionalidade de bloqueio acima. Para que seu aplicativo seja capaz de iniciar acima do bloqueio, o usuário precisará ativar essa configuração. O status das permissões de bloqueio acima também é armazenado no objeto ActivationSignalDetectionConfiguration. O status de AvailabilityInfo. HasLockScreenPermission reflete se o usuário recebeu a permissão de bloqueio acima. Se essa configuração estiver desabilitada, um aplicativo de voz poderá solicitar que o usuário navegue até a página de configurações apropriada no link "MS-Settings: privacy-voiceactivation" com instruções sobre como habilitar a ativação de bloqueio acima para o aplicativo.

## <a name="closing-the-application"></a>Fechando o aplicativo

Para fechar corretamente o aplicativo programaticamente enquanto estiver acima ou abaixo do bloqueio, use a `WindowService.CloseWindow()` API. Isso dispara todos os métodos de ciclo de vida UWP, incluindo onsuspend, permitindo que o aplicativo descarte sua `ConversationalAgentSession` instância antes de fechar.

> [!NOTE]
> O aplicativo pode fechar sem fechar a [instância de bloqueio abaixo](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). Nesse caso, o modo de exibição de bloqueio acima precisa "limpar", garantindo que, quando a tela for desbloqueada, não haja manipuladores de eventos ou tarefas que tentem manipular a exibição de bloqueio acima.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visite o aplicativo de exemplo do assistente de voz UWP para obter exemplos e passo a passo de código](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
