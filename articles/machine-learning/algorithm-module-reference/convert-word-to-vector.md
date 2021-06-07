---
title: 'Converter palavra em vetor: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar três modelos do Word2Vec fornecidos para extrair um vocabulário e suas incorporações de palavras correspondentes de um corpus de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536725"
---
# <a name="convert-word-to-vector-module"></a>Converter palavra em módulo vetorial

Este artigo descreve como usar a palavra converter em módulo vetorial no Azure Machine Learning designer para executar estas tarefas:

- Aplique vários modelos de Word2Vec (Word2Vec, FastText, diferenciada modelo pretreinado) na Corpus de texto que você especificou como entrada.
- Gere um vocabulário com incorporações de palavras.

Esse módulo usa a biblioteca Gensim. Para obter mais informações sobre o Gensim, consulte seu [site oficial](https://radimrehurek.com/gensim/apiref.html), que inclui tutoriais e uma explicação dos algoritmos.

### <a name="more-about-converting-words-to-vectors"></a>Mais sobre a conversão de palavras em vetores

Converter palavras em vetores ou em vetores de palavras é um processo de NLP (processamento de idioma natural). O processo usa modelos de linguagem para mapear palavras em um espaço vetorial. Um espaço de vetor representa cada palavra por um vetor de números reais. Ele também permite que palavras com significados semelhantes tenham representações semelhantes.

Use incorporações de palavras como entrada inicial para tarefas de downstream NLP, como classificação de texto e análise de sentimentos.

Entre várias tecnologias de incorporação de palavras, neste módulo, implementamos três métodos amplamente usados. Dois, Word2Vec e FastText, são modelos de treinamento online. O outro é um modelo pretreinado, diferenciada-wiki-gigaword-100. 

Os modelos de treinamento online são treinados em seus dados de entrada. Os modelos pretreinados são treinados offline em um corpus de texto maior (por exemplo, Wikipédia, Google News) que geralmente contém cerca de 100.000.000.000 palavras. A incorporação de palavras permanece constante durante a vetorização de palavras. Modelos de palavras pretreinados fornecem benefícios como o tempo de treinamento reduzido, melhores vetores de palavras codificados e desempenho geral aprimorado.

Veja algumas informações sobre os métodos:

+ Word2Vec é uma das técnicas mais populares para aprender incorporações de palavras usando uma rede neural superficial. A teoria é discutida neste documento, disponível como um download de PDF: [estimativa eficiente de representações de palavras no espaço de vetor](https://arxiv.org/pdf/1301.3781.pdf). A implementação neste módulo é baseada na [biblioteca Gensim para Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ A teoria do FastText é explicada neste documento, disponível como um download de PDF: [enriquecer vetores de palavras com informações de subpalavra](https://arxiv.org/pdf/1607.04606.pdf). A implementação neste módulo é baseada na [biblioteca Gensim para FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ O modelo pretreinado diferenciada é diferenciada-wiki-gigaword-100. É uma coleção de vetores pretreinados com base em uma corpus de texto da Wikipédia, que contém tokens de 5.600.000.000 e 400.000 palavras informadas de vocabulário. Um download de PDF está disponível: [diferenciada: vetores globais para representação de palavras](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Como configurar a conversão de palavras em vetor

Este módulo requer um conjunto de um DataSet que contém uma coluna de texto. O texto pré-processado é melhor.

1. Adicione o módulo **Converter palavras em vetor** em seu pipeline.

2. Como entrada para o módulo, forneça um conjunto de dados que contenha uma ou mais colunas de texto.

3. Para **coluna de destino**, escolha apenas uma coluna que contenha texto para processar.

    Como esse módulo cria um vocabulário a partir do texto, o conteúdo das colunas é diferente, o que leva a conteúdo de vocabulário diferente. É por isso que o módulo aceita apenas uma coluna de destino.

4. Para a  **estratégia Word2Vec**, escolha entre **diferenciada modelo de inglês pretreinado**, **Gensim Word2Vec** e **Gensim FastText**.

5. Se a **estratégia Word2Vec** for **Gensim Word2Vec** ou **Gensim FastText**:

    + Para o **algoritmo de treinamento do Word2Vec**, escolha entre **Skip_gram** e **CBOW**. A diferença é introduzida no [documento original (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        O método padrão é **Skip_gram**.

    + Para o **tamanho da incorporação de palavras**, especifique a dimensionalidade dos vetores de palavra. Essa configuração corresponde ao `size` parâmetro em Gensim.

        O tamanho de inserção padrão é 100.

    + Para **tamanho da janela de contexto**, especifique a distância máxima entre a palavra que está sendo prevista e a palavra atual. Essa configuração corresponde ao `window` parâmetro em Gensim.

        O tamanho padrão da janela é 5.

    + Para o **número de épocas**, especifique o número de épocas (iterações) em relação ao corpus. Corresponde ao `iter` parâmetro em Gensim.

        O número de época padrão é 5.

6. Para **tamanho máximo de vocabulário**, especifique o número máximo de palavras no vocabulário gerado.

    Se houver mais palavras exclusivas do que o tamanho máximo, remova as raras.

    O tamanho de vocabulário padrão é 10.000.

7. Para **contagem mínima de palavras**, forneça uma contagem mínima de palavras. O módulo irá ignorar todas as palavras que têm uma frequência inferior a esse valor.

    O valor padrão é 5.

8. Envie o pipeline.

## <a name="examples"></a>Exemplos

O módulo tem uma saída:

+ **Vocabulário com incorporações**: contém o vocabulário gerado, junto com a inserção de cada palavra. Uma dimensão ocupa uma coluna.

O exemplo a seguir mostra como funciona a palavra converter em módulo de vetor. Ele usa converter palavra em vetor com configurações padrão para o conjunto de conjuntos de o SP 500 do Wikipédia pré-processado.

### <a name="source-dataset"></a>Conjunto de dados de origem

O conjunto de conteúdo contém uma coluna de categoria, juntamente com o texto completo buscado da Wikipédia. A tabela a seguir mostra alguns exemplos representativos.

|Texto|
|----------|
|nasdaq 100 component s p 500 component foundation founder location city apple campus 1 infinite loop street infinite loop cupertino california cupertino california location country united states...|
|br nasdaq 100 nasdaq 100 component br s p 500 s p 500 component industry computer software foundation br founder charles geschke br john warnock location adobe systems...|
|s p 500 s p 500 component industry automotive industry automotive predecessor general motors corporation 1908 2009 successor...|
|s p 500 s p 500 component industry conglomerate company conglomerate foundation founder location city fairfield connecticut fairfield connecticut location country usa area...|
|br s p 500 s p 500 component foundation 1903 founder william s harley br arthur davidson harley davidson founder arthur davidson br walter davidson br william a davidson location...|

### <a name="output-vocabulary-with-embeddings"></a>Vocabulário de saída com incorporações

A tabela a seguir contém a saída desse módulo, colocando o conjunto de dados da Wikipédia SP 500 como entrada. A coluna mais à esquerda indica o vocabulário. Seu vetor de incorporação é representado por valores de colunas restantes na mesma linha.

|Vocabulário|Inserindo dim 0|Inserindo dim 1|Inserindo dim 2|Inserindo dim 3|Inserindo dim 4|Inserindo dim 5|...|Inserindo dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|componente|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|-0,151542|-0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|-0,530901
fundação|-0,166819|-0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
fundador|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
local|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
apple|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
campus|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
infinito|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
loop|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

Neste exemplo, usamos a **Gensim Word2Vec** padrão para a **estratégia Word2Vec**, e o **algoritmo de treinamento** é **Skip-Gram**. O **comprimento da incorporação de palavras** é 100, então temos 100 colunas de inserção.

## <a name="technical-notes"></a>Observações técnicas

Esta seção contém dicas e respostas para perguntas frequentes.

+ Diferença entre o modelo de treinamento online e o pretreinado:

    No módulo converter palavra em vetor, fornecemos três estratégias diferentes: dois modelos de treinamento online e um modelo pretreinado. Os modelos de treinamento online usam seu conjunto de dados de entrada como dado de treinamento e geram vocabulário e vetores de palavras durante o treinamento. O modelo pretreinado já é treinado por um corpus de texto muito maior, como a Wikipédia ou o texto do Twitter. O modelo pretreinado é, na verdade, uma coleção de pares de palavras/incorporação.  

    O modelo pré-treinado diferenciada resume um vocabulário do conjunto de dados de entrada e gera um vetor de incorporação para cada palavra do modelo pretreinado. Sem treinamento online, o uso de um modelo pretreinado pode economizar tempo de treinamento. Ele tem melhor desempenho, especialmente quando o tamanho do conjunto de dados de entrada é relativamente pequeno.

+ Tamanho da incorporação:

    Em geral, o comprimento da incorporação de palavras é definido como algumas centenas. Por exemplo, 100, 200, 300. Um pequeno tamanho de incorporação significa um pequeno espaço de vetor, que poderia causar colisões de incorporação de palavras.  

    O tamanho das incorporações de palavras é fixo para modelos pretreinados. Neste exemplo, o tamanho de inserção de diferenciada-wiki-gigaword-100 é 100.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 

Para obter uma lista de erros específicos para os módulos do designer, consulte [Machine Learning códigos de erro](designer-error-codes.md).
