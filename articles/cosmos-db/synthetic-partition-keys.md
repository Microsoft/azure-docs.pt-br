---
title: Crie uma chave de partição sintética no Azure Cosmos DB para distribuir seus dados e a carga de trabalho uniformemente.
description: Aprenda a usar chaves de partição sintéticas em seus contêineres do Azure Cosmos
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937093"
---
# <a name="create-a-synthetic-partition-key"></a>Crie uma chave de partição sintética

É a prática recomendada ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir seus dados e carga de trabalho uniformemente entre os itens associados a esses valores de chave de partição. Se essa propriedade não existir em seus dados, você pode construir uma *chave de partição sintético*. Este documento descreve várias técnicas básicas para gerar uma chave de partição sintético para seu contêiner do Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenar várias propriedades de um item

Você pode formar uma chave de partição concatenando vários valores de propriedade em uma única propriedade artificial `partitionKey`. Essas chaves são chamadas de chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir /deviceId ou /date como a chave de partição. Use essa opção, se você deseja particionar seu contêiner com base na ID do dispositivo ou data. Outra opção é concatenar esses dois valores em uma propriedade `partitionKey` sintética que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, você pode ter milhares de itens em um banco de dados. Em vez de adicionar manualmente a chave sintética, defina a lógica do lado do cliente para concatenar os valores e inserir a chave sintética os itens em seus contêineres do Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usar uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho mais uniformemente é anexar um número aleatório no final do valor da chave de partição. Quando distribui itens dessa maneira, você pode executar operações de gravação paralelas nas partições.

Um exemplo é quando uma chave de partição representa uma data. Você pode escolher um número aleatório entre 1 e 400 e concatená-lo como um sufixo para a data. Esse método resulta em valores de chave de partição, como `2018-08-09.1`,`2018-08-09.2`e assim por diante, por meio de `2018-08-09.400`. Como você está randomizando a chave de partição, as operações de gravação no contêiner em cada dia são distribuídas uniformemente em várias partições. Este método resulta em melhor paralelismo e maior rendimento global.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usar uma chave de partição com sufixos pré-calculados 

A estratégia de sufixo aleatório poderá melhorar consideravelmente a taxa de transferência de gravação, mas é difícil de ler um item específico. Você não souber o valor de sufixo que foi usado quando você o escreveu o item. Para tornar mais fácil de ler itens individuais, use a estratégia de sufixos pré-calculados. Em vez de usar um número aleatório para distribuir os itens entre as partições, use um número é calculado com base em algo que você deseja consultar.

Considere o exemplo anterior, em que um contêiner usa uma data como a chave de partição. Agora suponha que cada item tem um `Vehicle-Identification-Number` (`VIN`) atributo que desejamos acessar. Além disso, suponha que você geralmente executa consultas para localizar itens pelo `VIN`, além de data. Antes de seu aplicativo gravar o item no contêiner, ele pode calcular um sufixo hash com base no VIN e anexá-lo à data da chave de partição. O cálculo poderá gerar um número entre 1 e 400 é distribuído uniformemente. Esse resultado é semelhante a resultados produzidos pelo método estratégia sufixo aleatório. O valor da chave de partição será, então, a data concatenada com o resultado calculado.

Com essa estratégia, as gravações são distribuídas uniformemente pelos valores da chave de partição e pelas partições. Facilmente você pode ler um item específico e uma data, porque você pode calcular o valor de chave de partição para um determinado `Vehicle-Identification-Number`. A vantagem desse método é que você pode evitar a criação de uma chave de partição ativa única, ou seja, uma chave de partição que usa a carga de trabalho. 

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o conceito de particionamento nos seguintes artigos:

* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
