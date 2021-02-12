---
title: Interação humana e tempos limite nas Funções Duráveis – Azure
description: Saiba como lidar com interação humana e tempos limite na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: dd7f8416b2f4520ec8e94c8608f753f7412afc4d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627365"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana nas Funções Duráveis – exemplo de verificação por telefone

Este exemplo demonstra como criar uma orquestração de [Funções Duráveis](durable-functions-overview.md) que envolve interação humana. Sempre que uma pessoa real está envolvida em um processo automatizado, o processo deve ser capaz de enviar notificações para a pessoa e de receber respostas de forma assíncrona. Ele também deve contar com a possibilidade de a pessoa não estar disponível. (É nesta última parte que os tempos limite tornam-se importantes.)

Este exemplo implementa um sistema de verificação por telefone baseado em SMS. Esses tipos de fluxo costumam ser usados para verificar o número de telefone de um cliente ou para MFA (autenticação multifator). É um exemplo poderoso, pois toda a implementação é feita usando algumas funções pequenas. Não é necessário um armazenamento de dados externo, como um banco de dados.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

A verificação por telefone é usada para confirmar a identidade dos usuários finais de seu aplicativo e que eles não são remetentes de spam. A autenticação multifator é um caso de uso comum para proteger contas de usuário contra hackers. O desafio ao implementar sua própria verificação por telefone é que ela requer uma **interação com estado** com uma pessoa. Normalmente, um usuário final recebe um código (por exemplo, um número de 4 dígitos) e deve responder **dentro de um período razoável**.

O Azure Functions comum é sem estado (assim como muitos outros pontos de extremidade de nuvem em outras plataformas), portanto, esses tipos de interações envolvem explicitamente o gerenciamento de estado externamente em um banco de dados ou algum outro armazenamento persistente. Além disso, a interação deve ser dividida em várias funções que possam ser coordenadas juntas. Por exemplo, você precisa de pelo menos uma função para decidir sobre um código, persisti-lo em algum lugar e enviá-lo para o telefone do usuário. Além disso, você precisa de pelo menos uma outra função para receber uma resposta do usuário e, de alguma forma, mapeá-la de volta para a chamada de função original para fazer a validação do código. O tempo limite também é um aspecto importante para garantir a segurança. Ele pode ficar bastante complexo rapidamente.

A complexidade desse cenário é bastante reduzida quando você usa as Funções Duráveis. Como você verá neste exemplo, uma função de orquestrador pode gerenciar a interação com estado facilmente e sem envolver nenhum armazenamento de dados externo. Como as funções de orquestrador são *duráveis*, esses fluxos interativos também são altamente confiáveis.

## <a name="configuring-twilio-integration"></a>Configurando a integração com o Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo aborda as seguintes funções no aplicativo de exemplo:

* `E4_SmsPhoneVerification`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que executa o processo de verificação de telefone, incluindo o gerenciamento de tempos limite e novas tentativas.
* `E4_SendSmsChallenge`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que envia um código por meio de mensagem de texto.

> [!NOTE]
> A `HttpStart` função no [aplicativo de exemplo e o início rápido](#prerequisites) atua como um [cliente de orquestração](durable-functions-bindings.md#orchestration-client) que dispara a função de orquestrador.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification função de orquestrador

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Pode não ser óbvio a princípio, mas esse orquestrador não viola a [restrição de orquestração determinística](durable-functions-code-constraints.md). Ele é determinístico porque a `CurrentUtcDateTime` propriedade é usada para calcular o tempo de expiração do temporizador e retorna o mesmo valor em cada reprodução neste ponto do código do orquestrador. Esse comportamento é importante para garantir que os mesmos `winner` resultados de cada chamada repetida para `Task.WhenAny` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A função **E4_SmsPhoneVerification** usa o *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Este é o código que implementa a função:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Pode não ser óbvio a princípio, mas esse orquestrador não viola a [restrição de orquestração determinística](durable-functions-code-constraints.md). Ele é determinístico porque a `currentUtcDateTime` propriedade é usada para calcular o tempo de expiração do temporizador e retorna o mesmo valor em cada reprodução neste ponto do código do orquestrador. Esse comportamento é importante para garantir que os mesmos `winner` resultados de cada chamada repetida para `context.df.Task.any` .

# <a name="python"></a>[Python](#tab/python)

A função **E4_SmsPhoneVerification** usa o *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Este é o código que implementa a função:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Pode não ser óbvio a princípio, mas esse orquestrador não viola a [restrição de orquestração determinística](durable-functions-code-constraints.md). Ele é determinístico porque a `currentUtcDateTime` propriedade é usada para calcular o tempo de expiração do temporizador e retorna o mesmo valor em cada reprodução neste ponto do código do orquestrador. Esse comportamento é importante para garantir que os mesmos `winner` resultados de cada chamada repetida para `context.df.Task.any` .

---

Uma vez iniciada, essa função de orquestrador faz o seguinte:

1. Obtém um número de telefone para o qual *enviará* a notificação de SMS.
2. Chama **E4_SendSmsChallenge** para enviar uma mensagem SMS para o usuário e retorna o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável que dispara 90 segundos a partir da hora atual.
4. Em paralelo com o temporizador, aguarda um evento **SmsChallengeResponse** do usuário.

O usuário recebe uma mensagem SMS com um código de 4 dígitos. Eles têm de 90 segundos para enviar o mesmo código de quatro dígitos de volta para a instância de função do orquestrador para concluir o processo de verificação. Se enviar o código errado, ele terá mais três tentativas para acertar (na mesma janela de 90 segundos).

> [!WARNING]
> É importante [cancelar os temporizadores](durable-functions-timers.md) se você não precisar mais que eles expirem, como no exemplo acima quando uma resposta ao desafio é aceita.

## <a name="e4_sendsmschallenge-activity-function"></a>Função de atividade de E4_SendSmsChallenge

A função **E4_SendSmsChallenge** usa a associação twilio para enviar a mensagem SMS com o código de quatro dígitos para o usuário final.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Será necessário instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote NuGet para executar o código de exemplo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O *function.json* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

E aqui está o código que gera o código de desafio de quatro dígitos e envia a mensagem SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

O *function.json* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

E aqui está o código que gera o código de desafio de quatro dígitos e envia a mensagem SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Execute o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A função de orquestrador recebe o número de telefone fornecido e envia imediatamente uma mensagem SMS para ele com um código de verificação de 4 dígitos gerado aleatoriamente &mdash;, por exemplo, *2168*. Em seguida, a função espera durante 90 segundos por uma resposta.

Para responder com o código, você pode usar [ `RaiseEventAsync` (.net) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o webhook **sendEventUrl** HTTP post referenciado na resposta 202 acima, substituindo `{eventName}` pelo nome do evento `SmsChallengeResponse` :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se você enviá-lo antes que o temporizador expire, a orquestração será concluída e o campo `output` será definido como `true`, indicando uma verificação bem-sucedida.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar que o temporizador expire ou se inserir o código errado quatro vezes, você poderá consultar o status e ver uma saída de função de orquestração `false`, indicando que houve falha na verificação por telefone.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou alguns dos recursos avançados do Durable Functions, notadamente `WaitForExternalEvent` e `CreateTimer` APIs. Você viu como elas podem ser combinadas com `Task.WaitAny` para implementar um sistema de tempo limite confiável, que geralmente é útil para interagir com pessoas reais. Você pode aprender mais sobre como usar Funções Duráveis lendo uma série de artigos que oferecem uma cobertura aprofundada de tópicos específicos.

> [!div class="nextstepaction"]
> [Ir para o primeiro artigo na série](durable-functions-bindings.md)
