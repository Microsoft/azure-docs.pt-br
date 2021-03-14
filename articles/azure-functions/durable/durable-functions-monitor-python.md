---
title: Monitores no Durable Functions (Python) – Azure
description: Saiba como implementar um monitor de status usando a extensão de Durable Functions para Azure Functions (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434756"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Cenário de monitor no Durable Functions-exemplo de monitoramento de problemas do GitHub

O padrão de monitoramento refere-se a um processo recorrente flexível em um fluxo de trabalho - por exemplo, sondagem até que determinadas condições sejam atendidas. Este artigo explica um exemplo que usa as Funções Duráveis para implementar o monitoramento.

## <a name="prerequisites"></a>Pré-requisitos

* [Concluir o artigo de início rápido](quickstart-python-vscode.md)
* [Clonar ou baixar o projeto de exemplos do GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Visão geral do cenário

Este exemplo monitora a contagem de problemas em um repositório GitHub e alerta o usuário se houver mais de três problemas abertos. Você pode usar uma função disparada por temporizador regular para solicitar as contagens de problemas abertas em intervalos regulares. No entanto, um problema com essa abordagem é o **gerenciamento do tempo de vida**. Se apenas um alerta for enviado, o monitor precisará se desabilitá-lo depois que 3 ou mais problemas forem detectados. O padrão de monitoramento pode encerrar sua própria execução, entre outros benefícios:

* Os monitores são executados em intervalos, não em agendamentos: um disparador com timer *executa* a cada hora; um monitor *aguarda* uma hora entre as ações. As ações de um monitor não se sobrepõem, exceto se especificado, o que pode ser importante para tarefas de execução longa.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Os monitores poderão terminar quando alguma condição for atendida ou terminada por outro processo.
* Monitores podem receber parâmetros. O exemplo mostra como o mesmo processo de monitoramento de repositório pode ser aplicado a qualquer repositório e número de telefone do GitHub público solicitado.
* Monitores são escalonáveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem a necessidade de criar novas funções ou definir mais código.
* Os monitores integram-se facilmente em fluxos de trabalho maiores. Um monitor pode ser uma seção de uma função de orquestração mais complexa ou uma [sub-orquestração](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Configuração

### <a name="configuring-twilio-integration"></a>Configurando a integração com o Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E3_Monitor`: Uma [função de orquestrador](durable-functions-bindings.md#orchestration-trigger) que chama `E3_TooManyOpenIssues` periodicamente. Ele chama `E3_SendAlert` se o valor de retorno de `E3_TooManyOpenIssues` é `True` .
* `E3_TooManyOpenIssues`: Uma [função de atividade](durable-functions-bindings.md#activity-trigger) que verifica se um repositório tem muitos problemas abertos. Para fins de demonstração, consideramos que mais de três problemas abertos são muitos.
* `E3_SendAlert`: uma função de atividade que envia uma mensagem SMS via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor função de orquestrador


A função **E3_Monitor** usa *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Este é o código que implementa a função:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Essa função de orquestrador executa as ações a seguir:

1. Obtém o *repositório* para monitorar e o *número de telefone* para o qual ele enviará uma notificação de SMS.
2. Determina o tempo de expiração do monitor. O exemplo usa um valor embutido em código para brevidade.
3. Chama **E3_TooManyOpenIssues** para determinar se há muitos problemas abertos no repositório solicitado.
4. Se houver muitos problemas, o chamará **E3_SendAlert** para enviar uma notificação de SMS para o número de telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de sondagem. O exemplo usa um valor embutido em código para brevidade.
6. Continua em execução até que a hora UTC atual passe o tempo de expiração do monitor ou um alerta SMS seja enviado.

Várias instâncias de orquestrador podem ser executadas simultaneamente chamando a função de orquestrador várias vezes. O repositório a ser monitorado e o número de telefone para o qual enviar um alerta do SMS pode ser especificado. Por fim, observe que a função de orquestrador *não* está em execução enquanto aguarda o timer, portanto, você não será cobrado por ela.


### <a name="e3_toomanyopenissues-activity-function"></a>Função de atividade de E3_TooManyOpenIssues

Como com outros exemplo, as funções de atividade auxiliares são funções regulares que usam a associação de gatilho `activityTrigger`. A função **E3_TooManyOpenIssues** Obtém uma lista de problemas abertos no momento no repositório e determina se há "muitos" deles: mais de 3 de acordo com nosso exemplo.


O *function.json* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

E aqui está a implementação.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>Função de atividade de E3_SendAlert

A função **E3_SendAlert** usa a associação twilio para enviar uma mensagem SMS notificando o usuário final de que há pelo menos três problemas abertos aguardando uma resolução.


O *function.json* é simples:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

E aqui está o código que envia a mensagem SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Execute o exemplo

Você precisará de uma conta do [GitHub](https://github.com/) . Com ele, crie um repositório público temporário no qual você pode abrir problemas.

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Por exemplo, se o nome de usuário do GitHub for `foo` e seu repositório for `bar` , seu valor `"repo"` deverá ser `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A instância **E3_Monitor** inicia e consulta o repositório fornecido para problemas em aberto. Se pelo menos 3 problemas abertos forem encontrados, ele chamará uma função de atividade para enviar um alerta; caso contrário, ele definirá um temporizador. Quando o temporizador expirar, a orquestração será retomada.

É possível visualizar a atividade da orquestração, observando os logs de função no Portal do Azure Functions.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

A orquestração será concluída quando seu tempo limite for atingido ou mais de três problemas abertos forem detectados. Você também pode usar a `terminate` API dentro de outra função ou invocar o webhook **TERMINATEPOSTURI** http post referenciado na resposta 202 acima. Para usar o webhook, substitua `{text}` pelo motivo do encerramento antecipado. A URL HTTP POST terá uma aparência semelhante à seguinte:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou como usar as Funções Duráveis para monitorar o status de uma fonte externa usando [temporizadores duráveis](durable-functions-timers.md) e lógica condicional. O próximo exemplo mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com interação humana.

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)
