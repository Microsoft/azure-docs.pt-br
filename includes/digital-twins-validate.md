---
author: baanders
description: arquivo de inclusão descrevendo como validar modelos de gêmeos digitais do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025734"
---
> [!TIP]
> Depois de criar um modelo, é recomendável validar seus modelos offline antes de carregá-los em sua instância de gêmeos digital do Azure.

Há um exemplo independente de linguagem disponível para validar documentos de modelo para verificar se o DTDL está correto antes de carregá-lo em sua instância. Ele está localizado aqui: [**exemplo de validador de DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* O exemplo do validador DTDL é criado em uma biblioteca do analisador do .NET DTDL, que está disponível no NuGet como uma biblioteca do lado do cliente: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Você também pode usar a biblioteca diretamente para criar sua própria solução de validação. Ao usar a biblioteca do analisador, certifique-se de usar uma versão compatível com a versão que o Azure digital gêmeos está executando. Atualmente, essa é a versão *3.12.4*.

Você pode aprender mais sobre a amostra do validador e a biblioteca do analisador, incluindo exemplos de uso, em [*instruções: analisar e validar modelos*](../articles/digital-twins/how-to-parse-models.md).