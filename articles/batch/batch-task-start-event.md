---
title: Evento de início de tarefa no Lote do Azure
description: Informações de referência para evento de início de tarefa no Lote. Esse evento é emitido quando uma tarefa é agendada para iniciar em um nó de computação pelo agendador.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 3a57ffbb1e1659cff54d101aa4b90ca1bd5d3a57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851009"
---
# <a name="task-start-event"></a>Evento de início da tarefa

 Esse evento é emitido quando uma tarefa é agendada para iniciar em um nó de computação pelo agendador. Observe que, se a tarefa for repetida ou colocada novamente na fila, esse evento será emitido novamente para a mesma tarefa, mas a contagem de repetição e versão de tarefa do sistema serão atualizadas adequadamente.


 O exemplo a seguir mostra o corpo de um evento de início da tarefa.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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
        "retryCount": 0
    }
}
```

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`jobId`|String|A ID do trabalho que contém a tarefa.|
|`id`|String|A ID da tarefa.|
|`taskType`|String|O tipo de tarefa. Pode ser “JobManager” indicando que é uma tarefa do gerenciador de trabalhos ou “Usuário”, indicando que não é uma tarefa do gerenciador de trabalhos.|
|`systemTaskVersion`|Int32|Esse é o contador interno de repetição de uma tarefa. Internamente, o serviço em lotes pode repetir uma tarefa para contabilizar problemas transitórios. Esses problemas podem incluir erros internos de agendamento ou tentativa de recuperar nós de computação em estado inválido.|
|`requiredSlots`|Int32|Os slots necessários para executar a tarefa.|
|[`nodeInfo`](#nodeInfo)|Tipo complexo|Contém informações sobre o nó de computação em que a tarefa é executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo complexo|Especifica que a tarefa é uma tarefa com várias instâncias que precisa de vários nós de computação.  Consulte [multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
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
|`numberOfInstances`|Int|O número de nós de computação que a tarefa precisa.|

###  <a name="constraints"></a><a name="constraints"></a> restrições

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço em lotes repetirá uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de tentativas. O serviço em lotes tentará a tarefa uma vez e, em seguida, pode tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetição for 3, o lote tentará uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetição for 0, o serviço em lote não tentará repetir a tarefas.<br /><br /> Se a contagem máxima de repetição for -1, o serviço em lotes repetirá as tarefas ilimitadamente.<br /><br /> O valor padrão é 0 (sem novas tentativas).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço em lotes. A tarefa será repetida se a saída tiver um código de saída diferente de zero, até a MaxTaskRetryCount especificada|
