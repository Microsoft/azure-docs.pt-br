---
title: Descompasso do Esquema do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Construa fluxos de dados resistentes no Azure Data Factory com descompasso de esquema
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: aadab68185347dc0a12e0802f675efe13ecea545
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261794"
---
# <a name="mapping-data-flow-schema-drift"></a>Descompasso do Esquema do Fluxo de Dados do Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

O conceito de Descompasso do Esquema consiste no caso em que as suas fontes alteram frequentemente os metadados. Campos, colunas, tipos, etc. podem ser adicionados, removidos ou alterados em tempo real. Sem o manuseio do Descompasso do Esquema, seu Fluxo de Dados se torna vulnerável a mudanças nas alterações da fonte de dados upstream. Quando as colunas e os campos de entrada são alterados, os padrões típicos de ETL falham porque tendem a estar vinculados a esses nomes de origem.

Para proteger contra o Descompasso do Esquema, é importante ter recursos em uma ferramenta de Fluxo de Dados para permitir que você, como Engenheiro de Dados:

* Defina fontes que tenham nomes de campos, tipos de dados, valores e tamanhos mutáveis
* Defina parâmetros de transformação que funcionam com padrões de dados ao invés de campos e valores codificados
* Defina expressões que compreendam padrões para corresponder aos campos recebidos, ao invés de usar campos nomeados

## <a name="how-to-implement-schema-drift"></a>Como implementar o descompasso do esquema

* Escolha "Permitir descompasso de esquema" na sua transformação de fonte

<img src="media/data-flow/schemadrift001.png" width="400">

* Ao selecionar essa opção, todos os campos recebidos são lidos da sua fonte em cada execução do Fluxo de Dados e são passados por todo o fluxo até o Coletor.

* Certifique-se de usar o "Mapeamento Automático" para mapear todos os novos campos na Transformação de Coletor para que todos os novos campos sejam coletados e descarregados no seu destino:

<img src="media/data-flow/automap.png" width="400">

* Tudo funcionará quando novos campos forem introduzidos nesse cenário com um mapeamento simples de Fonte -> Coletor (ou Cópia).

* Para adicionar transformações a esse fluxo de trabalho que manuseia o descompasso de esquema, use a correspondência de padrões para corresponder colunas por nome, tipo e valor.

* Clique em "Adicionar padrão de coluna" na transformação Coluna Derivada ou Agregação se quiser criar uma transformação que compreenda o "Descompasso de Esquema".

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Você precisa tomar uma decisão sobre a arquitetura de seu fluxo de dados para aceitar o descompasso de esquema em todo o fluxo. Ao fazer isso, você pode proteger contra alterações de esquema das fontes. No entanto, você perde a vinculação antecipada de suas colunas e tipos em todo o fluxo de dados. O Azure Data Factory trata os fluxos de descompasso de esquema como fluxos de vinculação tardia, portanto, ao criar suas transformações, os nomes das colunas não ficam disponíveis nas visualizações de esquema durante todo o fluxo.

<img src="media/data-flow/taxidrift1.png" width="400">

No fluxo de dados da amostra Taxi Demo, há um exemplo de descompasso de esquema no fluxo de dados inferior com a fonte TripFare. Na transformação Agregar, observe que estamos usando o design de "padrão de coluna" para os campos de agregação. Ao invés de nomear colunas específicas ou procurar colunas por posição, presumimos que os dados podem mudar e podem não aparecer na mesma ordem entre as execuções.

Neste exemplo de manuseio do descompasso de esquema do Fluxo de Dados do Azure Data Factory, criamos uma agregação que analisa colunas do tipo 'duplo', sabendo que o domínio de dados contém preços para cada viagem. Podemos, então, executar um cálculo matemático agregado em todos os campos duplos na fonte, independentemente de onde a coluna está e da nomenclatura da coluna.

A sintaxe do Fluxo de Dados do Azure Data Factory usa $$ para representar cada coluna correspondente ao seu padrão. Você também pode corresponder nomes de coluna usando funções complexas de pesquisa de cadeia de caracteres e expressão regular. Nesse caso, vamos criar um novo nome de campo agregado com base em cada correspondência de um tipo de coluna 'duplo' e anexar o texto ```_total``` a cada um desses nomes correspondentes: 

```concat($$, '_total')```

Em seguida, arredondaremos e somaremos os valores de cada uma dessas colunas correspondentes:

```round(sum ($$))```

Você pode testar isso com a amostra "Taxi Demo" do Fluxo de Dados do Azure Data Factory. Ative a sessão de depuração usando a opção Depurar na parte superior da superfície de design do Fluxo de Dados para poder ver os resultados de forma interativa:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Acesso novas colunas downstream

Quando você gera novas colunas com padrões de coluna, você pode acessar essas novas colunas posteriormente no seu transformações de fluxo de dados usando a função de expressão "byName".

## <a name="next-steps"></a>Próximas etapas

No [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md) você encontrará recursos adicionais para os padrões de coluna e o descompasso do esquema incluindo "byName" e "byPosition".
