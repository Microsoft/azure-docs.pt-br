---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 844e4a0d2715799b808f2c7630c201f6e792bd63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582782"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho da camada de imagem máximo | 20 GiB | 20 GiB | 50 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1.000 | 3.000 | 10.000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2.000 |
| MBps de largura de banda de download<sup>2</sup> | 30 | 60 | 100 |
| MBps de largura de banda de upload<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicação geográfica | N/D | N/D | [Com suporte][geo-replication] |
| Confiança do conteúdo (versão prévia) | N/D | N/D | [Com suporte][content-trust] |

<sup>1</sup>os limites de armazenamento especificado são a quantidade de *incluídos* armazenamento para cada camada. Será cobrada uma taxa diária adicional por GiB para o armazenamento de imagens acima desses limites. Para obter informações de taxa, consulte [registro de contêiner do Azure de preços][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*, e *largura de banda* são estimativas mínimas. O registro de contêiner do Azure se esforça para melhorar o desempenho como requer o uso.

<sup>3</sup>um [pull do docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>4</sup>um [push do docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação, com base no número de camadas que devem ser enviados por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
