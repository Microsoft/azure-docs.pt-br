---
title: "Reconhecimento de entidade biomédica – Processo de ciência de dados de equipe – Azure Machine Learning | Microsoft Docs"
description: "Um guia de início rápido do projeto do Processo de Ciência de Dados de Equipe que usa aprendizado profundo para reconhecimento de entidade biomédica no Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: a13bbd5d32eaab96dfb97e60652dbe9bcbdfb1b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Reconhecimento de entidade biomédica usando o modelo TDSP (Processo de Ciência de Dados de Equipe)

O objetivo deste cenário do mundo real é destacar como usar o Azure Machine Learning Workbench para resolver uma tarefa NLP (Processamento de linguagem natural) complicada como a extração de entidades de textos não estruturados:

1. Como treinar um modelo de incorporações de palavra neural em um corpus de texto de cerca de 18 milhões de resumos da PubMed usando a [implementação Word2Vec do Spark](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Como criar um modelo de rede neural recorrente LSTM (Memória de longo e curto prazo) para a extração de entidades em uma GPU DS VM (Máquina Virtual de Ciência de Dados do Azure habilitada para GPU) no Azure.
2. Demonstre que o modelo de incorporações de palavras específicas do domínio pode ter um desempenho melhor que os modelos de incorporações de palavras genéricas na tarefa de reconhecimento de entidades. 
3. Demonstre como treinar e operacionalizar modelos de aprendizado profundo usando o Azure Machine Learning Workbench.

4. Demonstre os seguintes recursos dentro do Azure Machine Learning Workbench:

    * Criação de instâncias dos [modelos e da estrutura TDSP (Processo de Ciência de Dados de Equipe)](how-to-use-tdsp-in-azure-ml.md).
    * Execução de código em blocos de anotações do Jupyter, bem como scripts Python.
    * Acompanhamento do histórico de execuções para arquivos Python.
    * Execução de trabalhos no contexto de computação remoto do Spark usando clusters Spark 2.1 do HDInsight.
    * Execução de trabalhos em VMs GPU remotas no Azure.
    * Fácil operacionalização de modelos de aprendizado profundo como serviços Web nos Serviços de Contêiner do Azure.

## <a name="use-case-overview"></a>Visão geral de casos de uso
O reconhecimento de entidades nomeadas biomédicas é uma etapa essencial para tarefas NLP biomédicas complexas como: 
* Extração de doenças, sintomas de registros médicos ou de saúde eletrônicos.
* Descoberta de remédios
* Noções básicas sobre as interações entre os diferentes tipos de entidade como interações entre remédios, relação entre remédio e doença e relação entre genes e proteínas.

Nosso cenário de caso de uso se concentra em como uma grande quantidade de corpus de dados não estruturados como resumos da Medline PubMed podem ser analisados para treinar um modelo de incorporação de palavras. Em seguida, as incorporações de saída são consideradas recursos gerados automaticamente para treinar um extrator de entidades neurais.

Nossos resultados mostram que o treinamento do modelo de extração de entidades biomédicas sobre os recursos de incorporação de palavras específicas do domínio tem desempenho melhor do que o modelo treinado no tipo de recurso genérico. O modelo específico do domínio pode detectar 7012 entidades corretamente (de 9475) com pontuação F1 de 0,73 comparada às 5274 entidades com pontuação F1 de 0,61 para o modelo genérico.

A figura a seguir mostra a arquitetura que foi usada para processar dados e treinar modelos.

![Arquitetura](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Descrição dos dados

### <a name="1-word2vec-model-training-data"></a>1. Dados de treinamento de modelo Word2Vec
Baixamos primeiro os dados brutos de resumos da MEDLINE da [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Os dados estão publicamente disponíveis no formato de arquivos XML em seus [servidor FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Há 892 arquivos XML disponíveis no servidor e cada um desses arquivos tem informações de 30.000 artigos. São fornecidos mais detalhes sobre a etapa de coleta de dados na seção Estrutura do projeto. Os campos apresentados em cada arquivo são 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Dados de treinamento de modelo LSTM

O modelo de extração de entidades neurais foi treinado e avaliado em conjuntos de dados disponíveis publicamente. Para obter uma descrição detalhada sobre esses conjuntos de dados, consulte as seguintes fontes:
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) (Tarefa de reconhecimento de bioentidade na BioNLP/NLPBA 2004)
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/) (Corpus de tarefas Bio Creative V CDR)
 * [Semeval 2013 – Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/) (Semeval 2013 – Tarefa 9.1 (Reconhecimento de remédios))

## <a name="link-to-the-azure-gallery-github-repository"></a>Link para o repositório do GitHub da galeria do Azure
A seguir está o link para o repositório público do GitHub do cenário do mundo real que contém o código e uma descrição mais detalhada: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Pré-requisitos 

* Uma [assinatura](https://azure.microsoft.com/free/) do Azure
* Azure Machine Learning Workbench. Consulte o [guia de instalação](quickstart-installation.md). Atualmente, o Azure Machine Learning Workbench pode ser instalado com os seguintes sistemas operacionais: 
    * Windows 10 ou Windows Server 2016
    * macOS Sierra (ou mais recente)


### <a name="azure-services"></a>Serviços do Azure
* [Cluster Spark do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) versão Spark 2.1 no Linux (HDI 3.6) para computação de expansão. Para processar a quantidade total dos resumos da MEDLINE discutidos abaixo, é necessária a configuração mínima de: 
    * Nó de cabeçalho: tamanho [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/)
    * Nós de trabalho: pelo menos 4 do [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). Em nosso trabalho, usamos 11 nós de trabalho do tamanho D12_V2.
* [DSVM (Máquina Virtual de Ciência de Dados) NC6](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) para computação de expansão.

### <a name="python-packages"></a>Pacotes Python

Todas as dependências necessárias são definidas no arquivo aml_config/conda_dependencies.yml na pasta de projeto do cenário. As dependências definidas neste arquivo serão provisionadas automaticamente para execuções com relação ao Docker, VM e destinos de cluster HDI. Para obter detalhes sobre o formato do arquivo de ambiente Conda, consulte [aqui](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Instruções básicas para o AML (Azure Machine Learning) workbench
* [Visão geral](overview-what-is-azure-ml.md)
* [Instalação](quickstart-installation.md)
* [Usando o TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Como ler e escrever arquivos](how-to-read-write-files.md)
* [Como usar blocos de anotações do Jupyter](how-to-use-jupyter-notebooks.md)
* [Como usar a GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Estrutura do cenário
Para o cenário, usamos os modelos de documentação e a estrutura de projeto TDSP (Figura 1), que segue o [ciclo de vida TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). O projeto é criado com base nas instruções fornecidas [aqui](./how-to-use-tdsp-in-azure-ml.md).


![Preencher as informações do projeto](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

O fluxo de trabalho de ciência de dados passo a passo é o seguinte:

### <a name="1-data-acquisition-and-understanding"></a>1. Compreensão e aquisição de dados

Consulte [Data Acquisition and Understanding](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/Code/01_Data_Acquisition_and_Understanding/ReadMe.md) (Aquisição de dados e reconhecimento).

O corpus bruto da MEDLINE tem um total de 27 milhões de resumos em que cerca de 10 milhões de artigos têm um campo de resumo vazio. O Azure HDInsight Spark é usado para processar Big Data que não pode ser carregado na memória de um único computador como um [DataFrame Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Primeiro, os dados são baixados para o cluster Spark. Em seguida, as etapas a seguir são executadas no [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* analisar os arquivos XML usando o analisador XML da Medline
* pré-processar o texto do resumo incluindo divisão de frase, geração de tokens e normalização de maiúsculas.
* excluir artigos em que o campo do resumo está vazio ou contém texto curto 
* criar o vocabulário de palavras de resumos de treinamento
* treinar o modelo neural de incorporação de palavras. Para obter mais detalhes, consulte o [link do código do GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/Code/01_DataPreparation/ReadMe.md) para começar.


Após analisar os arquivos XML, os dados têm o seguinte formato: 

![Exemplo de dados](./media/scenario-tdsp-biomedical-recognition/datasample.png)

O modelo de extração de entidades neurais foi treinado e avaliado em conjuntos de dados disponíveis publicamente. Para obter uma descrição detalhada sobre esses conjuntos de dados, consulte as seguintes fontes:
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) (Tarefa de reconhecimento de bioentidade na BioNLP/NLPBA 2004)
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/) (Corpus de tarefas Bio Creative V CDR)
 * [Semeval 2013 – Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/) (Semeval 2013 – Tarefa 9.1 (Reconhecimento de remédios))

### <a name="2-modeling"></a>2. Modelagem

Consulte [Modeling](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling) (Modelagem).

A modelagem é o estágio em que mostramos como é possível usar os dados baixados na seção anterior para treinar seu próprio modelo de incorporação de palavras e usá-lo para outras tarefas downstream. Embora estejamos usando os dados da PubMed, o pipeline para gerar as incorporações é genérico e pode ser reutilizado para treinar incorporações de palavras para qualquer outro domínio. Para que as incorporações sejam uma representação exata dos dados, é essencial que o word2vec seja treinado em uma grande quantidade de dados.
Depois as incorporações de palavras estiverem prontas, será possível treinar um modelo de rede neural profunda que usa as incorporações aprendidas para inicializar a camada Incorporação. Marcamos a camada de incorporação como não treinável, mas isso não é obrigatório. O treinamento do modelo de incorporação de palavras não é supervisionado e, portanto, é possível aproveitar os textos sem rótulo. No entanto, o treinamento do modelo de reconhecimento de entidades é uma tarefa de aprendizado supervisionada e sua precisão depende da quantidade e da qualidade de dados anotados manualmente. 


#### <a name="21-feature-generation"></a>2.1. Geração de recursos

Consulte [Feature generation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/01_FeatureEngineering) (Geração de recursos).

O Word2Vec é o algoritmo de aprendizado de incorporação de palavras que treina um modelo de rede neural em um corpus de treinamento sem rótulo. Ele produz um vetor contínuo para cada palavra no corpus que representa suas informações semânticas. Esses modelos são redes neurais simples com uma camada oculta. Os vetores/incorporações de palavras são aprendidos por propagação reversa e por espaço descendente de gradiente alheatório. Há dois tipos de modelos word2vec, ou seja, Skip-Gram e a bolsa de palavras contínua (confira [aqui](https://arxiv.org/pdf/1301.3781.pdf) para obter mais detalhes). Como estamos usando a implementação do MLlib do word2vec, que dá suporte ao modelo Skip-gram, o descrevemos brevemente aqui (imagem extraída do [link](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Modelo Skip Gram](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

O modelo usa amostragem Softmax hierárquico e negativa para otimizar o desempenho. O H-SoftMax (SoftMax hierárquico) é uma aproximação inspirada por árvores binárias. O H-SoftMax substitui essencialmente a camada SoftMax simples por uma camada hierárquica que tem as palavras como folhas. Isso nos permite decompor o cálculo da probabilidade de uma palavra em uma sequência de cálculos de probabilidade, o que nos poupa de precisar calcular a normalização cara em todas as palavras. Como uma árvore binária balanceada tem uma profundidade de log2(|V|) (V é o vocabulário), só é necessário avaliar no máximo log2(|V|) nós para obter a probabilidade final de uma palavra. A probabilidade de uma palavra w dado seu contexto c é, então, simplesmente o produto das probabilidades das viradas à direita e à esquerda, respectivamente, que levam ao seu nó folha. É possível criar uma árvore de Huffmann com base na frequência das palavras no conjunto de dados para garantir que palavras mais frequentes obtenham representações mais curtas. Para obter mais informações, consulte [este link](http://sebastianruder.com/word-embeddings-softmax/).
Imagem extraída [daqui](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualização

Quando tivermos as incorporações, gostaríamos de visualizá-las e ver a relação entre palavras semanticamente semelhantes. 

![Similaridade W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Mostramos duas maneiras diferentes de visualizar as incorporações. A primeira delas usa um PCA para projetar o vetor altamente dimensional a um espaço de vetor 2D. Isso leva a uma perda significativa de informações e a visualização não é tão precisa. A segunda é usar o PCA com [t-SNE](https://distill.pub/2016/misread-tsne/). O t-SNE é uma técnica de redução de dimensionalidade não linear bem adequada para inserir dados altamente dimensionais em um espaço de duas ou três dimensões, que pode, então, ser visualizado em um gráfico de dispersão.  Ele modela cada objeto altamente dimensional por um ponto de duas ou três dimensões de maneira que objetos semelhantes sejam modelados por pontos próximos e objetos diferentes sejam modelados por pontos distantes. Ele funciona em duas partes. Primeiro, ele cria uma distribuição de probabilidade nos pares no maior espaço dimensional de maneira que objetos semelhantes tenham uma alta probabilidade de serem escolhidos e objetos diferentes tenham uma baixa probabilidade de serem escolhidos. Em segundo lugar, ele define uma distribuição de probabilidade nos pontos em um mapa baixo dimensional e minimiza a Divergência KL entre as duas distribuições com relação à localização de pontos no mapa. A localização dos pontos na baixa dimensão é obtida minimizando a Divergência KL usando o Espaço descendente do gradiente. Mas talvez o t-SNE nem sempre seja confiável. Os detalhes da implementação podem ser encontrados [aqui](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/01_FeatureEngineering). 


Conforme mostrado na figura a seguir, a visualização do t-SNE fornece mais separação dos padrões de vetores de palavras e de clustering potencial. 


* Visualização com PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Visualização com t-SNE

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Pontos mais próximos de "Câncer" (são todos os subtipos de câncer)

![Pontos mais próximos de câncer](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Treinar o extrator de entidade neural

Consulte [Train the neural entity extractor](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/02_ModelCreation/ReadMe.md) (Treinar o extrator de entidade neural).

A arquitetura de rede neural de encaminhamento do feed tem um problema que eles tratam cada entrada e saída como independentes das outras entradas e saídas. Essa arquitetura não pode modelar tarefas de rotulação sequência a sequência como tradução automática e extração de entidades. Modelos de redes neurais recorrentes não têm esse problema, pois eles podem passar informações computadas até novas para o próximo nó. Essa propriedade é chamada de ter memória na rede, pois ela é capaz de usar as informações anteriormente computadas, conforme mostrado na figura a seguir:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Os RNNs baunilha realmente apresentam o [Problema de Desaparecimento de Gradiente](https://en.wikipedia.org/wiki/Vanishing_gradient_problem), pois eles não são capazes de utilizar todas as informações que eles viram anteriormente. O problema fica evidente somente quando uma grande quantidade de contexto é necessária para fazer uma previsão. Mas modelos como LSTM não apresentam esse problema; na verdade, eles são criados para lembrar dependências de longo prazo. Diferentemente dos RNNs baunilha que têm uma única rede neural, os LSTMs têm as interações entre quatro redes neurais para cada célula. Para obter uma explicação detalhada sobre como os LSTMs funcionam, consulte [esta postagem](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![Célula LSTM](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Vamos tentar reunir nossa própria rede neural recorrente baseada em LSTM e tentar extrair tipos de entidade como menções a remédio, doença e sintomas dos dados da PubMed. A primeira etapa é obter uma grande quantidade de dados rotulados e, como adivinhou, isso não é fácil! A maioria dos dados médicos contém muitas informações confidenciais sobre a pessoa e, portanto, não está disponível publicamente. Dependemos de uma combinação de dois conjuntos de dados diferentes publicamente disponíveis. O primeiro conjunto de dados é da Semeval 2013 – Tarefa 9.1 (Reconhecimento de remédio) e a outra é da tarefa BioCreative V CDR. Estamos combinando e rotulando automaticamente esses dois conjuntos de dados para podermos detectar remédios e doenças de textos médicos e avaliar nossas incorporações de palavras. Para obter detalhes da implementação, consulte o [link do código do GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/02_Modeling/02_ModelCreation).

A arquitetura de modelo que foi usada em todos os códigos e para comparação é apresentada a seguir. O parâmetro alterado para diferentes conjuntos de dados é o comprimento máximo da sequência (613 neste caso).

![Modelo LSTM](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Avaliação do modelo
Usamos o script de avaliação da tarefa compartilhada [Bio-Entity Recognition Task at Bio NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) (Tarefa de reconhecimento de bioentidade na Bio NLP/NLPBA 2004) para avaliar a precisão, recuperação e pontuação F1 do modelo. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Modelos de incorporação de palavras genéricos versus no domínio

A seguir, há uma comparação entre a precisão de dois tipos de recurso: (1) incorporações de palavras treinadas em resumos da PubMed e (2) incorporações de palavras treinadas no Google Notícias. Vemos claramente que o modelo no domínio tem desempenho melhor do que o modelo genérico. Portanto, ter um modelo de incorporação de palavras específico em vez de usar um genérico é muito mais útil. 

* Tarefa nº 1: Detecção de remédios e de doenças

![Comparação de modelos 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Realizamos a avaliação das incorporações de palavras em outros conjuntos de dados de maneira semelhante e vimos que o modelo no domínio é sempre melhor.

* Tarefa nº 2: Detecção de proteínas, de linha de célula, de tipo de célula, de DNA e de RNA

![Comparação de modelos 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Tarefa nº 3: Detecção de substâncias químicas e de doenças

![Comparação de modelos 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Tarefa nº 4: Detecção de remédios

![Comparação de modelos 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Tarefa nº 5: Detecção de genes

![Comparação de modelos 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow versus CNTK
Todos os modelos reportados são treinados usando o Keras com o TensorFlow como back-end. O Keras com back-end CNTK não dá suporte ao "inverso" no momento em que esse trabalho foi realizado. Portanto, para fins de comparação, treinamos um modelo LSTM unidirecional com o back-end CNTK treinado e o comparamos com um modelo LSTM unidirecional com o back-end TensorFlow. Instale o CNTK 2.0 para Keras [aqui](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Comparação de modelos 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Concluímos que o CNTK tem desempenho tão bom quanto o Tensorflow nos termos do tempo de treinamento realizado por época (60s para CNTK e 75s para Tensorflow) e do número de entidades de teste detectadas. Estamos usando as camadas unidirecionais para avaliação.


### <a name="3-deployment"></a>3. Implantação

Consulte [Implantação](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/Code/03_Deployment).

Implantamos um serviço Web em um cluster no [ACS (Serviço de Contêiner do Azure)](https://azure.microsoft.com/services/container-service/). O ambiente de operacionalização provisiona o Docker e o Kubernetes no cluster para gerenciar a implantação do serviço Web. É possível encontrar mais informações sobre o processo de operacionalização [aqui](model-management-service-deploy.md ).


## <a name="conclusion"></a>Conclusão

Fomos além dos detalhes de como você poderia treinar um modelo de incorporação de palavras usando o algoritmo Word2Vec no Spark e usar as incorporações extraídas como recursos para treinar uma rede neural profunda para extração de entidades. Aplicamos o pipeline de treinamento no domínio biomédico. No entanto, o pipeline é genérico o suficiente para ser aplicado para detectar tipos de entidade personalizada de qualquer outro domínio. Só é necessário ter dados suficientes e é possível adaptar-se facilmente ao fluxo de trabalho apresentado aqui para um domínio diferente.

## <a name="references"></a>Referências

* Tomas Mikolov, Kai Chen, Greg Corrado e Jeffrey Dean. 2013a. Estimativa eficiente de representações de palavras no espaço de vetor. Em Trabalhos de ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado e Jeff Dean. 2013b. Representações distribuídas de palavras e frases e sua composição. Em Trabalhos de NIPS páginas 3111–3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen e Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf) (Como treinar boas incorporações de palavras para NLP biomédico), Em Trabalhos do 15º Workshop sobre Processamento de Linguagem Natural Biomédica, páginas 166–174.
* [Representações de vetor de palavras](https://www.tensorflow.org/tutorials/word2vec)
* [Redes neurais recorrentes](https://www.tensorflow.org/tutorials/recurrent)
* [Problemas encontrados com o Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: lições aprendidas](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

