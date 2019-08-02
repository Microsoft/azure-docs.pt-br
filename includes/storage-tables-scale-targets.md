---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457212"
---
| Resource | Destino |
|----------|---------------|
| Tamanho máximo de uma única tabela | 500 TiB |
| Tamanho máximo de uma entidade de tabela | 1 MiB |
| Número máximo de propriedades em uma entidade de tabela | 255, que inclui três propriedades do sistema: PartitionKey, RowKey e Timestamp |
| Tamanho máximo total de valores de propriedade em uma entidade | 1 MiB |
| Tamanho máximo total de uma propriedade individual em uma entidade | Varia por tipo de propriedade. Para obter mais informações, consulte **tipos de propriedade** na [Noções básicas sobre o modelo de dados do serviço de tabela](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de solicitação por conta de armazenamento | 20.000 transações por segundo, que assume um tamanho de entidade de 1 KiB |
| Taxa de transferência de destino para uma partição de tabela única (-entidades de 1 KiB) | Até 2.000 entidades por segundo |