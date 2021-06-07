---
author: baanders
description: incluir arquivo para Gêmeos Digitais do Azure – Formas de gerenciar instância
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473757"
---
Este artigo destaca como concluir diferentes operações de gerenciamento usando o [**SDK** do .NET (C#) para os Gêmeos Digitais do Azure](/dotnet/api/overview/azure/digitaltwins/management). Você também pode criar essas mesmas chamadas de gerenciamento usando os outros SDKs de linguagem descritos em [*Instruções: usar as APIs e os SDKs dos Gêmeos Digitais do Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se de que todos os métodos do SDK vêm em versões síncronas e assíncronas. Para chamadas de paginação, os métodos assíncronos retornam `AsyncPageable<T>` enquanto as versões síncronas retornam `Pageable<T>`.

Outra opção de gerenciamento é chamar as [**APIs REST**](/rest/api/azure-digitaltwins/) dos Gêmeos Digitais do Azure para esta área de tópico diretamente, por meio de um cliente REST como o Postman. Para obter instruções sobre como fazer isso, confira [*Instruções: fazer solicitações com o Postman*](../articles/digital-twins/how-to-use-postman.md).

Por fim, você pode concluir as mesmas operações de gerenciamento usando a **CLI** dos Gêmeos Digitais do Azure. Para saber mais sobre como usar a CLI, confira [*Instruções: usar a CLI dos Gêmeos Digitais do Azure*](../articles/digital-twins/how-to-use-cli.md).