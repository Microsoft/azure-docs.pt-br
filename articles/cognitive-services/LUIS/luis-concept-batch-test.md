---
title: Teste em lote-LUIS
titleSuffix: Azure Cognitive Services
description: Use o teste em lote para trabalhar continuamente em seu aplicativo para aprimorá-lo e melhorar o reconhecimento do idioma.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b962fc32cdcde0509cfa60d105022bb208633ae3
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639294"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Lote de teste com 1.000 declarações no portal do LUIS

O teste em lote valida o modelo treinado [ativo](luis-concept-version.md#active-version) para medir a precisão da previsão. Um teste em lote ajuda a exibir a precisão de cada tentativa e entidade em seu modelo treinado atual, exibindo os resultados com um gráfico. Examine os resultados do teste em lote para tomar a devida ação para melhorar a precisão, como adicionar mais declarações de exemplo a uma intenção, caso seu aplicativo falhe com frequência em identificar a intenção correta.

## <a name="group-data-for-batch-test"></a>Agrupar dados para o teste em lote

É importante que as declarações usadas para o teste em lote sejam novas no LUIS. Se você tiver um conjunto de dados de declarações, divida o declarações em três conjuntos: exemplo declarações adicionado a uma intenção, declarações recebido do ponto de extremidade publicado e declarações usado para testar o lote LUIS depois de ser treinado. 

## <a name="a-data-set-of-utterances"></a>Um conjunto de dados de declarações

Envie um arquivo em lotes de declarações, conhecido como um *conjunto de dados*, para teste em lotes. O conjunto de dados é um arquivo formatado em JSON que contém um máximo de 1.000 rotulado como declarações **não duplicado** . Você pode testar até 10 conjuntos de dados em um aplicativo. Se você precisar testar mais, exclua um conjunto de dados e, em seguida, adicione um novo.

|**Regras**|
|--|
|*Nenhuma declaração duplicada|
|1\.000 declarações ou menos|

*As duplicatas serão consideradas correspondências da cadeia de caracteres exatas, não correspondências sinalizadas com token primeiro. 

## <a name="entities-allowed-in-batch-tests"></a>Entidades permitidas nos testes em lotes

Todas as entidades personalizadas no modelo aparecem no filtro de entidades de teste de lote, mesmo se não houver nenhuma entidade correspondente nos dados do arquivo em lotes.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formato do arquivo em lote

O arquivo em lote consiste em declarações. Cada declaração deve ter uma previsão de intenção esperada junto com qualquer [entidade aprendida por máquina](luis-concept-entity-types.md#types-of-entities) que você espera detectar. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe em lote para intenções com entidades

Use o modelo a seguir para iniciar o arquivo em lotes:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

O arquivo em lotes usa as propriedades **startPos** e **endPos** para observar o início e o fim de uma entidade. Os valores são baseados em zero e não devem iniciar ou terminar em um espaço. Isso é diferente dos logs de consulta, que usam as propriedades startIndex e endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Modelo de sintaxe em lote para intenções sem entidades

Use o modelo a seguir para iniciar o arquivo em lotes sem entidades:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Se você não deseja testar entidades, inclua a propriedade `entities` e defina o valor como uma matriz vazia, `[]`.


## <a name="common-errors-importing-a-batch"></a>Erros comuns ao importar um lote

Os erros comuns incluem: 

> * Mais de 1.000 declarações
> * Um objeto JSON de declaração que não tem uma propriedade de entidades. A propriedade pode ser uma matriz vazia.
> * Palavra(s) rotulada(s) em várias entidades
> * Rótulo de entidade inicial ou final em um espaço.

## <a name="batch-test-state"></a>Estado do teste em lote

LUIS rastreia o estado do último teste de cada conjunto de dados. Isso inclui o tamanho (número de declarações no lote), data da última execução e último resultado (número de declarações previstas com êxito).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Resultados do teste em lote

O resultado do teste em lote é um gráfico de dispersão, conhecido como matriz de erro. Esse gráfico é uma comparação de 4 vias das declarações no arquivo de lote, intenção prevista do modelo atual e entidades. 

Os pontos de dados nas seções **Falso Positivo** e **Falso Negativo** indicam os erros que devem ser investigados. Se todos os pontos de dados estiverem nas seções **verdadeiro positivo** e **verdadeiro negativo** , a precisão do aplicativo será perfeita nesse conjunto de dados.

![Quatro seções do gráfico](./media/luis-concept-batch-test/chart-sections.png)

Esse gráfico ajuda a encontrar as declarações que o LUIS prevê incorretamente com base em seu treinamento atual. Os resultados são exibidos por região do gráfico. Selecione os pontos individuais no gráfico para examinar as informações de declaração ou selecione o nome da região para examinar os resultados da declaração nessa região.

![Teste em lote](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erros nos resultados

Os erros no teste em lote indicam as intenções que não estão previstas, como indicado no arquivo em lote. Os erros são indicados nas duas seções vermelhas do gráfico. 

A seção de falso positivo indica que uma declaração correspondeu a uma intenção ou entidade quando não deveria. O falso negativo indica que uma declaração não correspondeu a uma intenção ou entidade quando deveria. 

## <a name="fixing-batch-errors"></a>Corrigindo erros em lote

Se houver erros no teste em lote, você poderá adicionar mais declarações a uma intenção e/ou rotular mais declarações com a entidade para ajudar o LUIS a diferenciar as intenções. Se você adicionou declarações, rotulou e ainda recebe erros de previsão no teste em lote, considere adicionar um recurso de [lista de frases](luis-concept-feature.md) com um vocabulário específico do domínio para ajudar o LUIS a aprender mais rápido. 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [testar um lote](luis-how-to-batch-test.md)
