---
title: "Análise da coleção de documentos – Azure | Microsoft Docs"
description: "Como resumir e analisar um grande conjunto de documentos, incluindo técnicas como aprendizado de frase, modelagem de tópico e análise de modelos de tópico usando o Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: kehuan
ms.author: kehuan
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 669fc7a9ec5dfb446ef2755919c498fe6f60c9df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="document-collection-analysis"></a>Análise da coleção de documentos

Este cenário demonstra como resumir e analisar um grande conjunto de documentos, incluindo técnicas como aprendizado de frase, modelagem de tópico e análise de modelos de tópico usando o Azure ML Workbench. O Azure Machine Learning Workbench propicia um fácil aumento da coleção de documentos muito grandes e oferece mecanismos para treinar e ajustar modelos dentro de uma variedade de contextos computacionais, variando de computação local para Máquinas Virtuais de ciência de dados para o cluster Spark. Os blocos de anotações do Jupyter dentro do Azure Machine Learning Workbench oferecem um fácil desenvolvimento.

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

O repositório público do GitHub para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Visão geral

Com uma grande quantidade de dados (principalmente dados de texto não estruturados) coletados todos os dias, um desafio significativo é organizar, pesquisar e entender as vastas quantidades desses textos. Este cenário de análise de coleção de documentos demonstra um fluxo de trabalho de ponta a ponta eficiente e automatizado para analisar grandes coleções de documentos e habilitar tarefas NLP downstream.

Os principais elementos fornecidos por esse cenário são:

1. Aprendizado de importantes frases com várias palavras dos documentos.

1. Descoberta dos tópicos subjacentes apresentados na coleção de documentos.

1. Representação de documentos pela distribuição tópica.

1. Apresentação de métodos de organização, pesquisa e resumo de documentos com base no conteúdo tópico.

Os métodos apresentados nesse cenário podem permitir uma variedade de cargas de trabalho industriais críticas, como descoberta de anomalias de tendências de tópico, resumo de coleção de documentos e pesquisa de documentos semelhantes. Ele pode ser aplicado a muitos tipos diferentes de análise de documentos, como a legislação do governo, notícias, análises de produtos, comentários do cliente e artigos de pesquisa científica.

As técnicas/algoritmos de aprendizado de máquina usados neste cenário incluem:

1. Processamento de texto e limpeza

1. Aprendizado de Frase

1. Modelagem de tópico

1. Resumo de corpus

1. Tendências tópicas e detecção de anomalias

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para executar este exemplo são os seguintes:

* Certifique-se de que você tenha instalado devidamente o [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [Guia de início rápido de instalação e de criação](quickstart-installation.md).

* Este exemplo pode ser executado em qualquer contexto de computação. No entanto, recomenda-se executá-lo em um computador com vários núcleos com pelo menos 16 GB de memória e 5 GB de espaço em disco.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Análise de coleção de documentos" e selecione o modelo
5.  Clique em **Criar**

## <a name="data-description"></a>Descrição dos dados

O conjunto de dados usado neste cenário contém resumos de texto e metadados associados para cada ação legislativa tomada pelo Congresso dos EUA. Os dados são coletados de [GovTrack.us](https://www.govtrack.us/), que rastreia as atividades do Congresso dos Estados Unidos e ajuda os americanos a participarem de seu processo legislativo nacional. Os dados em massa podem ser baixados por meio [deste link](https://www.govtrack.us/data/congress/) usando um script manual, que não está incluído neste cenário. Os detalhes de como baixar os dados podem ser encontrados na [Documentação da API GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Fonte de dados

Neste cenário, os dados brutos coletados são uma série de ações legislativas introduzidas pelo Congresso dos EUA (projetos de lei e resoluções propostos) de 1973 a junho de 2017 (o 93º ao 115º congressos). Os dados coletados estão no formato JSON e contêm um conjunto avançado de informações sobre as ações legislativas. Consulte [este link do GitHub](https://github.com/unitedstates/congress/wiki/bills) para obter uma descrição detalhada dos campos de dados. Para fins de demonstração neste cenário, apenas um subconjunto de campos de dados eram extraídos dos arquivos JSON. Um arquivo TSV pré-compilado `CongressionalDataAll_Jun_2017.tsv` que contém registros dessas ações legislativas é fornecido neste cenário. O arquivo TSV pode ser baixado automaticamente pelos blocos de anotações `1_Preprocess_Text.ipynb` na pasta de blocos de anotações ou `preprocessText.py` no pacote Python.

### <a name="data-structure"></a>Estrutura de dados

Há nove campos de dados no arquivo de dados. Os nomes e as descrições do campo de dados são listados da seguinte maneira.

| Nome do campo | Tipo | Descrição | Contém o valor ausente |
|------------|------|-------------|---------------|
| `ID` | Cadeia de caracteres | A ID do projeto de lei/resolução. O formato deste campo é [tipo_do_projeto_de_lei] [número]-[congresso]. Por exemplo, "hconres1-93" significa que o tipo de projeto de lei é "hconres" (sigla para Resolução simultânea da casa, consulte [este documento](https://github.com/unitedstates/congress/wiki/bills#basic-information)), o número do projeto de lei é '1' e o número do Congresso é '93'. | Não |
| `Text` | Cadeia de caracteres | O conteúdo do projeto de lei/resolução. | Não |
| `Date` | Cadeia de caracteres | A data em que o projeto de lei/resolução foi inicialmente proposto. Em um formato de 'aaaa-mm-dd'. | Não |
| `SponsorName` | Cadeia de caracteres | O nome do principal responsável que propôs o projeto de lei/resolução. | Sim |
| `Type` | Cadeia de caracteres | O tipo do título do responsável principal, 'rep' (deputado) ou 'sen' (senador). | Sim |
| `State` | Cadeia de caracteres | O estado do responsável principal. | Sim |
| `District` | Número inteiro | O número do distrito do responsável principal se o título do responsável for deputado. | Sim |
| `Party` | Cadeia de caracteres | O partido do responsável principal. | Sim |
| `Subjects` | Cadeia de caracteres | Os termos de assunto adicionados cumulativamente pela Biblioteca do Congresso ao projeto de lei. Os termos são concatenados por vírgulas. Esses termos são escritos por uma pessoa na Biblioteca do Congresso e geralmente não estão presentes quando as informações no projeto de lei são publicadas pela primeira vez. Eles podem ser adicionados a qualquer momento. Portanto, no final da vida útil de um projeto de lei, talvez alguns assuntos possam não ser mais relevantes. | Sim |

## <a name="scenario-structure"></a>Estrutura do cenário

O exemplo de análise de coleção de documentos é organizado em dois tipos de entregas. O primeiro tipo é uma série de Blocos de anotações do iPython que mostram as descrições passo a passo de todo o fluxo de trabalho. O segundo tipo é um pacote Python, bem como alguns exemplos de código de como usar esse pacote. O pacote Python é genérico o suficiente para fornecer vários casos de uso.

Os arquivos neste exemplo são organizados da seguinte maneira.

| Nome do Arquivo | Tipo | Descrição |
|-----------|------|-------------|
| `aml_config` | Pasta | Pasta de configuração do Azure Machine Learning Workbench, consulte [esta documentação](./experiment-execution-configuration-reference.md) para obter configuração de execução de experimento detalhada |
| `Code` | Pasta | A pasta de código usada para salvar os scripts Python e o pacote Python |
| `Data` | Pasta | A pasta de dados usada para salvar arquivos intermediários |
| `notebooks` | Pasta | A pasta Blocos de anotações do Jupyter |
| `Code/documentAnalysis/__init__.py` | Arquivo Python | O arquivo de inicialização do pacote Python |
| `Code/documentAnalysis/configs.py` | Arquivo Python | O arquivo de configuração usado pelo pacote Python de análise de documentos, incluindo constantes predefinidas |
| `Code/documentAnalysis/preprocessText.py` | Arquivo Python | O arquivo Python usado para pré-processar os dados para tarefas downstream |
| `Code/documentAnalysis/phraseLearning.py` | Arquivo Python | O arquivo Python usado para aprender frases com base nos dados e transformar os dados brutos |
| `Code/documentAnalysis/topicModeling.py` | Arquivo Python | O arquivo Python usado para treinar um modelo de tópico LDA (Alocação Dirichlet Latente) |
| `Code/step1.py` | Arquivo Python | Etapa 1 da análise de coleção de documentos: pré-processar texto |
| `Code/step2.py` | Arquivo Python | Etapa 2 da análise de coleção de documentos: aprendizado de frase |
| `Code/step3.py` | Arquivo Python | Etapa 3 da análise de coleção de documentos: treinar e avaliar o modelo de tópico LDA |
| `Code/runme.py` | Arquivo Python | Exemplo da execução de todas as etapas em um arquivo |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Pré-processar texto e transformar os dados brutos |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Aprenda frases com base nos dados de texto (após a transformação de dados) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | Treinar o modelo de tópico LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | Resumir o conteúdo da coleção de documentos com base em um modelo de tópico LDA treinado |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | Analisar o conteúdo tópico de uma coleção de documentos de texto e correlacionar informações tópicas com relação a outros metadados associados à coleção de documentos |
| `notebooks/winprocess.py` | Arquivo Python | O script python para multiprocessamento usado por blocos de anotações |
| `README.md` | Arquivo markdown | O arquivo markdown README |

### <a name="data-ingestion-and-transformation"></a>Transformação e ingestão de dados

O conjunto de dados compilado `CongressionalDataAll_Jun_2017.tsv` é salvo no Armazenamento de Blobs e pode ser acessado tanto de dentro dos blocos de anotações quanto dos scripts Python. Há duas etapas para a ingestão e a transformação de dados: pré-processamento dos dados de texto e aprendizado de frase.

#### <a name="preprocess-text-data"></a>Pré-processar dados de texto

A etapa de pré-processamento aplica técnicas de processamento de linguagem natural para limpar e preparar os dados de texto brutos. Ela funciona como precursora para o aprendizado de frase sem supervisão e modelagem de tópico latente. A descrição passo a passo detalhada pode ser encontrada no bloco de anotações `1_Preprocess_Text.ipynb`. Também há um script Python `step1.py` correspondente a este bloco de anotações.

Nesta etapa, o arquivo de dados TSV é baixado do Armazenamento de Blobs do Azure e importado como um DataFrame Pandas. Cada elemento bruto no DataFrame é uma cadeia de caracteres de texto longa, única e coesa ou um 'documento'. Cada documento é dividido em partes de texto que provavelmente devem ser orações, frases ou sub-frases. A divisão é criada para proibir o processo de aprendizado de frase contra o uso de cadeias de caracteres de palavras entre frases ou entre sentenças ao aprender frases.

Há várias funções definidas para a etapa de pré-processamento no bloco de anotações e no pacote Python. A maioria do trabalho pode ser concluída chamando essas duas linhas de código.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Aprendizado de frase

A etapa do aprendizado de frase implementa uma estrutura básica para aprender frases importantes entre uma grande coleção de documentos. Ela é descrita no artigo intitulado "[Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)" (Modelagem de tópicos com várias palavras com árvore de frases restritas de fala conversacional), apresentado originalmente no Workshop IEEE sobre Tecnologia de língua falada em 2012. A implementação detalhada da etapa do aprendizado de frase é mostrada no Bloco de anotações iPython `2_Phrase_Learning.ipynb` e no script Python `step2.py`.

Esta etapa usa o texto limpo como entrada e aprende as frases mais evidente presentes em uma coleção grande de documentos. O aprendizado de frase é um processo iterativo que pode ser dividido em três tarefas: contagem de n-gramas, classificação de frases potenciais pelas Informações mútuas ponto a ponto ponderadas de suas palavras constituintes e a reescrita da frase para o texto. Essas três tarefas são executadas sequencialmente em várias iterações até que as frases especificadas tenham sido obtidas.

No pacote Python de análise do documento, uma classe Python `PhraseLearner` é definida no arquivo `phraseLearning.py`. Abaixo está o trecho de código usado para aprender frases.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> A etapa do aprendizado de frase é implementada com o multiprocessamento. No entanto, mais núcleos de CPU **NÃO** significa um tempo de execução mais rápido. Em nossos testes, o desempenho não é aprimorado com mais de oito núcleos devido à sobrecarga de multiprocessamento. Levou aproximadamente duas horas e meia para aprender 25.000 frases em um computador com oito núcleos (3.6 GHz).
>

### <a name="topic-modeling"></a>Modelagem de tópico

O aprendizado de um LDA de uso do modelo de tópico latente é a terceira etapa deste cenário. O pacote Python [gensim](https://radimrehurek.com/gensim/) é necessário nesta etapa para aprender um [modelo de tópico LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). O bloco de anotações correspondente para esta etapa é `3_Topic_Model_Training.ipynb`. Também é possível consultar `step3.py` sobre como usar o pacote de análise do documento.

Nesta etapa, a principal tarefa é aprender um modelo de tópico LDA e ajustar os hiperparâmetros. Há vários parâmetros que precisam ser ajustados ao treinar um modelo LDA, mas o parâmetro mais importante é o número de tópicos. Talvez poucos tópicos não tenham informações para a coleção de documentos; enquanto escolher muitos resulte no excesso de cluster de um corpus em muitos tópicos pequenos, altamente semelhantes. A finalidade deste cenário é mostrar como ajustar o número de tópicos. O Azure Machine Learning Workbench oferece liberdade para executar experimentos com diferentes configurações de parâmetro em diferentes contextos de computação.

No pacote Python de análise do documento, algumas funções foram definidas para ajudar os usuários a descobrir o melhor número de tópicos. A primeira abordagem é por meio da avaliação da coerência do modelo de tópico. Há quatro matrizes de avaliação com suporte: `u_mass`, `c_v`, `c_uci` e `c_npmi`. Os detalhes dessas quatro métricas são discutidos [neste documento](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A segunda abordagem é avaliar a perplexidade em um corpus mantido.

Para a avaliação de perplexidade, uma curva em forma de 'U' deve descobrir o melhor número de tópicos. E a posição do canto é a melhor opção. É recomendável avaliar várias vezes com diferentes sementes aleatórias e obter a média. A avaliação de coerência deve ser uma forma de 'n', que significa que a coerência aumenta com o aumento do número de tópicos e depois diminui. Um gráfico de exemplo de perplexidade e a coerência `c_v` estão sendo exibidos da seguinte maneira.

![Perplexidade](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![Coerência c_v](./media/scenario-document-collection-analysis/c_v_Coherence.png)

Neste cenário, a perplexidade aumenta significativamente após 200 tópicos, enquanto o valor da coerência diminui significativamente após 200 tópicos também. Com base nesses grafos e no desejo de tópicos mais gerais versus por tópicos clusterizados, escolher 200 tópicos deve ser uma boa opção.

É possível treinar um modelo de tópico LDA em uma execução de experimento ou treinar e avaliar vários modelos LDA com diferentes configurações do número de tópicos em uma única execução de experimento. É recomendável executar várias vezes para uma configuração e, em seguida, obter a média de avaliações de coerência e/ou de perplexidade. Os detalhes de como usar o pacote de análise do documentos podem ser encontrados no arquivo `step3.py`. O trecho de código de exemplo é o seguinte.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> O tempo de execução para treinar um modelo de tópico LDA depende de vários fatores, como o tamanho do corpus, configuração do hiperparâmetro e o número de núcleos no computador. Usar vários núcleos de CPU treina um modelo mais rapidamente. No entanto, com a mesma configuração de hiperparâmetro, mais núcleos significa menos atualizações durante o treinamento. Recomenda-se ter **pelo menos 100 atualizações para treinar um modelo LDA convergido**. A relação entre o número de atualizações e o de hiperparâmetros é discutida [nesta postagem](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) e [nesta postagem](http://miningthedetails.com/blog/python/lda/GensimLDA/). Em nossos testes, levou cerca de 3 horas para treinar um modelo LDA com 200 tópicos usando a configuração de `workers=15`, `passes=10`, `chunksize=1000` em um computador com 16 núcleos (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Resumo e análise do tópico

O resumo e a análise do tópico consiste em dois blocos de anotações, embora não haja funções correspondentes no pacote de análise de documentos.

No `4_Topic_Model_Summarization.ipynb`, ele mostra como resumir o conteúdo de um documento com base em um modelo de tópico LDA treinado. O resumo é aplicado a um modelo de tópico LDA aprendido na etapa 3. Ele mostra como medir a importância ou a qualidade de um tópico usando a medida de pureza de tópico para documento. Esta medida de pureza pressupõe que tópicos latentes que dominam os documentos nos quais eles aparecem são mais importantes semanticamente do que tópicos latentes distribuídos fracamente em muitos documentos. Esse conceito foi introduzido no artigo "[Latent Topic Modeling for Audio Corpus Summarization](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf) (Modelagem do tópico latente para resumo de corpus de áudio)."

O bloco de anotações `5_Topic_Model_Analysis.ipynb` mostra como analisar o conteúdo tópico de uma coleção de documentos e correlacionar informações tópicas com relação a outros metadados associados à coleção de documentos. Alguns gráficos são introduzidos neste bloco de anotações para ajudar os usuários a entenderem melhor o tópico aprendido e a coleção de documentos.

## <a name="conclusion"></a>Conclusão

Este cenário do mundo real destaca como usar técnicas de análise de texto conhecidas (neste caso, aprendizado de frase e modelagem de tópico LDA) para produzir um modelo robusto e como o Azure Machine Learning Workbench pode ajudar a acompanhar o desempenho do modelo e executar perfeitamente aprendizes em maior escala. Com mais detalhes:

* Use o aprendizado de frase e a modelagem de tópico para processar uma coleção de documentos e criar um modelo robusto. Se a coleção de documentos for grande, o Azure Machine Learning Workbench poderá aumentá-lo facilmente. Além disso, os usuários têm a liberdade de executar experimentos em diferentes contextos de computação facilmente de dentro do Azure Machine Learning Workbench.

* O Azure Machine Learning Workbench fornece ambas as opções para executar blocos de anotações passo a passo e o script Python para executar um experimento inteiro.

* Ajuste de hiperparâmetro usando o Azure Machine Learning Workbench para encontrar o melhor número de tópicos necessários para aprender. O Azure Machine Learning Workbench pode ajudar a acompanhar o desempenho do modelo e executar perfeitamente diferentes aprendizes em maior escala.

* O Azure Machine Learning Workbench pode gerenciar o histórico de execuções e os modelos aprendidos. Ele permite que os cientistas de dados identifiquem rapidamente os modelos com o melhor desempenho e encontrem os scripts e os dados usados para gerá-los.

## <a name="references"></a>Referências

* **Timothy J. Hazen, Fred Richardson**, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) (Modelando frases com várias palavras com árvore de frases restritas para melhor modelagem de tópico de um discurso de conversação). Workshop SLT (tecnologia de língua falada), 2012 IEEE. IEEE, 2012.

* **Timothy J. Hazen**, [_Modelagem de tópico latente para resumo do corpus de áudio_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12ª Conferência anual da International Speech Communication Association. 2011.

* **Michael Roder, Andreas Both, Alexander Hinneburg**, [_Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf) (Explorando o espaço das medidas de coerência de tópico). Trabalhos da oitava conferência internacional ACM sobre pesquisa Web e mineração de dados. ACM, 2015.
