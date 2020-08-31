---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205708"
---
O código para todas as funções em um aplicativo de funções específico está localizado em uma pasta do projeto raiz que contém um arquivo de configuração do host e uma ou mais subpastas. Cada subpasta contém o código para uma função separada. A estrutura de pastas é mostrada na seguinte representação:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Na versão 2. x e superior do tempo de execução do functions, todas as funções no aplicativo de funções devem compartilhar a mesma pilha de idiomas.  

O arquivo [host.json](../articles/azure-functions/functions-host-json.md) contém configurações específicas de tempo de execução e está na pasta raiz do aplicativo de funções. Uma pasta *bin* contém pacotes e outros arquivos de biblioteca que o aplicativo de funções requer. Consulte os requisitos específicos a um idioma para um projeto de aplicativo de função:

* [Biblioteca de classes C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script do C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script em F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
