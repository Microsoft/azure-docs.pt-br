---
title: Evento inicial de redimensionamento de pool de lote do Azure | Documentos do Microsoft
description: Referência de redimensionamento do pool de lote evento inicial.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776415"
---
# <a name="pool-resize-complete-event"></a>Evento de conclusão de redimensionamento de pool

 Esse evento é emitido na conclusão ou falha de um redimensionamento de pool.

 O exemplo a seguir mostra o corpo de um evento de conclusão de redimensionamento de pool para um pool que aumentou de tamanho e foi concluído com êxito.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|ID|Cadeia de caracteres|A ID do pool.|
|nodeDeallocationOption|Cadeia de caracteres|Especifica quando os nós poderão ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> **colocar novamente na fila** – Finalize as tarefas em execução e coloque-as novamente na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova nós assim que tarefas forem terminadas.<br /><br /> **terminar** – Termine as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que tarefas forem terminadas.<br /><br /> **taskcompletion** – Permita a conclusão das tarefas atualmente em execução. Não agende novas tarefas enquanto aguarda. Remova nós quando todas as tarefas forem concluídas.<br /><br /> **Retaineddata** – Permita que as tarefas atualmente em execução sejam concluídas e aguarde que os todos os períodos de retenção de dados das tarefas expirem. Não agende novas tarefas enquanto aguarda. Remova nós quando todos os períodos de retenção de tarefa expirem.<br /><br /> O valor padrão é colocar novamente na fila.<br /><br /> Se o tamanho do pool aumentar, o valor será definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao pool.|
|targetDedicated|Int32|O número de nós de computação solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
|startTime|DateTime|O tempo de redimensionamento do pool foi iniciado.|
|endTime|DateTime|O tempo de redimensionamento do pool foi concluído.|
|resultCode|Cadeia de caracteres|O resultado do redimensionamento.|
|resultMessage|Cadeia de caracteres|O erro de redimensionamento inclui os detalhes do resultado.<br /><br /> Se o redimensionamento foi concluído com êxito, informará que a operação foi bem-sucedida.|