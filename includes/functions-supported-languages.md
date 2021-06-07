---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623257"
---
|Idioma                                 |1.x         |2. x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[Versão Prévia (.NET 5.0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (Nó 6)|GA (Node 10 e 8)| GA (Node 14, 12 e 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2.2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/D|GA (Java 8)| GA (Java 11 e 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/D|GA (PowerShell Core 6)| GA (PowerShell 7 e Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/D|GA (Python 3.7 e 3.6)| GA (Python 3.8, 3.7 e 3.6) <br/> Versão prévia (Python 3.9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/D|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> Os aplicativos da biblioteca de classes .NET voltados para o runtime versão 2.x agora podem ser executados no .NET Core 3.1 no modo de compatibilidade do .NET Core 2.x. Para saber mais, confira [Considerações sobre funções da v2.x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Suporte por meio de transcompilação para JavaScript.

Consulte o artigo do guia do desenvolvedor específico a um idioma para obter mais informações sobre as versões de idiomas compatíveis.   
Para obter informações sobre alterações planejadas para o suporte de linguagem, consulte o [roteiro do Azure](https://azure.microsoft.com/roadmap/?tag=functions).
