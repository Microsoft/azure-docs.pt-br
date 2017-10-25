---
title: "Correspondência de P e R usando o Azure Machine Learning Workbench | Microsoft Docs"
description: "Como usar vários métodos de aprendizado de máquina eficazes para corresponder consultas em aberto a pares de perguntas/respostas de perguntas frequentes pré-existentes."
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
ms.manager: tihazen
ms.openlocfilehash: 8edc21fb8f42ee5897c4e938045cc1f42aedb3ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Correspondência de P e R usando o Azure Machine Learning Workbench
Responder a perguntas abertas é difícil e geralmente requer esforço manual de SMEs (especialistas no assunto). Para ajudar a reduzir as demandas em SMEs internos, as empresas geralmente criam listas de Perguntas frequentes como uma forma de auxiliar os usuários. Este exemplo demonstra vários métodos de aprendizado de máquina eficazes para corresponder consultas em aberto a pares de pergunta/respostas de perguntas frequentes pré-existentes. Este exemplo demonstra um processo de desenvolvimento fácil para criar essa solução usando o Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Visão geral

Este exemplo resolve o problema de mapear perguntas de usuário para pares P e R (perguntas e respostas) pré-existentes como é normalmente fornecido em uma lista de Perguntas frequentes ou nos pares de P e R apresentados em sites como o [Stack Overflow](https://stackoverflow.com/). Há muitas abordagens para corresponder uma pergunta à sua resposta correta, como localizar a resposta mais semelhante à pergunta. No entanto, neste exemplo, as perguntas abertas são correspondidas a perguntas feitas anteriormente supondo que cada resposta nas Perguntas frequentes possam responder a várias perguntas semanticamente equivalentes.

As principais etapas necessárias para fornecer essa solução são as seguintes:

1. Limpar e processar dados de texto.
2. Aprenda frases informativas, que são sequências de várias palavras que fornecem mais informações quando exibidas em sequência do que quando tratadas de maneira independente.
3. Extrair recursos de dados de texto.
4. Treine modelos de classificação de texto e avalie o desempenho do modelo.


## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para executar este exemplo são os seguintes:

1. Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
2. Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
3. Este exemplo pode ser executado em qualquer contexto de computação. No entanto, recomenda-se executá-lo em um computador com vários núcleos com pelo menos 16 GB de memória e 5 GB de espaço em disco.

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Correspondência de P e R" e selecione o modelo
5.  Clique em **Criar**

## <a name="data-description"></a>Descrição dos dados

O conjunto de dados usado neste exemplo é um Despejo de dados do Stack Exchange armazenado em [archive.org](https://archive.org/details/stackexchange). Esses dados são um despejo anônimo de todo o conteúdo enviado por usuários na [rede do Stack Exchange](https://stackexchange.com/). Cada site na rede é formatado como um arquivo separado composto de arquivos XML compactados com o 7-zip usando a compactação bzip2. Cada arquivo de site inclui publicações, usuários, votos, comentários, PostHistory e PostLinks. 

### <a name="data-source"></a>Fonte de dados

Este exemplo usa os [Dados de Postagens (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) e [Dados PostLinks (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) do site do Stack Overflow. Para obter informações completas sobre o esquema, consulte [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

O campo `PostTypeId` nos dados de Postagens indica se uma postagem é um `Question` ou um `Answer`. O campo `PostLinkTypeId` nos dados PostLinks indica se duas postagens estão vinculadas ou duplicadas. Normalmente, as postagens de perguntas incluem algumas marcas, que são palavras-chave que categorizam uma pergunta com outras perguntas semelhantes/duplicadas. Há algumas marcas com alta frequência, como `javascript`, `java`, `c#`, `php` etc., que consistem de em um número maior de postagens de perguntas. Neste exemplo, é extraído um subconjunto de pares de P e R com a marca `javascript`.

Além disso, uma postagem de pergunta pode estar relacionada a várias postagens de resposta ou a postagens de perguntas duplicadas. Para construir uma lista de perguntas frequentes com base nesses dois conjuntos de dados, são considerados alguns critérios de coleta de dados. Os três conjuntos de dados compilados são selecionados usando um script SQL, que não está incluído neste exemplo. A descrição dos dados resultante é a seguinte:

- `Original Questions (Q)`: as postagens de perguntas são feitas e respondidas no site do Stack Overflow.
- `Duplications (D)`: as postagens de perguntas duplicam outras perguntas pré-existentes (`PostLinkTypeId = 3`), que são as perguntas originais. As duplicações são consideradas semanticamente equivalentes às perguntas originais no sentido que a resposta fornecida à pergunta original também responde a nova pergunta duplicada.
- `Answers (A)`: cada pergunta original e suas duplicações podem ser vinculadas a mais de uma postagem de resposta. Este exemplo seleciona apenas a postagem de resposta aceita pelo autor original (filtrada por `AcceptedAnswerId`) ou com a pontuação mais alta (classificada por `Score`) nas perguntas originais. 

A combinação desses três conjuntos de dados cria pares de P e R em que uma resposta (A) é mapeada para uma pergunta (Q) original e várias perguntas duplicadas (D) conforme ilustrado pelo diagrama de dados a seguir:

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Estrutura de dados

O esquema de dados e os links de download direto dos três conjuntos de dados podem ser encontrados na tabela a seguir:

| Conjunto de dados | Campo | Tipo | Descrição
| ----------|------------|------------|--------
| [perguntas](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | Cadeia de caracteres | A ID exclusiva da pergunta (chave primária)
|  | AnswerId | Cadeia de caracteres | A ID exclusiva da resposta por pergunta
|  | Text0 | Cadeia de caracteres | Os dados de texto brutos incluindo o título e o corpo da pergunta
|  | CreationDate | Timestamp | O carimbo de data/hora de quando a pergunta foi feita
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | Cadeia de caracteres | A ID exclusiva da duplicação (chave primária)
|  | AnswerId | Cadeia de caracteres | A ID da resposta associada à duplicação
|  | Text0 | Cadeia de caracteres | Os dados de texto brutos incluindo o título e o corpo da duplicação
|  | CreationDate | Timestamp | O carimbo de data/hora de quando a duplicação foi perguntada
| [respostas](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | Cadeia de caracteres | A ID exclusiva da resposta (chave primária)
|  | text0 | Cadeia de caracteres | Os dados de texto brutos da resposta


## <a name="scenario-structure"></a>Estrutura do cenário

O exemplo de correspondência de P e R é apresentado por meio de três tipos de arquivos. O primeiro tipo é uma série de Blocos de anotações do Jupyter que mostram as descrições passo a passo de todo o fluxo de trabalho. O segundo tipo é um conjunto de arquivos Python que contêm módulos Python personalizados para aprendizado de frase e extração de recursos. Esses módulos Python são genéricos o suficiente não apenas para fornecer este exemplo, mas também outros casos de uso. O terceiro tipo é um conjunto de arquivos Python para ajustar os hiperparâmetros e para rastrear o desempenho do modelo usando o Azure Machine Learning Workbench.

Os arquivos neste exemplo são organizados da seguinte maneira.

| Nome do Arquivo | Tipo | Descrição
| ----------|------------|--------
| `Image` | Pasta | A pasta usada para salvar as imagens para o arquivo LEIAME
| `notebooks` | Pasta | A pasta Blocos de anotações do Jupyter
| `modules` | Pasta | A pasta de módulos Python
| `scripts` | Pasta | A pasta de arquivos Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Bloco de anotações do Jupyter | Acessar os dados de exemplo, pré-processar o texto e preparar o treinamento e os conjuntos de dados de teste
| `notebooks/Part_2_Phrase_Learning.ipynb` | Bloco de anotações do Jupyter | Aprender frases informativas e crie tokens de texto em representações BOWs (Bolsa de palavras)
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Bloco de anotações do Jupyter | Extrair recursos, treinar modelos de classificação de texto e desempenho do modelo de avaliação
| `modules/__init__.py` | Arquivo Python | O arquivo de inicialização do pacote Python
| `modules/phrase_learning.py` | Arquivo Python | Os módulos Python usados para transformar os dados brutos e aprender frases informativas
| `modules/feature_extractor.py` | Arquivo Python | Os módulos Python extraem recursos para o treinamento do modelo
| `scripts/naive_bayes.py` | Arquivo Python | O Python para ajustar hiperparâmetros no modelo Naive Bayes
| `scripts/random_forest.py` | Arquivo Python | O Python para ajustar hiperparâmetros no modelo Random Forest
| `README.md` | Arquivo markdown | O arquivo markdown README

> [!NOTE]
> A série de blocos de anotações é criada no Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Transformação e ingestão de dados

Os três conjuntos de dados compilados são armazenados em um Armazenamento de blobs e recuperados no bloco de anotações do `Part_1_Data_Preparation.ipynb`.  

Antes de treinar os modelos de classificação de texto, o texto nas perguntas é limpo e pré-processado para excluir trechos de código. Um aprendizado de frase sem supervisão é aplicado por meio do material de treinamento para aprender sequências informativas de várias palavras. Essas frases são representadas como unidades de única composta únicas na personalização BOWs (Bolsa de palavras) downstream usadas pelos modelos de classificação de texto.

As descrições detalhadas passo a passo do pré-processamento de texto e do aprendizado de frase podem ser encontradas nos Blocos de anotações `Part_1_Data_Preparation.ipynb` e `Part_2_Phrase_Learning.ipynb`, respectivamente.

### <a name="modeling"></a>Modelagem

Este exemplo foi criado para classificar novas perguntas com relação aos pares de P e R pré-existentes treinando modelos de classificação de texto em que cada par de P e R pré-existente é uma sub-rede e uma classe exclusiva das perguntas duplicadas para cada para de P e R disponível como material de treinamento. No exemplo, as perguntas originais e 75% das perguntas duplicadas são mantidas para treinamento e 25% das perguntas duplicadas postadas mais recentemente são mantidas como dados de avaliação.

O modelo de classificação usa um método conjunto para agregar três classificadores base, incluindo o **Naive Bayes**, o **Support Vector Machine** e a **Random Forest**. Em cada classificador base, o `AnswerId` é usado como o rótulo de classe e as representações BOWs são usadas como os recursos.

O processo de treinamento de modelo é ilustrado no `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Avaliação

Duas métricas de avaliação diferentes são usadas para avaliar o desempenho. 
1. `Average Rank (AR)`: indica a posição média em que a resposta correta é encontrada na lista de pares de P e R recuperados (do conjunto completo de 103 classes de resposta). 
2. `Top 3 Percentage`: indica o percentual de perguntas de teste que a resposta correta pode ser recuperada nas três principais opções na lista classificada retornada. 

A avaliação é demonstrada em `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Conclusão

Este exemplo destaca como usar técnicas de análise de texto conhecidas, como o aprendizado de frase e a classificação de texto, para produzir um modelo robusto. Ele também demonstra como o Azure Machine Learning Workbench pode ajudar com o desenvolvimento de soluções interativas e rastrear o desempenho do modelo. 

Alguns dos principais destaques desse exemplo são:

- O problema de correspondência de pergunta e de resposta pode ser resolvido efetivamente com o aprendizado de frase e com modelos de classificação de texto.
- Demonstrando o desenvolvimento de modelos interativos com o Azure Machine Learning Workbench e o Jupyter Notebook.
- O Azure Machine Learning Workbench gerencia o histórico de execuções e os modelos aprendidos com o registro em log de métricas de avaliação para fins de comparação. Esses recursos permitem o ajuste rápido de hiperparâmetros e ajuda a identificar os modelos com o melhor desempenho.


## <a name="references"></a>Referências

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) (Modelando frases com várias palavras com árvore de frases restritas para melhor modelagem de tópico de um discurso de conversação). Workshop SLT (tecnologia de língua falada), 2012 IEEE. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) (Técnicas de treinamento MCE para identificação de tópicos de documentos de áudio falado) nas Transações IEEE no processamento de áudio, fala e linguagem, vol. 19, no. 8, p. 2451-2460, nov. 2011.
