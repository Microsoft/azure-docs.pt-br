---
title: Solução de problemas nos Serviços de Comunicação do Azure
description: Saiba como reunir as informações necessárias para solucionar problemas de sua solução de Serviços de Comunicação.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 80db53a5ed8d2edc90bc847578d5df4d603cc437
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107220"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Solução de problemas nos Serviços de Comunicação do Azure

Este documento ajudará você a solucionar problemas que podem ocorrer na solução de Serviços de Comunicação. Se estiver solucionando problemas de SMS, você poderá [habilitar o relatório de entrega com a Grade de Eventos](../quickstarts/telephony-sms/handle-sms-events.md) para capturar os detalhes da entrega do SMS.

## <a name="getting-help"></a>Obtendo ajuda

Incentivamos os desenvolvedores a enviarem perguntas, sugerirem recursos e relatarem problemas. Para ajudar a fazer isso, temos uma [página de suporte dedicado e opções de ajuda](../support.md) que lista as suas opções de suporte.

Para ajudá-lo a solucionar determinados tipos de problemas, você pode ser solicitado a fornecer qualquer uma das seguintes informações:

* **ID MS-CV**: Essa ID é usada para solucionar problemas de chamadas e mensagens.
* **ID da chamada**: Essa ID é usada para identificar chamadas de Serviços de Comunicação.
* **ID da mensagem do SMS**: Essa ID é usada para identificar mensagens SMS.
* **Logs de chamada**: Esses logs contêm informações detalhadas que podem ser usadas para solucionar problemas de chamada e de rede.


## <a name="access-your-ms-cv-id"></a>Acessar sua ID do MS-CV

A ID do MS-CV pode ser acessada configurando o diagnóstico na instância do objeto `clientOptions` ao inicializar os SDKs. O diagnóstico pode ser configurado para qualquer um dos SDKs do Azure, incluindo chat, identidade e chamadas de voz por IP.

### <a name="client-options-example"></a>Exemplo das opções do cliente

Os trechos de código a seguir demonstram a configuração de diagnóstico. Quando os SDKs são usados com o diagnóstico habilitado, os detalhes do diagnóstico serão emitidos para o ouvinte de evento configurado:

# <a name="c"></a>[C#](#tab/csharp)
```
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
```
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Acessar sua ID de chamada

Ao fazer o arquivamento de uma solicitação de suporte por meio do portal do Azure relacionado à chamada de problemas, você pode ser solicitado a fornecer a ID da chamada à qual está se referindo. Isso pode ser acessado por meio do SDK de Chamada:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId)
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods
Log.d(call.getCallId())
```
---

## <a name="access-your-sms-message-id"></a>Acessar sua ID de mensagem de SMS

Para problemas de SMS, você pode coletar a ID da mensagem do objeto de resposta.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Habilitar e acessar os logs de chamadas

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O seguinte código pode ser usado a fim de configurar `AzureLogger` para gerar logs no console usando o SDK do JavaScript:

```javascript
import { AzureLogger } from '@azure/logger';

AzureLogger.verbose = (...args) => { console.info(...args); }
AzureLogger.info = (...args) => { console.info(...args); }
AzureLogger.warning = (...args) => { console.info(...args); }
AzureLogger.error = (...args) => { console.info(...args); }

callClient = new CallClient({logger: AzureLogger});
```

# <a name="ios"></a>[iOS](#tab/ios)

Ao desenvolver para iOS, os logs são armazenados em arquivos `.blog`. Observe que você não pode ver os logs diretamente, pois eles estão criptografados.

Eles podem acessados abrindo o Xcode. Vá para Windows > Dispositivos e Simuladores > Dispositivos. Selecione seu dispositivo. Em Aplicativos Instalados, selecione o aplicativo desejado e clique em "Baixar contêiner".

Isso lhe proporcionará um arquivo `xcappdata`. Clique com o botão direito do mouse sobre esse arquivo e selecione "Mostrar conteúdo do pacote". Em seguida, você verá os arquivos `.blog` que podem ser anexados à solicitação de Suporte do Azure.

# <a name="android"></a>[Android](#tab/android)

Ao desenvolver para Android, os logs são armazenados em arquivos `.blog`. Observe que você não pode ver os logs diretamente, pois eles estão criptografados.

No Android Studio, navegue até o Explorador de Arquivos do Dispositivo selecionando Exibir > Janelas de Ferramentas > Explorador de Arquivos do Dispositivo tanto do simulador quanto do dispositivo. O arquivo `.blog` estará localizado no diretório do aplicativo, que deve ser semelhante a `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. Você pode anexar esse arquivo à sua solicitação de suporte.


---

## <a name="calling-sdk-error-codes"></a>Como chamar códigos de erro do SDK

O SDK de Chamada dos Serviços de Comunicação do Azure usa os códigos de erro a seguir para ajudar você a solucionar problemas de chamada. Esses códigos de erro são expostos por meio da propriedade `call.callEndReason` depois que uma chamada é encerrada.

| Código do erro | Descrição | Ação a ser tomada |
| -------- | ---------------| ---------------|
| 403 | Proibido/Falha na autenticação. | Verifique se o token dos Serviços de Comunicação é válido e se não expirou. |
| 404 | Chamada não encontrada. | Verifique se o número que você está chamando (ou a chamada em que está ingressando) existe. |
| 408 | O controlador de chamada atingiu o tempo limite. | O controlador de chamada atingiu o tempo limite aguardando as mensagens de protocolo dos pontos de extremidade do usuário. Verifique se os clientes estão conectados e disponíveis. |
| 410 | Erro de pilha de mídia local ou infraestrutura de mídia. | Verifique se você está usando o SDK mais recente em um ambiente com suporte. |
| 430 | Não foi possível entregar a mensagem ao aplicativo cliente. | Verifique se o aplicativo cliente está em execução e disponível. |
| 480 | Ponto de extremidade de cliente remoto não registrado. | Verifique se o ponto de extremidade remoto está disponível. |
| 481 | Falha na manipulação da chamada de entrada. | Abra uma solicitação de suporte por meio do portal do Azure. |
| 487 | Chamada cancelada, recusada localmente, encerrada devido a um problema de incompatibilidade de ponto de extremidade ou com falha na geração da oferta de mídia. | Comportamento esperado. |
| 490, 491, 496, 487, 498 | Problemas de rede do ponto de extremidade local. | Verifique sua rede. |
| 500, 503, 504 | Erro de infraestrutura dos Serviços de Comunicação. | Abra uma solicitação de suporte por meio do portal do Azure. |
| 603 | Chamada globalmente recusada pelo participante dos Serviços de Comunicação remota | Comportamento esperado. |

## <a name="related-information"></a>Informações relacionadas
- [Configurar logs e diagnósticos](logging-and-diagnostics.md)
- [Métricas](metrics.md)
