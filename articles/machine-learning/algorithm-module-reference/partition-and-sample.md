---
title: 'Partição e exemplo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo partição e exemplo no Azure Machine Learning para executar a amostragem em um conjunto de informações ou para criar partições a partir de seu conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898481"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e exemplo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use o módulo partição e exemplo para executar a amostragem em um conjunto de um ou para criar partições do conjunto de seus.

A amostragem é uma ferramenta importante na aprendizagem por máquina porque permite reduzir o tamanho de um conjunto de dados, mantendo a mesma proporção de valores. Este módulo dá suporte a várias tarefas relacionadas que são importantes no aprendizado de máquina: 

- Dividir seus dados em várias subseções do mesmo tamanho. 

  Você pode usar as partições para validação cruzada ou para atribuir casos a grupos aleatórios.

- Separar dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

  Depois de atribuir aleatoriamente casos a grupos diferentes, talvez seja necessário modificar os recursos associados a apenas um grupo.

- Exemplos. 

  Você pode extrair uma porcentagem dos dados, aplicar a amostragem aleatória ou escolher uma coluna a ser usada para balancear o conjunto e executar a amostragem de sobreratificação em seus valores.

- Criando um conjunto de um DataSet menor para teste. 

  Se você tiver muitos dados, talvez queira usar apenas as primeiras *n* linhas ao configurar o pipeline e, em seguida, alternar para usando o conjunto de dados completo ao criar seu modelo. Você também pode usar a amostragem para criar um conjunto de um DataSet menor para uso no desenvolvimento.

## <a name="configure-the-module"></a>Configurar o módulo

Este módulo dá suporte aos seguintes métodos para dividir seus dados em partições ou para amostragem. Escolha o método primeiro e, em seguida, defina as opções adicionais que o método requer.

- Head
- amostragem
- Atribuir a dobras
- Selecionar dobra

### <a name="get-top-n-rows-from-a-dataset"></a>Obter as N primeiras linhas de um conjunto de registros

Use este modo para obter apenas as primeiras *n* linhas. Essa opção será útil se você quiser testar um pipeline em um pequeno número de linhas e não precisar que os dados sejam balanceados ou amostrados de qualquer forma.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline na interface e conecte o conjunto de espaço.  

1. **Modo de partição ou de exemplo**: defina essa opção como **Head**.

1. **Número de linhas a serem selecionadas**: Insira o número de linhas a serem retornadas.

   O número de linhas deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de registros, o conjunto de um inteiro será retornado.

1. Envie o pipeline.

O módulo gera um único conjunto de registros que contém apenas o número especificado de linhas. As linhas são sempre lidas na parte superior do conjunto de registros.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Essa opção dá suporte à amostragem aleatória simples ou à amostragem aleatória de sobreratificação. É útil se você quiser criar um conjunto de um DataSet de exemplo menor para teste.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline e conecte o conjunto de espaço.

1. **Modo de partição ou de exemplo**: defina essa opção como  **amostragem**.

1. **Taxa de amostragem**: Insira um valor entre 0 e 1. Esse valor especifica a porcentagem de linhas do conjunto de registros de origem que devem ser incluídas no conjunto de resultados de saída.

   Por exemplo, se você quiser apenas metade do conjunto de conjuntos original, insira `0.5` para indicar que a taxa de amostragem deve ser de 50%.

   As linhas do conjunto de dados de entrada são embaralhadas e colocadas seletivamente no conjunto de dados de saída, de acordo com a razão especificada.

1. **Semente aleatória para amostragem**: opcionalmente, insira um inteiro para usar como um valor de semente.

   Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. O valor padrão é **0**, o que significa que uma semente inicial é gerada com base no relógio do sistema. Esse valor pode levar a resultados ligeiramente diferentes cada vez que você executar o pipeline.

1. **Divisão de sobreratificação para amostragem**: Selecione esta opção se for importante que as linhas no conjunto de registros sejam divididas uniformemente por alguma coluna de chave antes da amostragem.

   Para a **coluna de chave estratificação para amostragem**, selecione uma única *coluna Strata* para usar ao dividir o conjunto de os. As linhas no conjunto de registros são então divididas da seguinte maneira:

   1. Todas as linhas de entrada são agrupadas (desratificadas) pelos valores na coluna Strata especificada.

   1. As linhas são colocadas em ordem aleatória dentro de cada grupo.

   1. Cada grupo é seletivamente adicionado ao conjunto de dados de saída para atender à taxa especificada.


1. Envie o pipeline.

   Com essa opção, o módulo gera um único conjunto de dados que contém uma amostragem representativa dos dados. A parte restante, não amostrada do conjunto de resultados não é a saída. 

## <a name="split-data-into-partitions"></a>Dividir dados em partições

Use esta opção quando você quiser dividir o conjunto de dados em subconjuntos deles. Essa opção também é útil quando você deseja criar um número personalizado de dobras para validação cruzada ou para dividir linhas em vários grupos.

1. Adicione a **partição e** o módulo de exemplo ao seu pipeline e conecte o conjunto de espaço.

1. Para o **modo de partição ou de exemplo**, selecione **atribuir a dobras**.

1. **Usar substituição no particionamento**: Selecione esta opção se desejar que a linha de amostra seja colocada de volta no pool de linhas para reutilização em potencial. Como resultado, a mesma linha pode ser atribuída a várias dobras.

   Se você não usar a substituição (a opção padrão), a linha de amostra não será colocada de volta no pool de linhas para reutilização potencial. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

1. **Divisão aleatória**: Selecione esta opção se desejar que as linhas sejam atribuídas aleatoriamente a dobras.

   Se você não selecionar essa opção, as linhas serão atribuídas a dobras por meio do método Round Robin.

1. **Semente aleatória**: opcionalmente, insira um inteiro para usar como o valor de semente. Essa opção será importante se você quiser que as linhas sejam divididas da mesma maneira toda vez. Caso contrário, o valor padrão de **0** significa que uma semente de início aleatório será usada.

1. **Especifique o método do particionador**: indique como você deseja que os dados sejam distribuídos para cada partição, usando estas opções:

   - **Particionar uniformemente**: Use essa opção para inserir um número igual de linhas em cada partição. Para especificar o número de partições de saída, insira um número inteiro na caixa **especificar número de dobras a serem divididas uniformemente** .

   - **Partição com proporções personalizadas**: Use essa opção para especificar o tamanho de cada partição como uma lista separada por vírgulas.

     Por exemplo, suponha que você deseja criar três partições. A primeira partição conterá 50% dos dados. As duas partições restantes conterá 25 por cento dos dados. Na caixa **lista de proporções separadas por vírgula** , insira estes números: **0,5,. 25,. 25**.

     A soma de todos os tamanhos de partição deve somar exatamente 1.

     Se você inserir números que se somam a *menos de 1*, uma partição extra será criada para manter as linhas restantes. Por exemplo, se você inserir os valores **0,2** e **3**, uma terceira partição será criada para manter a porcentagem restante de 50% de todas as linhas.
     
     Se você inserir números que somam *mais de 1*, um erro será gerado quando você executar o pipeline.

1. **Divisão de desratificação**: Selecione esta opção se desejar que as linhas sejam desratificadas quando divididas e, em seguida, escolha a _coluna Strata_.

1. Envie o pipeline.

   Com essa opção, o módulo gera vários conjuntos de valores. Os conjuntos de valores são particionados de acordo com as regras que você especificou.

### <a name="use-data-from-a-predefined-partition"></a>Usar dados de uma partição predefinida  

Use esta opção quando você tiver dividido um conjunto de informações em várias partições e agora quiser carregar cada partição por vez para análise ou processamento posterior.

1. Adicione a **partição e** o módulo de exemplo ao pipeline.

1. Conecte o módulo à saída de uma instância anterior de **partição e exemplo**. Essa instância deve ter usado a opção **atribuir a dobras** para gerar um número de partições.

1. **Modo de partição ou de exemplo**: selecione Selecionar **dobra**.

1. **Especifique a qual dobra deve ser amostrada**: selecione uma partição a ser usada inserindo seu índice. Índices de partição são baseados em 1. Por exemplo, se você dividiu o conjunto de um em três partes, as partições teriam os índices 1, 2 e 3.

   Se você inserir um valor de índice inválido, um erro de tempo de design será gerado: "erro 0018: DataSet contém dados inválidos".

   Além de agrupar o conjunto de um por dobras, você pode separar o conjunto de um em dois grupos: uma dobra de destino e todo o resto. Para fazer isso, insira o índice de uma única dobra e selecione a opção **escolher complemento da dobra selecionada** para obter tudo, exceto os dados na dobra especificada.

1. Se você estiver trabalhando com várias partições, deverá adicionar mais instâncias da **partição e** do módulo de exemplo para lidar com cada partição.

   Por exemplo, a **partição e** o módulo de exemplo na segunda linha são definidos para **atribuir a dobras** e o módulo na terceira linha é definido para **selecionar dobra**.   

   ![Partição e exemplo](./media/module/partition-and-sample.png)

1. Envie o pipeline.

   Com essa opção, o módulo gera um único conjunto de registros que contém apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Você não pode exibir as designações de dobra diretamente. Eles estão presentes apenas nos metadados.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 