---
title: Criar uma chave de partição sintética no Azure Cosmos DB
description: Saiba como usar chaves de partição sintéticas em seus contêineres de Cosmos do Azure para distribuir os dados e a carga de trabalho uniformemente entre as chaves de partição
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 6b8bc44f1ba5624c37620205aaa574e618ef395f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340644"
---
# <a name="create-a-synthetic-partition-key"></a>Crie uma chave de partição sintética
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

É a melhor prática ter uma chave de partição com muitos valores distintos, como centenas ou milhares. O objetivo é distribuir seus dados e carga de trabalho uniformemente entre os itens associados a esses valores de chave de partição. Se essa propriedade não existir em seus dados, você poderá construir uma *chave de partição sintética*. Este documento descreve várias técnicas básicas para gerar uma chave de partição sintética para seu contêiner Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenar várias propriedades de um item

Você pode formar uma chave de partição concatenando vários valores de propriedade em uma única propriedade artificial `partitionKey`. Essas chaves são chamadas de chaves sintéticas. Por exemplo, considere o seguinte documento de exemplo:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Para o documento anterior, uma opção é definir /deviceId ou /date como a chave de partição. Use essa opção se você quiser particionar seu contêiner com base na ID ou na data do dispositivo. Outra opção é concatenar esses dois valores em uma propriedade `partitionKey` sintética que é usada como chave de partição.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Em cenários em tempo real, você pode ter milhares de itens em um banco de dados. Em vez de adicionar a chave sintética manualmente, defina a lógica do cliente para concatenar valores e inserir a chave sintética nos itens em seus contêineres do cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usar uma chave de partição com um sufixo aleatório

Outra estratégia possível para distribuir a carga de trabalho mais uniformemente é anexar um número aleatório no final do valor da chave de partição. Quando distribui itens dessa maneira, você pode executar operações de gravação paralelas nas partições.

Um exemplo é quando uma chave de partição representa uma data. Você pode escolher um número aleatório entre 1 e 400 e concatenar-o como um sufixo para a data. Esse método resulta em valores de chave de partição como  `2018-08-09.1` , `2018-08-09.2` e assim por diante  `2018-08-09.400` . Como você está randomizando a chave de partição, as operações de gravação no contêiner em cada dia são distribuídas uniformemente em várias partições. Este método resulta em melhor paralelismo e maior rendimento global.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usar uma chave de partição com sufixos previamente calculados 

A estratégia de sufixo aleatório pode melhorar muito a taxa de transferência de gravação, mas é difícil ler um item específico. Você não sabe o valor do sufixo que foi usado quando escreveu o item. Para facilitar a leitura de itens individuais, use a estratégia de sufixos previamente calculados. Em vez de usar um número aleatório para distribuir os itens entre as partições, use um número que seja calculado com base em algo que você deseja consultar.

Considere o exemplo anterior, em que um contêiner usa uma data como a chave de partição. Agora suponha que cada item tenha um  `Vehicle-Identification-Number` `VIN` atributo () que desejamos acessar. Além disso, suponha que você geralmente execute consultas para localizar itens pelo `VIN` , além de data. Antes de seu aplicativo gravar o item no contêiner, ele pode calcular um sufixo hash com base no VIN e anexá-lo à data da chave de partição. O cálculo pode gerar um número entre 1 e 400 que é distribuído uniformemente. Esse resultado é semelhante aos resultados produzidos pelo método de estratégia de sufixo aleatório. O valor da chave de partição será, então, a data concatenada com o resultado calculado.

Com essa estratégia, as gravações são distribuídas uniformemente pelos valores da chave de partição e pelas partições. Você pode ler facilmente um item e uma data específicos, pois você pode calcular o valor da chave de partição para um específico `Vehicle-Identification-Number` . O benefício desse método é que você pode evitar a criação de uma única chave de partição ativa, ou seja, uma chave de partição que usa toda a carga de trabalho. 

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o conceito de particionamento nos seguintes artigos:

* Saiba mais sobre [partições lógicas](partitioning-overview.md).
* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
