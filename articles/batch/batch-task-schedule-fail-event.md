---
title: Evento de falha na agenda de tarefas do lote do Azure
description: Referência do evento de falha na agenda de tarefas do lote. Esse evento é emitido quando uma tarefa não é agendada e tentará novamente mais tarde.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91852096"
---
# <a name="task-schedule-fail-event"></a>Evento de falha no agendamento da tarefa

 Esse evento é emitido quando uma tarefa não é agendada e será repetida mais tarde. Essa é uma falha temporária no tempo de agendamento de tarefa devido à limitação de recursos, por exemplo, não há slots suficientes disponíveis em nós para executar uma tarefa com o `requiredSlots` especificado.

 O exemplo a seguir mostra o corpo de um evento de falha de agendamento de tarefa.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
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
|[`schedulingError`](#schedulingError)|Tipo complexo|Contém informações sobre o erro de agendamento da tarefa.|

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


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`category`|String|A categoria do erro.|
|`code`|String|Um identificador para o erro de agendamento da tarefa. Os códigos são invariáveis e devem ser consumidos programaticamente.|
|`message`|String|Uma mensagem que descreve o erro de agendamento da tarefa, destinada a ser adequada para exibição em uma interface do usuário.|
