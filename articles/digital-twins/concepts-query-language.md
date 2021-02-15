---
title: Idioma de consulta
titleSuffix: Azure Digital Twins
description: Entenda os conceitos básicos da linguagem de consulta do Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 742cff544886a1499bccfa575684edef708da7bd
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028352"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para o gêmeos digital do Azure

Lembre-se de que o centro do Azure digital gêmeos é o [gráfico de entrelaçamento](concepts-twins-graph.md), construído de gêmeos e relações digitais. 

Esse grafo pode ser consultado para obter informações sobre o gêmeos digital e as relações que ele contém. Essas consultas são escritas em uma linguagem de consulta do tipo SQL personalizada, conhecida como a **linguagem de consulta do Azure digital gêmeos**. Isso é semelhante à [linguagem de consulta do Hub IOT](../iot-hub/iot-hub-devguide-query-language.md) com muitos recursos comparáveis.

Este artigo descreve os conceitos básicos da linguagem de consulta e seus recursos. Para obter exemplos mais detalhados de sintaxe de consulta e como executar solicitações de consulta, consulte [*como consultar o grafo de entrelaçamento*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Sobre as consultas

Você pode usar a linguagem de consulta gêmeos digital do Azure para recuperar o gêmeos digital de acordo com seus...
* Propriedades (incluindo [Propriedades de marca](how-to-use-tags.md))
* modelos
* relacionamentos
  - Propriedades das relações

Para enviar uma consulta ao serviço de um aplicativo cliente, você usará a [**API de consulta**](/rest/api/digital-twins/dataplane/query)do gêmeos digital do Azure. Uma maneira de usar a API é por meio de um dos [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) do Azure digital gêmeos.

## <a name="reference-expressions-and-conditions"></a>Referência: expressões e condições

Esta seção descreve os operadores e funções que estão disponíveis para gravar consultas do Azure digital gêmeos. Por exemplo, consultas que ilustram o uso desses recursos, consulte [*instruções: consultar o grafo de entrelaçamento*](how-to-query-graph.md).

> [!NOTE]
> Todas as operações de consulta do Azure digital gêmeos diferenciam maiúsculas de minúsculas, portanto, tome cuidado para usar os nomes exatos definidos nos modelos. Se os nomes de propriedade forem digitados incorretamente ou em maiúsculas e minúsculas, o conjunto de resultados estará vazio sem erros retornados.

### <a name="operators"></a>Operadores

Há suporte para os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Lógico |`AND`, `OR`, `NOT` |
| Comparação | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contém | `IN`, `NIN` |

### <a name="functions"></a>Funções

Há suporte para as seguintes funções de verificação e conversão de tipo:

| Função | Descrição |
| -------- | ----------- |
| `IS_DEFINED` | Retorna um valor booliano que indica se um valor foi atribuído à propriedade. Isso só tem suporte quando o valor é um tipo primitivo. Tipos primitivos incluem cadeia de caracteres, booliano, numérico ou `null` . `DateTime`, tipos de objeto e matrizes não têm suporte. |
| `IS_OF_MODEL` | Retorna um valor booliano que indica se a propriedade de texto especificada corresponde ao tipo de modelo especificado |
| `IS_BOOL` | Retorna um valor booliano que indica se o tipo da expressão especificada é um booliano. |
| `IS_NUMBER` | Retorna um valor booliano que indica se o tipo da expressão especificada é um número. |
| `IS_STRING` | Retorna um valor booliano que indica se o tipo da expressão especificada é uma cadeia de caracteres. |
| `IS_NULL` | Retorna um valor booliano que indica se o tipo da expressão especificada é nulo. |
| `IS_PRIMITIVE` | Retorna um valor booleano indicando se o tipo da expressão especificada é um primitivo (cadeia de caracteres, booliano, numérico ou `null`). |
| `IS_OBJECT` | Retorna um valor booliano que indica se o tipo da expressão especificada é um objeto JSON. |

Há suporte para as seguintes funções de cadeia de caracteres:

| Função | Descrição |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda. |
| `ENDSWITH(x, y)` | Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda. |

## <a name="query-limitations"></a>Limitações da consulta

Esta seção descreve as limitações da linguagem de consulta.

* Tempo: pode haver um atraso de até 10 segundos antes que as alterações em sua instância sejam refletidas em consultas. Por exemplo, se você concluir uma operação como criar ou excluir gêmeos com a API DigitalTwins, o resultado poderá não ser refletido imediatamente nas solicitações de API de consulta. Aguardar um curto período de tempo deve ser suficiente para resolver.
* Nenhuma subconsulta é suportada na `FROM` instrução.
* `OUTER JOIN` Não há suporte para semântica, o que significa que, se a relação tiver uma classificação igual a zero, a "linha" inteira será eliminada do conjunto de resultados de saída.
* A profundidade de percurso do grafo é restrita a cinco `JOIN` níveis por consulta.
* A origem das `JOIN` operações é restrita: a consulta deve declarar o gêmeos onde a consulta começa.

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever consultas e ver exemplos de código de cliente em [*como: consultar o grafo de entrelaçamento*](how-to-query-graph.md).