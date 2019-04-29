---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731151"
---
O código para todas as funções em um aplicativo de funções específico está localizado em uma pasta do projeto raiz que contém um arquivo de configuração do host e uma ou mais subpastas. Cada subpasta contém o código para uma função separada, como na seguinte representação:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Na versão 2.x do tempo de execução do Functions, todas as funções no aplicativo de funções devem compartilhar o mesmo trabalhado de idioma.  

O arquivo [host.json](../articles/azure-functions/functions-host-json.md), que contém algumas configurações específicas de tempo de execução, está na pasta raiz do aplicativo de funções. Uma pasta `bin` contém pacotes e outros arquivos de biblioteca exigidos pelo aplicativo de funções. Consulte os requisitos específicos a um idioma para um projeto de aplicativo de função:

- [Biblioteca de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [Script do C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [Script em F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

