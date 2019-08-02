---
title: Iniciar eventos de exclusão de pool lote do Azure | Documentos do Microsoft
description: Referência para excluir um pool de lote evento inicial.
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
ms.openlocfilehash: 65d20f2194b2bf83ecf32e19c1ab5b0f7bc7a004
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323211"
---
# <a name="pool-delete-start-event"></a>Evento inicial de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento completo de conclusão de exclusão de pool seja emitido quando a operação de exclusão é concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
    "id": "myPool1"
}
```

|Elemento|Tipo|Observações|
|-------------|----------|-----------|
|id|Cadeia de caracteres|A ID do pool.|