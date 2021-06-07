---
title: 'Remover linhas duplicadas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo remover linhas duplicadas no Azure Machine Learning para remover duplicatas potenciais de um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905210"
---
# <a name="remove-duplicate-rows-module"></a>Remover o módulo de linhas duplicadas

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para remover duplicatas potenciais de um conjunto de uma.

Por exemplo, suponha que seus dados sejam semelhantes ao seguinte e representem vários registros para pacientes. 

| PatientID | Iniciais| Sexo|Idade|Verdade|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Fev|
|4| F.M.| M| 23| Fev|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem várias colunas com dados potencialmente duplicados. Se eles são, na verdade, duplicatas, depende de seu conhecimento dos dados. 

+ Por exemplo, você pode saber que muitos pacientes têm o mesmo nome. Você não eliminaria duplicatas usando colunas de nome, apenas a coluna de **ID** . Dessa forma, somente as linhas com valores de ID duplicados são filtradas, independentemente de os pacientes terem o mesmo nome ou não.

+ Como alternativa, você pode optar por permitir duplicatas no campo ID e usar alguma outra combinação de arquivos para localizar registros exclusivos, como nome, sobrenome, idade e sexo.  

Para definir os critérios para se uma linha está duplicada ou não, especifique uma única coluna ou um conjunto de colunas para usar como **chaves**. Duas linhas são consideradas duplicatas somente quando os valores em **todas as** colunas de chave são iguais. Se qualquer linha tiver um valor ausente para **as chaves**, elas não serão consideradas linhas duplicadas. Por exemplo, se sexo e idade forem definidos como chaves na tabela acima, a linha 6 e 7 não serão linhas duplicadas, dado que elas têm valor ausente na idade.

Quando você executa o módulo, ele cria um conjunto de registros candidato e retorna um conjunto de linhas que não possuem duplicatas no conjunto de colunas especificado.

> [!IMPORTANT]
> O conjunto de fonte de origem não é alterado; Esse módulo cria um novo conjunto de um que é filtrado para excluir duplicatas, com base nos critérios que você especificar.

## <a name="how-to-use-remove-duplicate-rows"></a>Como usar remover linhas duplicadas

1. Adicione o módulo ao seu pipeline. Você pode encontrar o módulo **remover linhas duplicadas** em **transformação de dados**, **manipulação**.  

2. Conecte o conjunto de registros que você deseja verificar em busca de linhas duplicadas.

3. No painel **Propriedades** , em **expressão de filtro de seleção de coluna de chave**, clique em **Iniciar seletor de coluna**, para escolher as colunas a serem usadas na identificação de duplicatas.

    Nesse contexto, a **chave** não significa um identificador exclusivo. Todas as colunas que você seleciona usando o seletor de coluna são designadas como **colunas de chave**. Todas as colunas não selecionadas são consideradas colunas não chave. A combinação de colunas que você seleciona como chaves determina a exclusividade dos registros. (Imagine-o como uma instrução SQL que usa várias junções equalities.)

    Exemplos:

    + "Quero garantir que as IDs sejam exclusivas": escolha apenas a coluna ID.
    + "Desejo garantir que a combinação de nome, sobrenome e ID seja exclusiva": selecione todas as três colunas.

4. Use a caixa de seleção **reter primeira linha duplicada** para indicar qual linha retornar quando duplicatas forem encontradas:

    + Se selecionado, a primeira linha será retornada e outras descartadas. 
    + Se você desmarcar essa opção, a última linha duplicada será mantida nos resultados e outras serão descartadas. 

5. Envie o pipeline.

6. Para examinar os resultados, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

> [!TIP]
> Se os resultados forem difíceis de entender ou se você quiser excluir algumas colunas da consideração, poderá remover colunas usando o módulo [selecionar colunas no conjunto de conjuntos](./select-columns-in-dataset.md) de linhas de base.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 