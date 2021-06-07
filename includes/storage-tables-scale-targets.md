---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78940912"
---
A tabela a seguir descreve a capacidade, escalabilidade e metas de desempenho para o Armazenamento de Tabelas.

| Recurso | Destino |
|----------|---------------|
| Número de tabelas em uma conta de armazenamento do Azure | Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições em uma tabela | Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em uma partição | Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho máximo de uma única tabela | 500 TiB |
| Tamanho máximo de uma única entidade, incluindo todos os valores de propriedade | 1 MiB |
| Número máximo de propriedades em uma entidade de tabela | 255 (incluindo as três propriedades do sistema, **PartitionKey**, **RowKey** e **Timestamp**) |
| Tamanho total máximo de uma propriedade individual em uma entidade | Varia por tipo de propriedade. Para obter mais informações, confira a seção **Tipos de propriedade** em [Noções básicas sobre o modelo de dados do serviço tabela](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Tamanho do **PartitionKey** | Uma cadeia de caracteres de até 1 KiB |
| Tamanho do **RowKey** | Uma cadeia de caracteres de até 1 KiB |
| Tamanho de uma transação de grupo de entidades | Uma transação pode incluir no máximo 100 entidades e o conteúdo deve ser menor que 4 MiB. Uma transação de grupo de entidades pode incluir uma atualização para uma entidade apenas uma vez. |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de solicitação por conta de armazenamento | 20 mil transações por segundo, o que supõe um tamanho de entidade de 1 KiB |
| Taxa de transferência de destino para uma partição de tabela única (entidades de 1 KiB) | Até 2 mil entidades por segundo |