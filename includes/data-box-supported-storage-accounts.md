---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706021"
---
Aqui está uma lista de contas de armazenamento e tipos de armazenamento com suporte para um dispositivo Data Box. Para obter uma lista completa de todos os recursos de todos os tipos de contas de armazenamento, consulte [tipos de contas de armazenamento](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Para ordens de importação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Observações**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | Y | S |
| Uso geral v1 Standard  | S | Y | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | Y | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento do Azure Premium |  |  | S |  |  
| Armazenamento de blobs Standard |S | | |Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente. |

\*  *– Os dados carregados para blobs de páginas devem ser de 512 bytes alinhados, como VHDs.*

Para ordens de exportação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Camadas de acesso compatíveis**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | Y | S | |
| Uso geral v1 Standard  | S | Y | Y | Quente, frio|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | Y | Y | Quente, frio|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento do Azure Premium |  |  | S |  |
| Armazenamento de blobs Standard |S | | |Quente, frio |
| Armazenamento de blobs de blocos Premium |Y | | |Quente, frio |
| Armazenamento de blobs de páginas Premium | |Y | | |

> [!IMPORTANT]
> - Para contas de uso geral, Data Box não oferece suporte a tipos de armazenamento de fila, tabela e disco para ordens de importação. Para ordens de exportação, Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas de uso geral.
> - Data Box não dá suporte a blobs de acréscimo para armazenamento de BLOB e contas de armazenamento de blob de bloco.
> - O suporte ao protocolo NFS (sistema de arquivos de rede) 3,0 no armazenamento de BLOBs do Azure não tem suporte com Data Box.
> - Os dados carregados em blobs de páginas devem ser 512 bytes alinhados como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de arquivos e instantâneos de BLOB não são exportados.