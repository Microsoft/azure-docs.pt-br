---
title: Evento de exclusão do pool do Lote do Azure concluído
description: Referência de exclusão do pool de lote evento inicial. Esse evento é emitido quando uma operação de exclusão de pool é concluída.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803724"
---
# <a name="pool-delete-complete-event"></a>Evento de conclusão de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é concluída.

 O exemplo a seguir mostra o corpo de um evento de conclusão de exclusão de pool.

```
{
   "id": "myPool1",
   "startTime": "2016-09-09T22:13:48.579Z",
   "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
|`startTime`|Datetime|A hora de início da exclusão do pool.|
|`endTime`|Datetime|A hora de conclusão da exclusão do pool.|

## <a name="remarks"></a>Comentários

Para obter mais informações sobre estados e códigos de erro para a operação de redimensionamento do pool, consulte [Excluir um pool de uma conta](/rest/api/batchservice/delete-a-pool-from-an-account).
