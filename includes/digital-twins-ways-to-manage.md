---
author: baanders
description: arquivo de inclusão para o Azure digital gêmeos-maneiras de gerenciar a instância
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473757"
---
Este artigo destaca como concluir operações de gerenciamento diferentes usando o [ **SDK** do .net do Azure digital gêmeos (C#)](/dotnet/api/overview/azure/digitaltwins/management). Você também pode criar essas mesmas chamadas de gerenciamento usando os outros SDKs de linguagem descritos em [*How-to: Use the Azure digital gêmeos APIs and SDKs*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se de que todos os métodos do SDK vêm em versões síncronas e assíncronas. Para chamadas de paginação, os métodos assíncronos retornam `AsyncPageable<T>` enquanto as versões síncronas retornam `Pageable<T>` .

Outra opção de gerenciamento é chamar as [**APIs REST**](/rest/api/azure-digitaltwins/) do Azure digital gêmeos para esta área de tópico diretamente, por meio de um cliente REST como o postmaster. Para obter instruções sobre como fazer isso, consulte instruções [*: fazer solicitações com o postmaster*](../articles/digital-twins/how-to-use-postman.md).

Por fim, você pode concluir as mesmas operações de gerenciamento usando a **CLI** digital gêmeos do Azure. Para saber mais sobre como usar a CLI, consulte [*como usar a CLI do Azure digital gêmeos*](../articles/digital-twins/how-to-use-cli.md).