---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 12/23/2020
ms.author: alkohli
ms.openlocfilehash: 11958c54dd1f54e424b71eb00780f5309a1c0bab
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209535"
---
Aqui está uma lista de contas de armazenamento e tipos de armazenamento com suporte para um dispositivo Data Box. Para obter uma lista completa de todos os recursos de todos os tipos de contas de armazenamento, consulte [tipos de contas de armazenamento](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Para ordens de importação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Observações**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | S |
| Uso geral v1 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento do Azure Premium |  |  | S |  |  
| Armazenamento de blobs Standard |S | | |Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente. |

\*  *– Os dados carregados para blobs de páginas devem ser de 512 bytes alinhados, como VHDs.*

Para ordens de exportação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Camadas de acesso compatíveis**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | S | |
| Uso geral v1 Standard  | S | S | S | Quente, frio|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | S | S | Quente, frio|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento do Azure Premium |  |  | S |  |
| Armazenamento de blobs Standard |S | | |Quente, frio |
| Armazenamento de blobs de blocos Premium |S | | |Quente, frio |
| Armazenamento de blobs de páginas Premium | |S | | |

> [!IMPORTANT]
> - Para contas de uso geral, Data Box não oferece suporte a tipos de armazenamento de fila, tabela e disco para ordens de importação. Para ordens de exportação, Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas de uso geral.
> - Data Box não dá suporte a blobs de acréscimo para armazenamento de BLOB e contas de armazenamento de blob de bloco.
> - Os dados carregados em blobs de páginas devem ser 512 bytes alinhados como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de arquivos e instantâneos de BLOB não são exportados.