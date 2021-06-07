---
title: Evento de redimensionamento do conjunto de Lote do Azure concluído
description: Referência de redimensionamento do pool de lote evento inicial. Confira um exemplo de pool que teve seu tamanho aumentado e foi concluído com êxito.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 9d3342587b5f6e0e134f4295a8c79deeb23df94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803639"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de pool

 Esse evento é emitido na conclusão ou falha de um redimensionamento de pool.

 O exemplo a seguir mostra o corpo de um evento de conclusão de redimensionamento de pool para um pool que aumentou de tamanho e foi concluído com êxito.

```
{
   "id": "myPool",
   "nodeDeallocationOption": "invalid",
      "currentDedicatedNodes": 10,
      "targetDedicatedNodes": 10,
   "currentLowPriorityNodes": 5,
     "targetLowPriorityNodes": 5,
   "enableAutoScale": false,
   "isAutoPool": false,
   "startTime": "2016-09-09T22:13:06.573Z",
   "endTime": "2016-09-09T22:14:01.727Z",
   "resultCode": "Success",
   "resultMessage": "The operation succeeded"
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
|`nodeDeallocationOption`|String|Especifica quando os nós poderão ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> **colocar novamente na fila** – Finalize as tarefas em execução e coloque-as novamente na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova nós assim que tarefas forem terminadas.<br /><br /> **terminar** – Termine as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que tarefas forem terminadas.<br /><br /> **taskcompletion** – Permita a conclusão das tarefas atualmente em execução. Não agende novas tarefas enquanto aguarda. Remova nós quando todas as tarefas forem concluídas.<br /><br /> **Retaineddata** – Permita que as tarefas atualmente em execução sejam concluídas e aguarde que os todos os períodos de retenção de dados das tarefas expirem. Não agende novas tarefas enquanto aguarda. Remova nós quando todos os períodos de retenção de tarefa expirem.<br /><br /> O valor padrão é colocar novamente na fila.<br /><br /> Se o tamanho do pool aumentar, o valor será definido como **inválido**.|
|`currentDedicatedNodes`|Int32|O número de nós de computação dedicados atualmente atribuídos ao pool.|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados solicitados para o pool.|
|`currentLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade atualmente atribuídos ao pool.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade solicitados para o pool.|
|`enableAutoScale`|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|`isAutoPool`|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
|`startTime`|Datetime|O tempo de redimensionamento do pool foi iniciado.|
|`endTime`|Datetime|O tempo de redimensionamento do pool foi concluído.|
|`resultCode`|String|O resultado do redimensionamento.|
|`resultMessage`|String| Uma mensagem detalhada sobre o resultado.<br /><br /> Se o redimensionamento foi concluído com êxito, informará que a operação foi bem-sucedida.|
