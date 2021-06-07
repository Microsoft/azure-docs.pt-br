---
title: Desempenho e escala nas Funções Duráveis – Azure
description: Saiba mais sobre as características exclusivas de dimensionamento da extensão de Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: d41b06bb0c2b26776f9d9c195c3a713e4dae9f82
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626568"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Desempenho e escala nas Funções Duráveis (Azure Functions)

Para otimizar o desempenho e escalabilidade, é importante entender as características únicas da colocação em escala nas [Funções Duráveis](durable-functions-overview.md).

Para entender o comportamento de escala, você precisa entender alguns dos detalhes do provedor subjacente do fornecedor do Armazenamento do Microsoft Azure.

## <a name="history-table"></a>Tabela de histórico

A tabela de **Histórico** é uma tabela do Armazenamento do Microsoft Azure que contém eventos de histórico de todas as instâncias de orquestração com um hub de tarefas. O nome da tabela está no formato *TaskHubName* histórico. Conforme as instâncias são executadas, novas linhas são adicionadas a essa tabela. A chave de partição da tabela é derivada da ID de instância da orquestração. Uma ID de instância é aleatória na maioria dos casos, o que garante a distribuição ideal das partições internas do Armazenamento do Microsoft Azure.

Quando uma instância de orquestração precisa ser executada, as linhas correspondentes da tabela de histórico são carregadas na memória. Esses *histórico de eventos* são, em seguida, copiados para o código de função do orquestrador para recuperá-lo em seu estado de ponto de verificação anteriormente. O uso do histórico de execução para recriar o estado dessa maneira é influenciado pelo [padrão de Fornecimento de Evento](/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela de instâncias

A tabela de **instâncias** é outra tabela de armazenamento do Azure que contém os status de todas as instâncias de orquestração e entidade em um hub de tarefas. Conforme as instâncias são criadas, novas linhas são adicionadas a essa tabela. A chave de partição desta tabela é a ID da instância de orquestração ou a chave de entidade e a chave de linha é uma cadeia de caracteres vazia. Há uma linha por orquestração ou instância de entidade.

Essa tabela é usada para satisfazer solicitações de consulta de instância das `GetStatusAsync` APIs (.net) e `getStatus` (JavaScript), bem como a [API http de consulta de status](durable-functions-http-api.md#get-instance-status). Isso é mantido finalmente consistente com o conteúdo da tabela **Histórico** mencionada anteriormente. O uso de uma tabela separada do Armazenamento do Microsoft Azure para atender com eficiência as operações de consulta de instância dessa maneira é influenciado pelo [padrão de Comando e Segregação de Reponsabilidade (CQRS)](/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Gatilhos de fila interna

As funções de Orchestrator e as funções de atividade são disparadas por filas internas no hub de tarefas do aplicativo. Usar filas dessa maneira fornece garantias de entrega de mensagem “pelo menos uma vez”. Há dois tipos de filas nas Funções Duráveis: a **fila de controle** e a **fila de itens de trabalho**.

### <a name="the-work-item-queue"></a>A fila de item de trabalho

Há uma fila de itens de trabalho por hub de tarefas nas Funções Duráveis. Essa é uma fila básica, que se comporta da mesma forma que qualquer outra fila `queueTrigger` no Azure Functions. Essa fila é usada para disparar *funções de atividade* sem estado para remover da fila uma mensagem única por vez. Cada uma dessas mensagens contém entradas de função de atividades e metadados adicionais, como a função para executar. Quando um aplicativo de Funções Duráveis é expandido para várias VMs, todas essas VMs concorrem para obter trabalho da fila de itens de trabalho.

### <a name="control-queues"></a>Fila(s) de controle

Há várias *filas de controle* por hub de tarefa em Funções Duráveis. Uma *fila de controle* é mais sofisticada do que as filas de itens de trabalho mais simples. Filas de controle são usadas para disparar o orquestrador com estado e as funções de entidade. Como as instâncias de função de orquestrador e de entidade são singletons com estado, é importante que cada orquestração ou entidade seja processada apenas por um trabalhador por vez. Para conseguir isso, cada instância de orquestração ou entidade é atribuída a uma única fila de controle. Essas filas de controle têm balanceamento de carga entre os trabalhadores para garantir que cada fila seja processada apenas por um trabalhador por vez. Para obter mais detalhes sobre esse comportamento, verifique as seções subsequentes.

Filas de controle contêm uma variedade de tipos de mensagem de ciclo de vida de orquestração. Exemplos incluem [mensagens de controle de orquestrador](durable-functions-instance-management.md), mensagens de *resposta* de função de atividade e mensagens de temporizador. Até 32 mensagens serão removidas da fila de uma fila de controle em uma única chamada seletiva. Essas mensagens contêm dados de conteúdo, bem como metadados, incluindo qual instância de orquestração é destinada. Se várias mensagens removidas da fila destinam-se à mesma instância de orquestração, será processado como um lote.

### <a name="queue-polling"></a>Sondagem de fila

A extensão de tarefa durável implementa um algoritmo de retirada exponencial aleatória para reduzir o efeito da sondagem de fila ociosa nos custos de transação de armazenamento. Quando uma mensagem é encontrada, o tempo de execução verifica imediatamente se há outra mensagem; quando nenhuma mensagem é encontrada, ela aguarda um período de tempo antes de tentar novamente. Após as tentativas subsequentes falharem em obter uma mensagem da fila, o tempo de espera continuará aumentando até atingir o tempo de espera máximo, cujo padrão é 30 segundos.

O atraso máximo de sondagem é configurável por meio da `maxQueuePollingInterval` propriedade no [host.jsno arquivo](../functions-host-json.md#durabletask). Definir essa propriedade com um valor mais alto pode resultar em latências de processamento de mensagens mais altas. As latências mais altas seriam esperadas somente após períodos de inatividade. Definir essa propriedade com um valor mais baixo pode resultar em custos de armazenamento mais altos devido a maiores transações de armazenamento.

> [!NOTE]
> Durante a execução nos planos de consumo Azure Functions e Premium, o [controlador de escala de Azure Functions](../event-driven-scaling.md) sondará cada controle e a fila de itens de trabalho uma vez a cada 10 segundos. Essa sondagem adicional é necessária para determinar quando ativar instâncias do aplicativo de funções e tomar decisões de escala. No momento da gravação, esse intervalo de 10 segundos é constante e não pode ser configurado.

### <a name="orchestration-start-delays"></a>Atrasos de início da orquestração
As instâncias de orquestrações são iniciadas colocando uma `ExecutionStarted` mensagem em uma das filas de controle do hub de tarefas. Em determinadas condições, você pode observar atrasos de vários segundos entre quando uma orquestração está agendada para ser executada e quando ela realmente começa a ser executada. Durante esse intervalo de tempo, a instância de orquestração permanece no `Pending` estado. Há duas causas potenciais desse atraso:

1. **Filas de controle de registro** posterior: se a fila de controle dessa instância contiver um grande número de mensagens, pode levar tempo antes que a `ExecutionStarted` mensagem seja recebida e processada pelo tempo de execução. Os registros de pendências de mensagem podem ocorrer quando orquestrações estão processando muitos eventos simultaneamente. Os eventos que entram na fila de controle incluem eventos de início de orquestração, conclusões de atividade, temporizadores duráveis, encerramento e eventos externos. Se esse atraso ocorrer em circunstâncias normais, considere a criação de um novo hub de tarefas com um número maior de partições. A configuração de mais partições fará com que o tempo de execução crie mais filas de controle para a distribuição de carga. Cada partição corresponde a 1:1 com uma fila de controle, com um máximo de 16 partições.

2. **Desfazer atrasos de sondagem**: outra causa comum de atrasos de orquestração é o [comportamento de sondagem de retirada descrito anteriormente para filas de controle](#queue-polling). No entanto, esse atraso só é esperado quando um aplicativo é escalado horizontalmente para duas ou mais instâncias. Se houver apenas uma instância de aplicativo ou se a instância do aplicativo que inicia a orquestração também for a mesma instância que está sondando a fila de controle de destino, não haverá um atraso de sondagem de fila. Os atrasos de sondagem podem ser reduzidos atualizando o **host.jsem** configurações, conforme descrito anteriormente.

## <a name="storage-account-selection"></a>Seleção da conta de armazenamento

As filas, as tabelas e os BLOBs usados pelo Durable Functions são criados em uma conta de armazenamento do Azure configurada. A conta a ser usada pode ser especificada usando a `durableTask/storageProvider/connectionStringName` configuração (ou a `durableTask/azureStorageConnectionStringName` configuração no Durable Functions 1. x) na **host.jsno** arquivo.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

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

Embora as funções de atividade possam ser expandidas infinitamente adicionando mais VMs de forma elástica, as instâncias individuais do orquestrador e as entidades são restritas a estar uma única partição e o número máximo de partições é limitado pela `partitionCount` configuração em seu `host.json` . 

> [!NOTE]
> De modo geral, as funções de orquestrador devem ser leves e não devem precisar de muita capacidade de computação. Portanto, não é necessário criar um grande número de partições de fila de controle para obter uma excelente taxa de transferência para orquestrações. A maior parte do trabalho pesado será feita em funções de atividade sem monitoração de estado, que podem ser expandidas infinitamente.

O número de filas do controle é definido no arquivo **host.json**. O exemplo a seguir host.jsno trecho de código define a `durableTask/storageProvider/partitionCount` Propriedade (ou `durableTask/partitionCount` no Durable Functions 1. x) como `3` . Observe que há tantas filas de controle quanto as partições.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

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

Durante cenários de baixo tráfego, seu aplicativo será dimensionado, de modo que as partições serão gerenciadas por um pequeno número de trabalhadores. Como exemplo, considere o diagrama a seguir.

![Diagrama de orquestrações de escala no](./media/durable-functions-perf-and-scale/scale-progression-1.png)

No diagrama anterior, vemos que os orquestradores de 1 a 6 têm balanceamento de carga entre partições. Da mesma forma, as partições, como atividades, têm balanceamento de carga entre os trabalhadores. As partições têm balanceamento de carga entre os trabalhadores, independentemente do número de orquestradores que começarem.

Se você estiver executando o consumo de Azure Functions ou os planos Premium elásticos ou se tiver configurado o dimensionamento automático baseado em carga, mais trabalhadores serão alocados conforme o aumento de tráfego e as partições eventualmente balancearão a carga entre todos os trabalhadores. Se continuarmos a escalar horizontalmente, cada partição eventualmente será gerenciada por um único trabalho. As atividades, por outro lado, continuarão a ser balanceadas com balanceamento de carga em todos os trabalhos. Isso é mostrado na imagem abaixo.

![Diagrama de orquestrações do primeiro dimensionamento horizontal](./media/durable-functions-perf-and-scale/scale-progression-2.png)

O limite superior do número máximo de orquestrações _ativas_ simultâneas em *um determinado momento* é igual ao número de trabalhadores alocados para o seu aplicativo _vezes_ seu valor para `maxConcurrentOrchestratorFunctions` . Esse limite superior pode se tornar mais preciso quando suas partições são totalmente dimensionadas entre os trabalhadores. Quando totalmente dimensionado, e como cada operador terá apenas uma única instância de host do functions, o número máximo de instâncias simultâneas do orquestrador _ativo_ será igual ao número de partições _vezes_ o valor de `maxConcurrentOrchestratorFunctions` . Nossa imagem abaixo ilustra um cenário totalmente dimensionado em que mais orquestradores são adicionados, mas alguns estão inativos, mostrados em cinza.

![Diagrama de orquestrações de segundo escalado horizontalmente](./media/durable-functions-perf-and-scale/scale-progression-3.png)

Durante a expansão, os bloqueios da fila de controle podem ser redistribuídos entre instâncias de host de funções para garantir que as partições sejam distribuídas uniformemente. Esses bloqueios são implementados internamente como concessões de armazenamento de BLOBs e garantem que qualquer instância de orquestração individual ou entidade seja executada apenas em uma única instância de host de cada vez. Se um hub de tarefas estiver configurado com três partições (e, portanto, três filas de controle), as instâncias de orquestração e as entidades poderão ser balanceadas com balanceamento de carga entre todas as três instâncias de host que mantêm a concessão. VMs adicionais podem ser adicionadas para aumentar a capacidade de execução da função de atividade.

O diagrama a seguir ilustra como o host do Azure Functions interage com as entidades de armazenamento em um ambiente expandido.

![Diagrama de escala](./media/durable-functions-perf-and-scale/scale-interactions-diagram.png)

Conforme mostrado no diagrama anterior, todas as máquinas virtuais competem por mensagens na fila do item de trabalho. No entanto, apenas três VMs podem adquirir mensagens das filas de controle e cada VM bloqueia uma única fila de controle.

As instâncias de orquestração e as entidades são distribuídas entre todas as instâncias de fila de controle. A distribuição é feita pelo hash da ID da instância da orquestração ou do nome da entidade e do par de chaves. As IDs de instância de orquestração por padrão são GUIDs aleatórios, garantindo que as instâncias sejam distribuídas igualmente entre todas as filas de controle.

De modo geral, as funções de orquestrador devem ser leves e não devem precisar de muita capacidade de computação. Portanto, não é necessário criar um grande número de partições de fila de controle para obter uma excelente taxa de transferência para orquestrações. A maior parte do trabalho pesado será feita em funções de atividade sem monitoração de estado, que podem ser expandidas infinitamente.

## <a name="auto-scale"></a>Dimensionamento automático

Assim como todos os Azure Functions em execução nos planos de consumo e Premium elásticos, Durable Functions dá suporte ao dimensionamento automático por meio do [controlador de escala de Azure Functions](../event-driven-scaling.md#runtime-scaling). O Controlador de Escala monitora a latência de todas as filas periodicamente emitindo comandos de _inspeção_. Com base nas latências das mensagens inspecionadas, o controlador de escala irá decidir se deseja adicionar ou remover VMs.

Se o controlador de escala determina que as latências de mensagem de fila de controle são muito altas, ele irá adicionar instâncias de VM até que a latência de mensagem diminua em um nível aceitável ou atinja a contagem de partições de fila do controle. Da mesma forma, o controlador de escala continuamente adiciona instâncias de VM se as latências de fila de item de trabalho são alta, independentemente da contagem de partição.

> [!NOTE]
> A partir do Durable Functions 2,0, os aplicativos de funções podem ser configurados para serem executados em pontos de extremidade de serviço protegidos por VNET no plano Premium elástico. Nessa configuração, os gatilhos de Durable Functions iniciam solicitações de dimensionamento em vez do controlador de escala.

## <a name="thread-usage"></a>Uso de thread

As funções do Orchestrator são executadas em um único thread para assegurar que a execução pode ser determinística entre muitas repetições. Devido a essa execução de thread único, é importante que os threads de função do orquestrador não execute tarefas de uso intensivo de CPU, faça E/S ou bloqueie por algum motivo. Qualquer trabalho que possa exigir E/S, bloqueio ou vários threads deve ser movido para funções de atividade.

As funções de atividade têm os mesmos comportamentos que as funções disparadas por filas regulares. Elas podem, de maneira segura, fazer E/S, executar operações com uso intensivo de CPU e usar vários threads. Como os gatilhos de atividade são sem monitoração de estado, eles podem ser expandidos livremente para um número ilimitado de VMs.

As funções de entidade também são executadas em um único thread e as operações são processadas uma por vez. No entanto, as funções de entidade não têm nenhuma restrição sobre o tipo de código que pode ser executado.

## <a name="concurrency-throttles"></a>Restrições de simultaneidade

O Azure Functions suporta a execução de várias funções simultaneamente em uma instância de aplicativo único. Essa execução simultânea ajuda a aumentar o paralelismo e minimiza o número de "frios" que um aplicativo típico terá ao longo do tempo. No entanto, a alta simultaneidade pode esgotar os recursos do sistema por VM, tais como conexões de rede ou memória disponível. Dependendo das necessidades do aplicativo de função, pode ser necessário aumentar a simultaneidade por instância para evitar a possibilidade de falta de memória em situações de carga alta.

Os limites de simultaneidade da atividade, do Orchestrator e da função de entidade podem ser configurados no **host.jsno** arquivo. As configurações relevantes são `durableTask/maxConcurrentActivityFunctions` para funções de atividade e `durableTask/maxConcurrentOrchestratorFunctions` para o orquestrador e para as funções de entidade.

### <a name="functions-20"></a>Funções 2,0

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

### <a name="functions-1x"></a>Funções 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

No exemplo anterior, um máximo de 10 funções de orquestrador ou entidade e 10 funções de atividade podem ser executados em uma única VM simultaneamente. Se não for especificado, o número de atividades simultâneas e as execuções de função de entidade ou de orquestrador serão limitadas a 10 vezes o número de núcleos na VM.

> [!NOTE]
> Essas configurações são úteis para ajudar a gerenciar o uso da memória e CPU em uma única VM. No entanto, quando dimensionado horizontalmente em várias VMs, cada VM tem seu próprio conjunto de limites. Essas configurações não podem ser usadas para controlar a simultaneidade em um nível global.

## <a name="extended-sessions"></a>Sessões estendidas

As sessões estendidas são uma configuração que mantém orquestrações e entidades na memória mesmo depois de concluir o processamento de mensagens. O efeito típico de habilitar sessões estendidas é E/S reduzidas em relação à conta de Armazenamento do Microsoft Azure e maior taxa de transferência geral.

Você pode habilitar as sessões estendidas definindo `durableTask/extendedSessionsEnabled` como `true` na **host.jsno** arquivo. A `durableTask/extendedSessionIdleTimeoutInSeconds` configuração pode ser usada para controlar quanto tempo uma sessão ociosa será mantida na memória:

**Funções 2,0**
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

**Funções 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Há duas desvantagens potenciais dessa configuração a serem consideradas:

1. Há um aumento geral no uso de memória do aplicativo de funções.
2. Pode haver uma redução geral na taxa de transferência se houver muitas execuções simultâneas de função de entidade ou orquestrador de curta duração.

Por exemplo, se `durableTask/extendedSessionIdleTimeoutInSeconds` é definido como 30 segundos, um orquestrador de curta duração ou um episódio de função de entidade que é executado em menos de 1 segundo ainda ocupa a memória por 30 segundos. Ele também conta com relação à `durableTask/maxConcurrentOrchestratorFunctions` cota mencionada anteriormente, potencialmente impedindo que outras funções de orquestrador ou entidade sejam executadas.

Os efeitos específicos das sessões estendidas no Orchestrator e nas funções de entidade são descritos nas próximas seções.

> [!NOTE]
> Atualmente, há suporte para sessões estendidas apenas em linguagens .NET, como C# ou F #. `extendedSessionsEnabled`A configuração para `true` outras plataformas pode levar a problemas de tempo de execução, como falha silenciosa ao executar as funções disparadas pela orquestração e atividade.


### <a name="orchestrator-function-replay"></a>Reprodução de função do Orchestrator

Conforme mencionado anteriormente, as funções do orquestrador são repetidas usando o conteúdo da tabela **Histórico**. Por padrão, o código de função do orquestrador é repetido sempre que um lote de mensagens for removido da fila de uma fila de controle. Mesmo se você estiver usando o padrão Fan-out e Fan-in e estiver aguardando que todas as tarefas sejam concluídas (por exemplo, usando o `Task.WhenAll` no .net ou `context.df.Task.all` em JavaScript), haverá repetições que ocorrem à medida que os lotes de respostas de tarefa são processados ao longo do tempo. Quando as sessões estendidas são habilitadas, as instâncias de função de orquestrador são mantidas em memória por mais tempo e novas mensagens podem ser processadas sem uma reprodução de histórico completo.

A melhoria de desempenho de sessões estendidas é geralmente observada nas seguintes situações:

* Quando há um número limitado de instâncias de orquestração em execução simultânea.
* Quando as orquestrações têm um grande número de ações sequenciais (por exemplo, centenas de chamadas de função de atividade) que são concluídas rapidamente.
* Quando orquestrações Fan-out e Fan-in um grande número de ações que são concluídas ao mesmo tempo.
* Quando as funções de orquestrador precisam processar mensagens grandes ou qualquer processamento de dados intensivo de CPU.

Em todas as outras situações, normalmente não há melhoria de desempenho observável para funções de orquestrador.

> [!NOTE]
> Essas configurações só devem ser usadas depois que uma função do orquestrador for totalmente desenvolvida e testada. O comportamento de reprodução agressiva padrão pode ser útil para detectar violações de [restrições de código de função de orquestrador](durable-functions-code-constraints.md) no tempo de desenvolvimento e, portanto, é desabilitado por padrão.

### <a name="entity-function-unloading"></a>Descarregamento de função de entidade

As funções de entidade processam até 20 operações em um único lote. Assim que uma entidade termina de processar um lote de operações, ela persiste seu estado e descarrega a partir da memória. Você pode atrasar o descarregamento de entidades da memória usando a configuração de sessões estendidas. As entidades continuam a persistir suas alterações de estado como antes, mas permanecem na memória pelo período de tempo configurado para reduzir o número de cargas do armazenamento do Azure. Essa redução de cargas do armazenamento do Azure pode melhorar a taxa de transferência geral de entidades acessadas com frequência.

## <a name="performance-targets"></a>Destinos de desempenho

Ao planejar usar funções duráveis para um aplicativo de produção, é importante considerar os requisitos de desempenho no início do processo de planejamento. Esta seção aborda alguns cenários de uso básico e os números de taxa de transferência máxima esperada.

* **Execução de atividades sequenciais**: este cenário descreve uma função do orquestrador que executa uma série de funções de atividade um após o outro. Ele é bastante semelhante ao exemplo de [Encadeamento de função](durable-functions-sequence.md).
* **Execução de atividade paralela**: este cenário descreve uma função do orquestrador que executa muitas funções de atividades em paralelo usando o padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md).
* **Resposta de processamento paralelo**: esse cenário é a segunda metade do padrão [Fan-out, Fan-in](durable-functions-cloud-backup.md). Se concentra no desempenho de fan-in. É importante observar que ao contrário do tipo fan-out, fan-in é feito por uma instância de função do orchestrator único e, portanto, só pode executar em uma única VM.
* **Processamento de eventos externos**: esse cenário representa uma instância de função do orquestrador único que espera [eventos externos](durable-functions-external-events.md), um de cada vez.
* **Processamento de operação de entidade**: esse cenário testa a rapidez com que uma _única_ [entidade de contador](durable-functions-entities.md) pode processar um fluxo constante de operações.

> [!TIP]
> Ao contrário do tipo fan-out, as operações de consolidação são limitadas a uma única VM. Se seu aplicativo usa o padrão fan-out, fan-in e você estiver preocupado sobre o desempenho de fan-in, divida abaixo a divisão da função de atividade em várias [sub-orquestrações](durable-functions-sub-orchestrations.md).

A tabela a seguir mostra os números *máximos* de taxa de transferência para os cenários descritos anteriormente. "Instância" refere-se a uma única instância de uma função do orquestrador em execução em uma única pequena VM ([A1](../../virtual-machines/sizes-previous-gen.md)) no Serviço do Aplicativo do Azure. Todos os casos, presume-se que [sessões estendidas](#orchestrator-function-replay) estão habilitadas. Os resultados reais podem variar dependendo do trabalho de CPU ou E/S executado pelo código da função.

| Cenário | Taxa de transferência máxima |
|-|-|
| Execução da atividade sequencial | 5 atividades por segundo, por instância |
| Execução de atividade paralela (fan-out) | 100 atividades por segundo, por instância |
| Processamento paralelo de resposta (fan-in) | 150 respostas por segunda, por instância |
| Processamento de evento externo | 50 eventos por segunda, por instância |
| Processamento de operação de entidade | 64 operações por segundo |

> [!NOTE]
> Esses números são atuais a partir da versão v1.4.0 (GA) da extensão de Funções Duráveis. Esses números podem alterar ao longo do tempo conforme o recurso amadurece e otimizações que são feitas.

Se você não estiver vendo os números de taxa de transferência esperada e a CPU e uso de memória aparece íntegra, verifique se a causa está relacionada à integridade [ de sua conta de armazenamento](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). A extensão de Funções Duráveis pode colocar carga significativa em uma conta de Armazenamento do Microsoft Azure e carga suficientemente altas podem resultar na limitação da conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre recuperação de desastres e distribuição geográfica](durable-functions-disaster-recovery-geo-distribution.md)
