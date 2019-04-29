---
title: Desempenho e escala nas Funções Duráveis – Azure
description: Introdução à extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: e6ae4cc527ae0828f530ab7f3904d2b3c64c910b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733226"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e escala nas Funções Duráveis (Azure Functions)

Para otimizar o desempenho e escalabilidade, é importante entender as características únicas da colocação em escala nas [Funções Duráveis](durable-functions-overview.md).

Para entender o comportamento de escala, você precisa entender alguns dos detalhes do provedor subjacente do fornecedor do Armazenamento do Microsoft Azure.

## <a name="history-table"></a>Tabela de histórico

A tabela de **Histórico** é uma tabela do Armazenamento do Microsoft Azure que contém eventos de histórico de todas as instâncias de orquestração com um hub de tarefas. O nome da tabela está no formato *TaskHubName*histórico. Conforme as instâncias são executadas, novas linhas são adicionadas a essa tabela. A chave de partição da tabela é derivada da ID de instância da orquestração. Uma ID de instância é aleatória na maioria dos casos, o que garante a distribuição ideal das partições internas do Armazenamento do Microsoft Azure.

Quando uma instância de orquestração precisa ser executada, as linhas correspondentes da tabela de histórico são carregadas na memória. Esses *histórico de eventos* são, em seguida, copiados para o código de função do orquestrador para recuperá-lo em seu estado de ponto de verificação anteriormente. O uso do histórico de execução para recriar o estado dessa maneira é influenciado pelo [padrão de Fornecimento de Evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela de instâncias

A tabela de **Instâncias** é uma tabela do Armazenamento do Microsoft Azure que contém status de todas as instâncias de orquestração dentro de um hub de tarefas. Conforme as instâncias são criadas, novas linhas são adicionadas a essa tabela. A chave de partição da tabela é a ID de instância de orquestração e a chave de linha é uma constante fixa. Há uma linha por instância de orquestração.

Esta tabela é usada para atender a solicitações de consulta de instância da API [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) e `getStatus` (JavaScript), bem como a [API de HTTP de consulta de status](durable-functions-http-api.md#get-instance-status). Isso é mantido finalmente consistente com o conteúdo da tabela **Histórico** mencionada anteriormente. O uso de uma tabela separada do Armazenamento do Microsoft Azure para atender com eficiência as operações de consulta de instância dessa maneira é influenciado pelo [padrão de Comando e Segregação de Reponsabilidade (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

As funções de Orchestrator e as funções de atividade são disparadas por filas internas no hub de tarefas do aplicativo. Usar filas dessa maneira fornece garantias de entrega de mensagem “pelo menos uma vez”. Há dois tipos de filas nas Funções Duráveis: a **fila de controle** e a **fila de itens de trabalho**.

### <a name="the-work-item-queue"></a>A fila de item de trabalho

Há uma fila de itens de trabalho por hub de tarefas nas Funções Duráveis. Essa é uma fila básica, que se comporta da mesma forma que qualquer outra fila `queueTrigger` no Azure Functions. Essa fila é usada para disparar *funções de atividade* sem estado para remover da fila uma mensagem única por vez. Cada uma dessas mensagens contém entradas de função de atividades e metadados adicionais, como a função para executar. Quando um aplicativo de Funções Duráveis é expandido para várias VMs, todas essas VMs concorrem para obter trabalho da fila de itens de trabalho.

### <a name="control-queues"></a>Fila(s) de controle

Há várias *filas de controle* por hub de tarefa em Funções Duráveis. Uma *fila de controle* é mais sofisticada do que as filas de itens de trabalho mais simples. As filas de controle são usadas para disparar as funções de orquestrador com estado. Como as instâncias de função de orquestrador são singletons com estado, não é possível usar um modelo de consumidor concorrente para distribuir a carga entre VMs. Em vez disso, as mensagens do orquestrador sofrem balanceamento de carga em filas de controle. Para obter mais detalhes sobre esse comportamento, verifique as seções subsequentes.

Filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Exemplos incluem [mensagens de controle de orquestrador](durable-functions-instance-management.md), mensagens de *resposta* de função de atividade e mensagens de temporizador. Até 32 mensagens serão removidas da fila de uma fila de controle em uma única chamada seletiva. Essas mensagens contêm dados de conteúdo, bem como metadados, incluindo qual instância de orquestração é destinada. Se várias mensagens removidas da fila destinam-se à mesma instância de orquestração, será processado como um lote.

### <a name="queue-polling"></a>Sondagem de fila

A extensão de tarefa durável implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem nos custos das transações de armazenamento de fila ociosa. Quando uma mensagem for encontrada, o tempo de execução imediatamente verifica outra mensagem; Quando nenhuma mensagem for encontrada, ele aguarda um período de tempo antes de tentar novamente. Após subsequentes tentativas com falha para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é 30 segundos.

O atraso máximo de sondagem é configurável por meio de `maxQueuePollingInterval` propriedade no [arquivo host. JSON](../functions-host-json.md#durabletask). Definir isso como um valor mais alto pode resultar em maior latências de processamento de mensagens. Latências mais altas deve ser esperadas somente após períodos de inatividade. Definir isso como um valor mais baixo pode resultar em custos de armazenamento maiores devido às transações de armazenamento maior.

> [!NOTE]
> Quando em execução nos planos de consumo do Azure Functions e Premium, o [controlador de escala do Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) irá sondar cada fila de controle e de item de trabalho uma vez a cada 10 segundos. Essa pesquisa adicional é necessária para determinar quando ativar instâncias do aplicativo de função e para tomar decisões de dimensionamento. No momento da escrita, esse intervalo de 10 segundos é constante e não pode ser configurado.

## <a name="storage-account-selection"></a>Seleção da conta de armazenamento

As filas, tabelas e blobs usados pelas funções duráveis são criados em uma conta de armazenamento do Azure configurada. A conta a ser usada pode ser especificada usando a configuração do arquivo `durableTask/azureStorageConnectionStringName` em **host.json** arquivo.

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se não estiver especificado, a conta de armazenamento `AzureWebJobsStorage` padrão é usada. Para cargas de trabalho afetadas pelo desempenho, no entanto, configurar uma conta de armazenamento padrão não é recomendável. Funções duráveis usam o Armazenamento do Microsoft Azure intensamente e usam uma conta de armazenamento dedicado que isola o uso do armazenamento de funções duráveis com o uso interno pelo host de Funções do Microsoft Azure.

## <a name="orchestrator-scale-out"></a>Expansão do orquestrador

As funções de atividade são sem estado e são expandidas automaticamente adicionando VMs. Funções de orquestrador, por outro lado, são *particionadas* entre uma ou mais filas de controle. O número de filas do controle é definido no arquivo **host.json**. O snippet do exemplo a seguir host.json define a `durableTask/partitionCount` propriedade para `3`.

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Um hub de tarefa pode ser configurado com entre 1 e 16 partições. Se não for especificado, a contagem de participação padrão 4 **será usada**.

Ao expandir para várias instâncias de host de função (normalmente, em VMs diferentes), cada instância adquire um bloqueio de uma das filas de controle. Esses bloqueios são implementados internamente como concessões de armazenamento de blob e garantem que uma instância de orquestração só é executada em uma instância de host único por vez. Se um hub de tarefas for configurado com três filas de controle, instâncias de orquestração poderão sofrer balanceamento de carga entre até três VMs. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.

O diagrama a seguir ilustra como o host do Azure Functions interage com as entidades de armazenamento em um ambiente expandido.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-diagram.png)

Conforme mostrado no diagrama anterior, todas as máquinas virtuais competem por mensagens na fila do item de trabalho. No entanto, apenas três VMs podem adquirir mensagens das filas de controle e cada VM bloqueia uma única fila de controle.

As instâncias de orquestração são distribuídas em todas as instâncias de fila do controle. A distribuição é feita com a ID da instância de orquestração de hash. IDs de instância por padrão são GUIDs aleatórios, garantindo que as instâncias sejam distribuídas igualmente em todas as filas de controle.

De modo geral, as funções de orquestrador devem ser leves e não devem precisar de muita capacidade de computação. Por esse motivo, não é necessário criar um grande número de partições de fila de controle para obter uma boa taxa de transferência. A maior parte do trabalho pesado será feita em funções de atividade sem monitoração de estado, que podem ser expandidas infinitamente.

## <a name="auto-scale"></a>Dimensionamento automático

Assim como ocorre com todas as funções do Azure Functions em execução no Plano de Consumo, as Funções Duráveis dão suporte ao dimensionamento automático por meio do [Controlador de escala do Azure Functions](../functions-scale.md#runtime-scaling). O Controlador de Escala monitora a latência de todas as filas periodicamente emitindo comandos de _inspeção_. Com base nas latências das mensagens inspecionadas, o controlador de escala irá decidir se deseja adicionar ou remover VMs.

Se o controlador de escala determina que as latências de mensagem de fila de controle são muito altas, ele irá adicionar instâncias de VM até que a latência de mensagem diminua em um nível aceitável ou atinja a contagem de partições de fila do controle. Da mesma forma, o controlador de escala continuamente adiciona instâncias de VM se as latências de fila de item de trabalho são alta, independentemente da contagem de partição.

## <a name="thread-usage"></a>Uso de thread

As funções do Orchestrator são executadas em um único thread para assegurar que a execução pode ser determinística entre muitas repetições. Devido a essa execução de thread único, é importante que os threads de função do orquestrador não execute tarefas de uso intensivo de CPU, faça E/S ou bloqueie por algum motivo. Qualquer trabalho que possa exigir E/S, bloqueio ou vários threads deve ser movido para funções de atividade.

As funções de atividade têm os mesmos comportamentos que as funções disparadas por filas regulares. Elas podem, de maneira segura, fazer E/S, executar operações com uso intensivo de CPU e usar vários threads. Como os gatilhos de atividade são sem monitoração de estado, eles podem ser expandidos livremente para um número ilimitado de VMs.

## <a name="concurrency-throttles"></a>Restrições de simultaneidade

O Azure Functions suporta a execução de várias funções simultaneamente em uma instância de aplicativo único. Essa execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "frios" que um aplicativo típico terá ao longo do tempo. No entanto, a simultaneidade alta pode resultar no uso alto de memória por VM. Dependendo das necessidades do aplicativo de função, pode ser necessário aumentar a simultaneidade por instância para evitar a possibilidade de falta de memória em situações de carga alta.

A função de atividade e os limites de simultaneidade do orquestrador podem ser configurados no arquivo **host.json**. As configurações relevantes são `durableTask/maxConcurrentActivityFunctions` e `durableTask/maxConcurrentOrchestratorFunctions` respectivamente.

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

No exemplo anterior, um máximo de 10 funções do orquestrador e 10 atividades podem ser executados em uma única VM simultaneamente. Se não for especificado, o número de execuções simultâneas de função de atividade e o orchestrator está limitado a 10 vezes o número de núcleos na VM.

> [!NOTE]
> Essas configurações são úteis para ajudar a gerenciar o uso da memória e CPU em uma única VM. No entanto, quando expandida em várias VMs, cada VM terá seu próprio conjunto de limites. Essas configurações não podem ser usadas para controlar a simultaneidade em um nível global.

## <a name="orchestrator-function-replay"></a>Reprodução de função do Orchestrator

Conforme mencionado anteriormente, as funções do orquestrador são repetidas usando o conteúdo da tabela **Histórico**. Por padrão, o código de função do orquestrador é repetido sempre que um lote de mensagens for removido da fila de uma fila de controle.

Esse comportamento de repetição agressiva pode ser desabilitado, permitindo **sessões estendidas**. Quando sessões estendidos são habilitadas, instâncias de função do orquestrador são mantidas na memória mensagens novas e mais podem ser processadas sem uma reprodução completa. Sessões estendidas são ativadas por configuração `durableTask/extendedSessionsEnabled` para arquivo `true` no **host.json**. A `durableTask/extendedSessionIdleTimeoutInSeconds` configuração é usada para controlar quanto tempo uma sessão ociosa será mantida na memória:

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Funções 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

O efeito típico de habilitar sessões estendidas é E/S reduzidas em relação à conta de Armazenamento do Microsoft Azure e maior taxa de transferência geral.

No entanto, uma desvantagem potencial desse recurso é que as instâncias da função do orquestrador permanecerão na memória por mais tempo. Há dois efeitos a serem considerados:

1. Aumento da visão geral no uso de memória do aplicativo de função.
2. Diminua geral na taxa de transferência se houver várias execuções de função do orchestrator simultâneas e de curta duração.

Por exemplo, se `durableTask/extendedSessionIdleTimeoutInSeconds` é definido como 30 segundos, em seguida, um episódio de função do orquestrador de curta duração que é executado em menos de 1 segundo ainda ocupa a memória por 30 segundos. Ele também afetará a `durableTask/maxConcurrentOrchestratorFunctions` cota mencionada anteriormente, impedindo que outras funções do orquestrador sejam executadas.

> [!NOTE]
> Essas configurações só devem ser usadas depois que uma função do orquestrador for totalmente desenvolvida e testada. O comportamento de repetição agressiva padrão é útil para detectar erros de idempotência nas funções do orquestrador no tempo de desenvolvimento.

## <a name="performance-targets"></a>Destinos de desempenho

Ao planejar usar funções duráveis para um aplicativo de produção, é importante considerar os requisitos de desempenho no início do processo de planejamento. Esta seção aborda alguns cenários de uso básico e os números de taxa de transferência máxima esperada.

* **Execução da atividade sequencial**: Este cenário descreve uma função do orquestrador que executa uma série de funções de atividade um após o outro. Ele é bastante semelhante ao exemplo de [Encadeamento de função](durable-functions-sequence.md).
* **Execução de atividade paralela**: Este cenário descreve uma função do orquestrador que executa muitas funções de atividades em paralelo usando o padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md).
* **Resposta de processamento paralelo**: Esse cenário é a segunda metade do padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md). Se concentra no desempenho de fan-in. É importante observar que ao contrário do tipo fan-out, fan-in é feito por uma instância de função do orchestrator único e, portanto, só pode executar em uma única VM.
* **Processamento de eventos externos**: Esse cenário representa uma instância de função do orquestrador único que espera [eventos externos](durable-functions-external-events.md), um de cada vez.

> [!TIP]
> Ao contrário do tipo fan-out, as operações de consolidação são limitadas a uma única VM. Se seu aplicativo usa o padrão fan-out, fan-in e você estiver preocupado sobre o desempenho de fan-in, divida abaixo a divisão da função de atividade em várias [sub-orquestrações](durable-functions-sub-orchestrations.md).

A tabela a seguir mostra os números *máximos* de taxa de transferência para os cenários descritos anteriormente. "Instância" refere-se a uma única instância de uma função do orquestrador em execução em uma única pequena VM ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) no Serviço do Aplicativo do Azure. Todos os casos, presume-se que [sessões estendidas](#orchestrator-function-replay) estão habilitadas. Os resultados reais podem variar dependendo do trabalho de CPU ou E/S executado pelo código da função.

| Cenário | Taxa de transferência máxima |
|-|-|
| Execução da atividade sequencial | 5 atividades por segundo, por instância |
| Execução de atividade paralela (fan-out) | 100 atividades por segundo, por instância |
| Processamento paralelo de resposta (fan-in) | 150 respostas por segunda, por instância |
| Processamento de evento externo | 50 eventos por segunda, por instância |

> [!NOTE]
> Esses números são atuais a partir da versão v1.4.0 (GA) da extensão de Funções Duráveis. Esses números podem alterar ao longo do tempo conforme o recurso amadurece e otimizações que são feitas.

Se você não estiver vendo os números de taxa de transferência esperada e a CPU e uso de memória aparece íntegra, verifique se a causa está relacionada à integridade [ de sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A extensão de Funções Duráveis pode colocar carga significativa em uma conta de Armazenamento do Microsoft Azure e carga suficientemente altas podem resultar na limitação da conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar sua primeira função durável em C#](durable-functions-create-first-csharp.md)
