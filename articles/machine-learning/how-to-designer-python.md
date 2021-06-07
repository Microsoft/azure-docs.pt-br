---
title: Executar script Python no designer
titleSuffix: Azure Machine Learning
description: Saiba como usar o modelo executar script Python no Azure Machine Learning designer para executar operações personalizadas escritas em Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556226"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Executar código Python no designer de Azure Machine Learning

Neste artigo, você aprenderá a usar o módulo [Executar Script do Python](algorithm-module-reference/execute-python-script.md) para adicionar lógica personalizada ao designer do Azure Machine Learning. Nas instruções a seguir, você usa a biblioteca Pandas para fazer engenharia de recursos simples.

Você pode usar o editor de código interno para adicionar rapidamente uma lógica simples do Python. Se quiser adicionar um código mais complexo ou carregar bibliotecas adicionais do Python, use o método de arquivo zip.

O ambiente de execução padrão usa a distribuição Anaconda do Python. Para receber uma lista completa dos pacotes pré-instalados, consulte a página [referência do módulo Executar Script do Python](algorithm-module-reference/execute-python-script.md).

![Executar mapa de entrada do Python](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Executar Python escrito no designer

### <a name="add-the-execute-python-script-module"></a>Adicionar o módulo Executar Script do Python

1. Localize o módulo **Executar Script do Python** na paleta do designer. Ele pode ser encontrado na seção **Linguagem do Python**.

1. Arraste e solte o módulo na painel da tela do pipeline.

### <a name="connect-input-datasets"></a>Conectar conjuntos de dados de entrada

Este artigo usa o exemplo de conjunto de dados **Dados de preços de automóveis (brutos)** . 

1. Arraste e solte o conjunto de dados na painel da tela do pipeline.

1. Conecte a porta de saída do conjunto de dados à porta de entrada superior esquerda do módulo **Executar Script do Python**. O designer expõe a entrada como um parâmetro para o script de ponto de entrada.
    
    A porta de entrada à direita é reservada para bibliotecas do Python compactadas.

    ![Conectar conjuntos de dados](media/how-to-designer-python/connect-dataset.png)
        

1. Anote a porta de entrada que você usa. O designer atribui a porta de entrada à esquerda para a variável `dataset1` e a porta de entrada intermediária para `dataset2`. 

Os módulos de entrada são opcionais, já que você pode gerar ou importar dados diretamente no módulo **Executar Script do Python**.

### <a name="write-your-python-code"></a>Escrever seu código Python

O designer oferece um script inicial que serve de ponto de partida para você editar e inserir seu próprio código Python. 

Neste exemplo, você usa o Pandas para combinar duas colunas encontradas no conjunto de dados de automóvel, **Preço** e **Cavalos-vapor**, para criar uma nova coluna, **Dólares por cavalo-vapor**. Esta coluna representa quanto você paga por cada cavalo-vapor, o que pode ser um recurso útil para decidir se um carro tem um bom custo-benefício. 

1. Selecione o módulo **Executar Script do Python**.

1. No painel que aparece à direita do painel da tela, selecione a caixa de texto **Script Python**.

1. Copie e cole o código a seguir na caixa de texto.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Seu pipeline deverá ter a seguinte imagem:
    
    ![Executar o pipeline do Python](media/how-to-designer-python/execute-python-pipeline.png)

    O script de ponto de entrada deve conter a função `azureml_main`. Há dois parâmetros de função que são mapeados para as duas portas de entrada do módulo **Executar Script do Python**.

    O valor retornado deve ser um Dataframe do Pandas. Você pode retornar até dois Dataframes como saída de módulo.
    
1. Envie o pipeline.

Agora, você tem um conjunto de dados com o novo recurso **Dólares/CV**, o que pode ser útil para o treinamento de um recomendador de carros. Este é um exemplo de extração de recursos e redução de dimensionalidade. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [importar seus próprios dados](how-to-designer-import-data.md) no designer do Azure Machine Learning.
