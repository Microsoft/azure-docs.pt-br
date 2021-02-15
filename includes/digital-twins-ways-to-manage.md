---
author: baanders
description: arquivo de inclusão para o Azure digital gêmeos-maneiras de gerenciar a instância
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231405"
---
Este artigo destaca como concluir operações de gerenciamento diferentes usando o [ **SDK** do .net do Azure digital gêmeos (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Você também pode criar essas mesmas chamadas de gerenciamento usando os outros SDKs de linguagem descritos em [*How-to: Use the Azure digital gêmeos APIs and SDKs*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Lembre-se de que todos os métodos do SDK vêm em versões síncronas e assíncronas. Para chamadas de paginação, os métodos assíncronos retornam `AsyncPageable<T>` enquanto as versões síncronas retornam `Pageable<T>` .

Outra opção de gerenciamento é chamar as [**APIs REST**](/rest/api/azure-digitaltwins/) do Azure digital gêmeos para esta área de tópico diretamente, por meio de um cliente REST como o postmaster. Para obter instruções sobre como fazer isso, consulte instruções [*: fazer solicitações com o postmaster*](../articles/digital-twins/how-to-use-postman.md).

Por fim, você pode concluir as mesmas operações de gerenciamento usando a **CLI** digital gêmeos do Azure. Para saber mais sobre como usar a CLI, consulte [*como usar a CLI do Azure digital gêmeos*](../articles/digital-twins/how-to-use-cli.md).