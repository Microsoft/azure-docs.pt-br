---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542651"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites de nível de compartilhamento de arquivo de premium adicionais

|Área  |Destino  |
|---------|---------|
|Aumentar/diminuir de tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB até 100.000|
|IOPS de intermitência    |3 x IOPS por GiB até 100.000|
|Taxa de saída         |60 MiB/s + 0,06 * provisionado GiB        |
|Taxa de entrada| 40 MiB/s + 0,04 * provisionado GiB |

#### <a name="file-level-limits"></a>Limites de nível de arquivo

|Área  |Arquivo Premium  |Arquivo padrão |
|---------|---------|---------|
|Tamanho                  |1 TiB         |1 TiB|
|IOPS máxima por arquivo     |5\.000         |1\.000|
|Identificadores simultâneos    |2\.000         |2\.000|
|Entrada  |300 MiB/s|      Consulte os valores da taxa de transferência de arquivo padrão|
|Saída   |200 Mib/s| Consulte os valores da taxa de transferência de arquivo padrão|
|Produtividade| Ver os valores de entrada/saída de arquivo do premium| Até 60 MiB/s|