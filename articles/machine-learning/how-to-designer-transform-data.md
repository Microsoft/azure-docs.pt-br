---
title: Transformar dados no designer
titleSuffix: Azure Machine Learning
description: Saiba como importar e transformar dados no designer de Azure Machine Learning para criar seus próprios conjuntos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 70f5e17c4cc42201e9aa3d36c9937f6ceb9527d0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880196"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformar dados no designer de Azure Machine Learning


Neste artigo, você aprenderá a transformar e salvar conjuntos de dados no designer de Azure Machine Learning para que possa preparar seus próprios dados para o machine learning.

Você usará o conjunto de dados de exemplo de [Classificação Binária de Renda de Censo de Adulto](./samples-designer.md) para preparar dois conjuntos de dados: um que inclui informações de censo somente de adultos residentes nos Estados Unidos e outro com informações de censo de adultos não residentes nos EUA.

Neste artigo, você aprenderá como:

1. Transforme um conjunto de dados a fim de prepará-lo para treinamento.
1. Exporte os conjuntos de dados resultantes para um armazenamento de dados.
1. Veja os resultados.

Estas instruções são um pré-requisito para o artigo [Como treinar novamente os modelos de designer](how-to-retrain-designer.md). Nesse artigo, você aprenderá a usar os conjuntos de dados transformados para treinar vários modelos com parâmetros de pipeline.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformar um conjunto de dados

Nesta seção, você aprenderá a importar o conjunto de dados de exemplo e a dividir os dados em conjuntos de dados para residentes dos EUA e para residentes de outros países. Para obter mais informações sobre como importar seus próprios dados para o designer, confira [como importar dados](how-to-designer-import-data.md).

### <a name="import-data"></a>Importar dados

Use as etapas a seguir para importar o conjunto de dados de exemplo.

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Acesse o designer. Selecione **Módulos de precompilação fáceis de usar** para criar um pipeline.

1. Selecione um destino de computação padrão para executar o pipeline.

1. À esquerda da tela do pipeline há uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de dados**. Em seguida, veja a seção **Exemplos**.

1. Arraste e solte o conjunto de dados **Classificação Binária de Renda de Censo de Adulto** na tela.

1. Clique com o botão direito do mouse no módulo conjunto de **censo de renda de adulto** e selecione **Visualizar**  >  **saída do conjunto de resultados**

1. Use a janela de visualização de dados para explorar o conjunto de dados. Tome nota especial dos valores de coluna "native-country".

### <a name="split-the-data"></a>Dividir os dados

Nesta seção, você usa o módulo [Dividir módulo de dados](algorithm-module-reference/split-data.md) para identificar e dividir as linhas que contêm "United-States" na coluna "native-country". 

1. Na paleta de módulos à esquerda da tela, expanda a seção **Transformação de Dados** e localize o módulo **Dividir Dados**.

1. Arraste o módulo **Dividir Dados** para a tela e solte o módulo abaixo do módulo conjunto de dados.

1. Conecte o módulo conjunto de dados ao módulo **Dividir Dados**.

1. Selecione o módulo **Dividir dados**.

1. No painel detalhes do módulo à direita da tela, defina **Modo de divisão** como **Expressão Regular**.

1. Insira a **Expressão Regular**: `\"native-country" United-States`.

    O modo de **Expressão Regular** testa uma coluna para um valor. Para obter mais informações sobre o módulo Dividir Dados, confira a [página de referência do módulo de algoritmo](algorithm-module-reference/split-data.md) relacionado.

Seu pipeline deve ter esta aparência:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="Captura de tela mostrando como configurar o pipeline e o módulo Dividir Dados":::


## <a name="save-the-datasets"></a>Salvar os conjuntos de dados

Agora que seu pipeline está configurado para dividir os dados, você precisa especificar onde deseja manter os conjuntos de dados. Para este exemplo, use o módulo **Exportar Dados** para salvar o conjunto de dados em um armazenamento de dados. Para obter mais informações sobre armazenamentos de dados, confira [Conectar aos serviços do Armazenamento do Azure](how-to-access-data.md)

1. Na paleta de módulos à esquerda da tela, expanda a seção **Entrada e Saída de Dados** e localize o módulo **Exportar Dados**.

1. Arraste e solte dois módulos **Exportar Dados** abaixo do módulo **Dividir Dados**.

1. Conecte cada porta de saída do módulo **Dividir Dados** a outro módulo **Exportar Dados**.

    Seu pipeline deve ser semelhante ao seguinte:

    ![Captura de tela mostrando como conectar os módulos Exportar Dados](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selecione o módulo **Exportar Dados** que está conectado à porta mais à *esquerda* do módulo **Dividir Dados**.

    A ordem das portas de saída é importante para o módulo **Dividir Dados**. A primeira porta de saída contém as linhas em que a expressão regular é verdadeira. Nesse caso, a primeira porta contém linhas para renda de residentes nos EUA e a segunda porta contém linhas para renda de não residentes nos EUA.

1. No painel de detalhes do módulo à direita da tela, defina as seguintes opções:
    
    **Tipo de armazenamento de dados**: Armazenamento do Blobs do Azure

    **Armazenamento de dados**: Selecione um armazenamento de dados existente ou selecione "Novo armazenamento de dados" para criar um agora.

    **Caminho**: `/data/us-income`

    **Formato de arquivo**: csv

    > [!NOTE]
    > Este artigo pressupõe que você tenha acesso a um armazenamento de dados registrado para o workspace atual do Azure Machine Learning. Para obter instruções sobre como configurar um armazenamento de dados, confira [Conectar aos serviços do Armazenamento do Azure](how-to-connect-data-ui.md#create-datastores).

    Se você não tiver um armazenamento de dados, poderá criar um agora. Para fins de exemplo, este artigo salvará os conjuntos de dados na conta de armazenamento de blobs padrão associada ao workspace. Ele salvará os conjuntos de itens no contêiner `azureml` em uma nova pasta chamada `data`.

1.  Selecione o módulo **Exportar Dados** que está conectado à porta mais à *direita* do módulo **Dividir Dados**.

1. No painel de detalhes do módulo à direita da tela, defina as seguintes opções:
    
    **Tipo de armazenamento de dados**: Armazenamento do Blobs do Azure

    **Armazenamento de dados**: Selecione o mesmo armazenamento de dados que foi selecionado acima

    **Caminho**: `/data/non-us-income`

    **Formato de arquivo**: csv

1. Confirme que o módulo **Exportar Dados** conectado à porta à esquerda do módulo **Dividir Dados** tem o **Caminho** `/data/us-income`.

1. Confirme se o módulo **Exportar Dados** conectado à porta à direita tem o **Caminho** `/data/non-us-income`.

    Seu pipeline e configurações devem ter esta aparência:
    
    ![Captura de tela mostrando como configurar os módulos Exportar Dados](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Enviar a execução

Agora que o pipeline está configurado para dividir e exportar os dados, envie uma execução de pipeline.

1. Na parte superior da tela, selecione **Enviar**.

1. Na caixa de diálogo **Configurar execução de pipeline**, selecione **Criar** para criar um experimento.

    Os experimentos agrupam logicamente execuções de pipeline relacionadas. Se você executar esse pipeline no futuro, deverá usar o mesmo experimento para fins de acompanhamento e registro em log.

1. Forneça um nome de teste descritivo, como "dados-de-censo-divididos".

1. Selecione **Enviar**.

## <a name="view-results"></a>Exibir os resultados

Depois que o pipeline for concluído, você poderá exibir os resultados navegando até o armazenamento de blobs no portal do Azure. Você também pode exibir os resultados intermediários do módulo **Dividir Dados** para confirmar que seus dados foram divididos corretamente.

1. Selecione o módulo **Dividir dados**.

1. No painel de detalhes do módulo à direita da tela, selecione **Saídas + logs**. 

1. Selecione o ícone visualizar ![ícone visualizar](media/how-to-designer-transform-data/visualize-icon.png) ao lado de **Conjunto de dados 1 de resultados**. 

1. Verifique se a coluna "native-country" contém apenas o valor "United-States".

1. Selecione o ícone visualizar ![ícone visualizar](media/how-to-designer-transform-data/visualize-icon.png) ao lado de **Conjunto de dados 2 de resultados**. 

1. Confirme que a coluna "native-country" não contém o valor "United-States".

## <a name="clean-up-resources"></a>Limpar os recursos

Ignore esta seção se quiser continuar com a parte 2 destas instruções, [Treinar modelos novamente com o designer do Azure Machine Learning](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como transformar um conjunto de dados e salvá-lo em um armazenamento de dados registrado.

Prossiga para a próxima parte desta série de instruções com [Treinar modelos novamente com o designer do Azure Machine Learning](how-to-retrain-designer.md) para usar seus conjuntos de dados transformados e parâmetros de pipeline para treinar modelos de machine learning.