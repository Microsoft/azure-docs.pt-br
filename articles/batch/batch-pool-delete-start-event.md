---
title: Evento inicial de exclusão do pool do Lote do Azure
description: Referência para excluir um pool de lote evento inicial. Esse evento é emitido quando uma operação de exclusão de pool é iniciada.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803741"
---
# <a name="pool-delete-start-event"></a>Evento inicial de exclusão de pool

 Esse evento é emitido quando uma operação de exclusão de pool é iniciada. Como a exclusão do pool é um evento assíncrono, você pode esperar que um evento completo de conclusão de exclusão de pool seja emitido quando a operação de exclusão é concluída.

 O exemplo a seguir mostra o corpo de um evento de início de exclusão de pool.

```
{
   "id": "myPool1"
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
