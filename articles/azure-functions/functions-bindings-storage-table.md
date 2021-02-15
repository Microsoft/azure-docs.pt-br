---
title: Associações de armazenamento de tabelas do Azure Functions
description: Entenda como usar as associações de armazenamento da Tabela do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096718"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associações de armazenamento de tabelas do Azure Functions

O Azure Functions integra-se com o [armazenamento do Azure](../storage/index.yml) por meio [de gatilhos e associações](./functions-triggers-bindings.md). A integração com o armazenamento de tabelas permite que você crie funções que lêem e gravam dados de armazenamento de tabelas.

| Ação | Tipo |
|---------|---------|
| Ler dados de armazenamento de tabela em uma função | [Associação de entrada](./functions-bindings-storage-table-input.md) |
| Permitir que uma função grave dados de armazenamento de tabela |[Associação de saída](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações de Armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versão 2.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Próximas etapas

- [Ler dados de armazenamento de tabela quando uma função é executada](./functions-bindings-storage-table-input.md)
- [Gravar dados de armazenamento de tabela de uma função](./functions-bindings-storage-table-output.md)
