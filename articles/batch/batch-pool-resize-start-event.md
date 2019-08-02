---
title: Evento inicial de redimensionamento de pool de lote do Azure | Documentos do Microsoft
description: Referência de redimensionamento do pool de lote evento inicial.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: c7ef6bb9550b7398a10f5b57e6009d896256666b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323188"
---
# <a name="pool-resize-start-event"></a>Evento inicial de redimensionamento de pool

 Esse evento é emitido quando um redimensionamento de pool é iniciado. Como o redimensionamento do pool é um evento assíncrono, você pode esperar que um evento completo de redimensionamento de pool seja emitido quando a operação de redimensionamento é concluída.

 O exemplo a seguir mostra o corpo de um evento inicial de redimensionamento de pool para um redimensionamento de 0 a 2 nós com redimensionamento manual.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|poolId|Cadeia de caracteres|A ID do pool.|
|nodeDeallocationOption|Cadeia de caracteres|Especifica quando os nós poderão ser removidos do pool, se o tamanho do pool estiver diminuindo.<br /><br /> Os valores possíveis são:<br /><br /> **colocar novamente na fila** – Finalize as tarefas em execução e coloque-as novamente na fila. As tarefas serão executadas novamente quando o trabalho for habilitado. Remova nós assim que tarefas forem terminadas.<br /><br /> **terminar** – Termine as tarefas em execução. As tarefas não serão executadas novamente. Remova nós assim que tarefas forem terminadas.<br /><br /> **taskcompletion** – Permita a conclusão das tarefas atualmente em execução. Não agende novas tarefas enquanto aguarda. Remova nós quando todas as tarefas forem concluídas.<br /><br /> **Retaineddata** – Permita que as tarefas atualmente em execução sejam concluídas e aguarde que os todos os períodos de retenção de dados das tarefas expirem. Não agende novas tarefas enquanto aguarda. Remova nós quando todos os períodos de retenção de tarefa expirem.<br /><br /> O valor padrão é colocar novamente na fila.<br /><br /> Se o tamanho do pool aumentar, o valor será definido como **inválido**.|
|currentDedicated|Int32|O número de nós de computação atualmente atribuídos ao pool.|
|targetDedicated|Int32|O número de nós de computação solicitados para o pool.|
|enableAutoScale|Bool|Especifica se o tamanho do pool é ajustado automaticamente com o tempo.|
|isAutoPool|Bool|Especifica se o pool foi criado por meio de um mecanismo de AutoPool do trabalho.|
