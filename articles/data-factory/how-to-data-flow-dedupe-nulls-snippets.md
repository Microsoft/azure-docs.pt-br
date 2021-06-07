---
title: Eliminação de duplicação de linhas e localização de nulos usando trechos de fluxo de dados
description: Saiba como facilmente a eliminação de duplicação de linhas e a localização de nulos usando trechos de código em fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 1b49b2584c4cb462c7c0f520fe8d1b5bf69c8674
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393659"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>Eliminação de duplicação de linhas e localização de nulos usando trechos de fluxo de dados

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usando trechos de código no mapeamento de fluxos de dados, você pode facilmente executar tarefas comuns, como eliminação de duplicação de dados e filtragem nula. Este artigo explica como adicionar facilmente essas funções a seus pipelines usando trechos de script de fluxo de dados.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **novo pipeline**.

1. Adicione uma atividade de fluxo de dados.

1. Selecione a guia **configurações de origem** , adicione uma transformação origem e, em seguida, conecte-a a um de seus conjuntos de valores.

    ![Captura de tela do painel "configurações de origem" para adicionar um tipo de origem.](media/data-flow/snippet-adf-2.png)

    Os trechos de verificação de eliminação de duplicação e nulo usam padrões genéricos que aproveitam a descompasso do esquema de fluxo de dados. Os trechos de código funcionam com qualquer esquema do seu conjunto de linhas ou com conjuntos de valores que não têm nenhum esquema predefinido.

1. Na seção "linhas distintas usando todas as colunas" do [script de fluxo de dados (DFS)](./data-flow-script.md#distinct-row-using-all-columns), copie o trecho de código para DistinctRows.

1. [Vá para a página de documentação do script de fluxo de dados e copie o trecho de código para linhas distintas.](./data-flow-script.md#distinct-row-using-all-columns)

    ![Captura de tela de um trecho de código-fonte.](media/data-flow/snippet-adf-3.png)

1. Em seu script, após a definição para `source1` , pressione Enter e cole o trecho de código.

1. Siga um destes procedimentos:

   * Conecte esse trecho de código colado à transformação origem que você criou anteriormente no grafo digitando **origem1** na frente do código colado.

   * Como alternativa, você pode conectar a nova transformação no designer selecionando o fluxo de entrada do novo nó de transformação no grafo.

     ![Captura de tela do painel "configurações de divisão condicional".](media/data-flow/snippet-adf-4.png)

   Agora, seu fluxo de dados removerá linhas duplicadas da origem usando a transformação Agregação, que agrupa por todas as linhas usando um hash geral em todos os valores de coluna.
    
1. Adicione um trecho de código para dividir os dados em um fluxo que contém linhas com valores nulos e outro fluxo sem valores nulos. Para fazer isso:

1. [Volte para a biblioteca de trechos e, desta vez, copie o código para as verificações nulas.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. No designer de fluxo de dados, selecione **script** novamente e cole esse novo código de transformação na parte inferior. Essa ação conecta o script à transformação anterior colocando o nome dessa transformação na frente do trecho colado.

   Seu grafo de fluxo de dados agora deve ser semelhante a este:

    ![Captura de tela do grafo de fluxo de dados.](media/data-flow/snippet-adf-1.png)

  Agora você criou um fluxo de dados de trabalho com deduping genéricas e verificações nulas usando trechos de código existentes da biblioteca de script de fluxo de dados e adicionando-os ao seu design existente.

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.