---
title: Evento de conclusão de tarefa do Lote do Azure
description: Referência de evento de tarefa de lote concluída. Esse evento é emitido quando uma tarefa é concluída, independentemente do código de saída.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 11f727b07723f32cd08130b4af17e57ede3d264f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850884"
---
# <a name="task-complete-event"></a>Evento de conclusão de tarefa

 Esse evento é emitido quando uma tarefa é concluída, independentemente do código de saída. Esse evento pode ser usado para determinar a duração de uma tarefa, em que ela foi executada e se foi repetida.


 O exemplo a seguir mostra o corpo de um evento de conclusão de tarefa.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`jobId`|String|A ID do trabalho que contém a tarefa.|
|`id`|String|A ID da tarefa.|
|`taskType`|String|O tipo de tarefa. Pode ser “JobManager” indicando que é uma tarefa do gerenciador de trabalhos ou “Usuário”, indicando que não é uma tarefa do gerenciador de trabalhos. Esse evento não é emitido para tarefas de preparação, lançamento ou inicialização de trabalho.|
|`systemTaskVersion`|Int32|Esse é o contador interno de repetição de uma tarefa. Internamente, o serviço em lotes pode repetir uma tarefa para contabilizar problemas transitórios. Esses problemas podem incluir erros internos de agendamento ou tentativa de recuperar nós de computação em estado inválido.|
|`requiredSlots`|Int32|Os slots necessários para executar a tarefa.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa é executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa com várias instâncias que precisa de vários nós de computação.  Confira [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
|[`constraints`](#constraints)|Tipo complexo|As restrições de execução aplicáveis a essa tarefa.|
|[`executionInfo`](#executionInfo)|Tipo complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`poolId`|String|A ID do pool em que a tarefa foi executada.|
|`nodeId`|String|A ID do nó em que a tarefa foi executada.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação que a tarefa precisa.|

###  <a name="constraints"></a><a name="constraints"></a> restrições

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço em lotes repetirá uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de tentativas. O serviço em lotes tentará a tarefa uma vez e, em seguida, pode tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetição for 3, o lote tentará uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetição for 0, o serviço em lote não tentará repetir a tarefas.<br /><br /> Se a contagem máxima de repetição for -1, o serviço em lotes repetirá as tarefas ilimitadamente.<br /><br /> O valor padrão é 0 (sem novas tentativas).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`startTime`|Datetime|A hora em que a tarefa começou a ser executada. “Em execução” corresponde ao estado de **execução**, portanto, se a tarefa especificar arquivos de recursos ou pacotes de aplicativos, a hora de início refletirá a hora na qual a tarefa começou a baixar ou implantar esses arquivos ou pacotes.  Se a tarefa foi reiniciada ou repetida, esta é a última vez em que a tarefa começou a ser executada.|
|`endTime`|Datetime|A hora e conclusão da tarefa.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço em lotes. A tarefa será repetida se a saída tiver um código de saída diferente de zero, até a MaxTaskRetryCount especificada.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi colocada em fila novamente pelo serviço em lotes, como resultado de uma solicitação de usuário.<br /><br /> Quando o usuário remove nós de um pool (redimensionando ou reduzindo o pool) ou quando o trabalho é desabilitado, o usuário pode especificar que as tarefas em execução nos nós sejam colocadas novamente na fila de execução. Essa contagem controla quantas vezes a tarefa foi colocada novamente em fila por esses motivos.|
