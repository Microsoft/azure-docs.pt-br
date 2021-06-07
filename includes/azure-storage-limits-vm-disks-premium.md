---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80334582"
---
**Discos de máquina virtual não gerenciados Premium: limites por conta**

| Recurso | Limite |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total de instantâneos por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Entrada* refere-se a todos os dados de solicitações que são enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados de respostas recebidos de uma conta de armazenamento.

**Discos de máquina virtual não gerenciados Premium: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1\.024 GiB (1 TB) |2\.048 GiB (2 TB)|4\.095 GiB (4 TB)|
| IOPS máxima por disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Taxa de transferência máxima por disco |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquina virtual não gerenciados Premium: limites por VM**

| Recurso | Limite |
| --- | --- |
| IOPS máxima por VM |80.000 IOPS com VM GS5 |
| Taxa de transferência máxima por VM |2 mil MB/s com VM GS5 |

