---
title: Funções definidas pelo usuário no Azure Stream Analytics
description: Este artigo é uma visão geral das funções definidas pelo usuário no Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020495"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo usuário no Azure Stream Analytics

A linguagem de consulta do tipo SQL no Azure Stream Analytics facilita a implementação da lógica de análise em tempo real em dados de streaming. Stream Analytics fornece flexibilidade adicional por meio de funções personalizadas que são invocadas em sua consulta. O exemplo de código a seguir é um UDF chamado `sampleFunction` que aceita um parâmetro, cada registro de entrada que o trabalho recebe e o resultado é gravado na saída como `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipos de funções

O Azure Stream Analytics dá suporte aos quatro tipos de função a seguir: 

* Funções definidas pelo usuário de JavaScript 
* Agregações definidas pelo usuário do JavaScript 
* Funções definidas pelo usuário do C# (usando o Visual Studio) 
* Azure Machine Learning 

Você pode usar essas funções para cenários como pontuação em tempo real usando modelos de aprendizado de máquina, manipulações de cadeia de caracteres, cálculos matemáticos complexos, codificação e decodificação de dados. 

## <a name="limitations"></a>Limitações

As funções definidas pelo usuário são sem estado e o valor de retorno só pode ser um valor escalar. Você não pode chamar pontos de extremidade REST externos dessas funções definidas pelo usuário, pois isso provavelmente afetará o desempenho do seu trabalho. 

Azure Stream Analytics não mantém um registro de todas as invocações de funções e retornou resultados. Para garantir a capacidade de repetição – por exemplo, executar novamente seu trabalho de carimbo de data/hora antigo produz os mesmos resultados novamente – não use funções como `Date.GetData()` ou `Math.random()` , pois essas funções não retornam o mesmo resultado para cada invocação.  

## <a name="resource-logs"></a>Logs de recursos

Os erros de tempo de execução são considerados fatais e são exibidos por meio de logs de atividade e de recursos. É recomendável que sua função manipule todas as exceções e erros e retorne um resultado válido para sua consulta. Isso impedirá que seu trabalho vá para um [estado de falha](job-states.md).  

## <a name="exception-handling"></a>Tratamento de exceções

Qualquer exceção durante o processamento de dados é considerada uma falha catastrófica ao consumir dados no Azure Stream Analytics. As funções definidas pelo usuário têm um potencial maior para lançar exceções e fazer com que o processamento pare. Para evitar esse problema, use um bloco *try-catch* em JavaScript ou C# para capturar exceções durante a execução de código. As exceções que são detectadas podem ser registradas e tratadas sem causar uma falha do sistema. Você é incentivado a sempre encapsular seu código personalizado em um bloco *try-catch* para evitar lançar exceções inesperadas no mecanismo de processamento.

## <a name="next-steps"></a>Próximas etapas

* [Funções definidas pelo usuário do JavaScript no Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Agregações definidas pelo usuário do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md)
* [Desenvolver .NET Standard funções definidas pelo usuário para trabalhos de Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integrar o Azure Stream Analytics com o Azure Machine Learning](machine-learning-udf.md)
