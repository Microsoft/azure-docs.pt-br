---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172134"
---
| Resource | Destino        |
|----------|---------------|
| Tamanho máximo de um único contêiner de blob | Mesmo que a capacidade da conta de armazenamento máximo |
| Número máximo de blocos em um bloco de BLOBs ou de acréscimo | 50.000 blocos |
| Tamanho máximo de um bloco em um blob de blocos | 100 MiB |
| Tamanho máximo de um blob de blocos | 50.000 x 100 MiB (aproximadamente 4,75 TiB) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de um blob de página | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contêiner de blob | 5 |
|Taxa de transferência de destino para blob único |Até os limites de entrada/saída de conta de armazenamento<sup>1</sup> |

<sup>1</sup> taxa de transferência do único objeto depende de vários fatores, incluindo, mas não limitado a: simultaneidade, tamanho da solicitação, o nível de desempenho, velocidade do código-fonte para carregamentos e destino para downloads. Para aproveitar [blob de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) aprimoramentos de desempenho, use um tamanho de solicitação Put Blob ou Put Block > 4 MiB (> 256 KiB para armazenamento de blobs de bloco de desempenho premium ou para o Data Lake armazenamento Gen2).
