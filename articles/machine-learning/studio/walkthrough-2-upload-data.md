---
title: 'Etapa 2: carregar dados em um experimento do Machine Learning | Microsoft Docs'
description: "Etapa 2 - desenvolver um passo a passo de solução de previsão: carregamento armazenado de dados públicos no Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 469c94f6115f99bc4cf067e9c8f0e55c64990358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Etapa 2 do passo a passo: carregar dados existentes no experimento de Azure Machine Learning
Esta é a segunda etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Criar um espaço de trabalho do Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Carregar dados existentes**
3. [Criar um novo teste](walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](walkthrough-6-access-web-service.md)

- - -
Para desenvolver um modelo preditivo para risco de crédito, precisamos de dados que podemos usar para treinar e testar o modelo. Para este passo a passo, usaremos o “Conjunto de Dados Statlog (Dados de Crédito Alemão) UCI” do repositório UC Irvine Machine Learning. Você pode encontrá-lo aqui:   
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos o arquivo chamado **german.data**. Baixe esse arquivo em sua unidade de disco rígido local.  

O conjunto de dados **german.data** contém linhas de 20 variáveis para 1000 candidatos antigos de crédito. Essas 20 variáveis representam o conjunto de recursos do conjunto de dados (o *vetor de recurso*), que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do candidato, com 700 candidatos identificados como um risco de crédito baixo e 300 como um alto risco.

O website do UCI fornece uma descrição dos atributos do vetor de recurso para esses dados. Isso inclui informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, foi dada uma classificação binária indicando se são um risco baixo ou alto de crédito. 

Usaremos estes dados para treinar um modelo analítico preditivo. Quando tivermos concluído, nosso modelo deverá ser capaz de aceitar um vetor de recurso para uma nova pessoa e prever se ela é um risco de crédito alto ou baixo.  

Aqui está uma mudança interessante. A descrição do conjunto de dados no site do UCI menciona quanto custa se classificarmos incorretamente o risco de crédito de uma pessoa.
Se o modelo previr um alto risco de crédito para alguém que, de fato, é de baixo risco de crédito, o modelo terá feito uma classificação incorreta.
Porém, a classificação incorreta inversa é cinco vezes mais onerosa para a instituição financeira: se o modelo previr um baixo risco de crédito para alguém que, de fato, é de alto risco de crédito.

Dessa forma, queremos treinar nosso modelo para que o custo desse último tipo de classificação incorreta seja cinco vezes mais alto do que o outro tipo de classificação incorreta.
Uma maneira simples de fazer isso ao treinar o modelo em nossa experiência é duplicando (cinco vezes) essas entradas que representam alguém com um alto risco de crédito. Assim, se o modelo classificar incorretamente alguém como de baixo risco de crédito, quando ele for de fato de risco alto, o modelo fará a mesma classificação incorreta cinco vezes, uma para cada duplicação. Isso aumentará o custo deste erro nos resultados de treinamento.


## <a name="convert-the-dataset-format"></a>Converter o formato do conjunto de dados
O conjunto de dados original usa um formato separado por espaço em branco. O Machine Learning Studio trabalha melhor com um arquivo CSV (de valores separados por vírgula). Então, converteremos o conjunto de dados substituindo espaços por vírgulas.  

Há muitas maneiras de converter esses dados. Uma maneira é usar o seguinte comando do Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra maneira é usar o comando Unix sed:  

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, criamos uma versão separada por vírgulas dos dados em um arquivo chamado **german.csv** que usaremos em nosso teste.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Carregar o conjunto de dados para o Machine Learning Studio
Depois que os dados tiverem sido convertidos no formato CSV, precisaremos carregá-los no Machine Learning Studio. 

1. Abra a home page do Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Clique em ![Menu][1] no canto superior esquerdo da janela, clique em **Azure Machine Learning**, selecione **Estúdio** e entre.

3. Clique em **+NOVO** na parte inferior da janela.

4. Selecione **CONJUNTO DE DADOS**.

5. Selecione **DO ARQUIVO LOCAL**.

    ![Adicionar um conjunto de dados de um arquivo local][2]

6. No diálogo **Carregar um novo conjunto de dados**, clique em **Pesquisar** e localize o arquivo **german.csv** criado.

7. Insira um nome para o conjunto de dados. Para este passo a passo, vamos chamá-lo de "Dados do cartão de crédito alemão UCI".

8. Para tipo de dados, selecione **Arquivo CSV genérico sem cabeçalho (.nh.csv)**.

9. Inclua uma descrição se desejar.

10. Clique na marca de seleção **OK**.  

    ![Carregar o conjunto de dados][3]

Isso carrega os dados em um módulo de conjunto de dados que podemos usar em um experimento.

É possível gerenciar conjuntos de dados que você carregou no Estúdio clicando na guia **CONJUNTOS DE DADOS** à esquerda da janela do Estúdio.

![Gerenciar conjuntos de dados][4]

Para obter mais informações sobre como importar outros tipos de dados para um teste, consulte [Importar dados de treinamento para o Azure Machine Learning Studio](import-data.md).

**A seguir: [criar um novo experimento](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
