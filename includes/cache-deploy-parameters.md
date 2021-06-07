---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "67171731"
---
### <a name="cacheskuname"></a>cacheSKUName

O tipo de preço do novo Cache do Azure para Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

O modelo define os valores permitidos para esse parâmetro (Basic, Standard ou Premium) e atribui um valor padrão (Basic) se nenhum valor for especificado. Basic fornece um único nó com vários tamanhos disponíveis, até 53 GB. Standard fornece Principal/Réplica de dois nós com vários tamanhos disponíveis, até 53 GB e SLA de 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily

A família do SKU.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

O tamanho da nova instância do Cache do Azure para Redis.

Para as famílias básica e Standard:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

A capacidade de cache de valor Premium é definida da mesma, exceto que os valores permitidos são executados de 1 a 5 em vez de 0 a 6.

O modelo define os valores inteiros permitidos para esse parâmetro (de 0 a 6 para as famílias básica e Standard; 1 a 5 para a família Premium). Se nenhum valor for especificado, o modelo atribuirá um valor padrão de 0 para básico e Standard, 1 para Premium.

Os valores correspondem aos seguintes tamanhos de cache:

| Valor | Básico e Standard<br>tamanho do cache | Premium<br>tamanho do cache |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (padrão)                 | N/D                   |
| 1     | 1 GB                             | 6 GB (padrão)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | N/D                   |
