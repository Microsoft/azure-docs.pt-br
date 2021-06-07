---
title: Referência de módulo de hash de recurso
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo hash de recurso no designer de Azure Machine Learning para personalizarr dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420777"
---
# <a name="feature-hashing-module-reference"></a>Referência de módulo de hash de recurso

Este artigo descreve um módulo incluído no Azure Machine Learning designer.

Use o módulo hash de recurso para transformar um fluxo de texto em inglês em um conjunto de recursos inteiros. Em seguida, você pode passar esse conjunto de recursos com hash para um algoritmo de aprendizado de máquina para treinar um modelo de análise de texto.

A funcionalidade de hash de recurso fornecida neste módulo é baseada na estrutura nimbusml. Para obter mais informações, consulte [classe NgramHash](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>O que é o hash de recurso?

O hash de recurso funciona convertendo tokens exclusivos em inteiros. Ele opera nas cadeias de caracteres exatas que você fornece como entrada e não executa nenhuma análise linguística ou pré-processamento. 

Por exemplo, faça um conjunto de frases simples como essas, seguido por uma pontuação de sentimentos. Suponha que você deseja usar esse texto para criar um modelo.

|Texto do usuário|Sentimento|
|--------------|---------------|
|Adorei este livro|3|
|Eu odiei este livro|1|
|Este livro foi ótimo|3|
|Eu adoro livros|2|

Internamente, o módulo hash de recurso cria um dicionário de n-grams. Por exemplo, a lista de bigrams para esse conjunto de tais seria algo assim:

|Termo (bigrams)|Frequência|
|------------|---------------|
|Este livro|3|
|Eu adore|1|
|Eu odiei|1|
|Eu adoro|1|

Você pode controlar o tamanho dos n-grams usando a propriedade **n-grams** . Se você escolher bigrams, unigrams também serão computados. O dicionário também incluiria termos únicos como estes:

|Termo (unigrams)|Frequência|
|------------|---------------|
|agendar|3|
|I|3|
|livros|1|
|deveria|1|

Depois que o dicionário é criado, o módulo hash de recurso converte os termos do dicionário em valores de hash. Em seguida, ele computa se um recurso foi usado em cada caso. Para cada linha de dados de texto, o módulo gera um conjunto de colunas, uma coluna para cada recurso com hash.

Por exemplo, após o hash, as colunas de recurso podem ter uma aparência semelhante a esta:

|Classificação|Recurso de hash 1|Recurso de hash 2|Recurso de hash 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se o valor na coluna for 0, a linha não conterá o recurso com hash.
* Se o valor for 1, a linha conterá o recurso.

O hash de recurso permite que você represente documentos de texto de comprimento variável como vetores de recursos numéricos de comprimento igual para reduzir a dimensionalidade. Se você tentou usar a coluna de texto para treinamento como está, ela seria tratada como uma coluna de recursos categóricos com muitos valores distintos.

As saídas numéricas também possibilitam o uso de métodos comuns de aprendizado de máquina, incluindo classificação, clustering e recuperação de informações. Como as operações de pesquisa podem usar hashes inteiros em vez de comparações de cadeias de caracteres, obter os pesos dos recursos também é muito mais rápido.

## <a name="configure-the-feature-hashing-module"></a>Configurar o módulo hash de recurso

1.  Adicione o módulo hash de recurso ao seu pipeline no designer.

1. Conecte o conjunto de um que contém o texto que você deseja analisar.

    > [!TIP]
    > Como o hash de recurso não executa operações léxicas como lematização ou truncamento, às vezes você pode obter resultados melhores por meio de pré-processamento de texto antes de aplicar o hash de recurso. 

1. Defina as **colunas de destino** para as colunas de texto que você deseja converter em recursos com hash. Tenha em mente que:

    * As colunas devem ser do tipo de dados de cadeia de caracteres.
    
    * Escolher várias colunas de texto pode ter um impacto significativo na dimensionalidade do recurso. Por exemplo, o número de colunas para um hash de 10 bits vai de 1.024 para uma única coluna a 2.048 para duas colunas.

1. Use **bits de hash** para especificar o número de bits a serem usados ao criar a tabela de hash.
    
    O tamanho de bit padrão é 10. Para muitos problemas, esse valor é adequado. Talvez seja necessário mais espaço para evitar colisões, dependendo do tamanho do vocabulário de n-grams no texto de treinamento.
    
1. Para **N-grams**, insira um número que defina o comprimento máximo dos N-grams a serem adicionados ao dicionário de treinamento. Um n-Gram é uma sequência de *n* palavras, tratada como uma unidade exclusiva.

    Por exemplo, se você inserir 3, unigrams, bigrams e trigrams serão criados.

1. Enviar o pipeline.

## <a name="results"></a>Resultados

Depois que o processamento é concluído, o módulo gera um conjunto de resultados transformado no qual a coluna de texto original foi convertida em várias colunas. Cada coluna representa um recurso no texto. Dependendo do quão significativo for o dicionário, o conjunto de resultados resultante pode ser grande:

|Nome da coluna 1|Tipo de coluna 2|
|-------------------|-------------------|
|UserText|Coluna de dados original|
|SENTIMENTO|Coluna de dados original|
|UserText-recurso de hash 1|Coluna de recurso com hash|
|UserText-recurso de hash 2|Coluna de recurso com hash|
|UserText-recurso de hash n|Coluna de recurso com hash|
|UserText-recurso de hashing 1024|Coluna de recurso com hash|

Depois de criar o conjunto de dados transformado, você pode usá-lo como a entrada para o módulo modelo de treinamento.
 
## <a name="best-practices"></a>Práticas recomendadas

As práticas recomendadas a seguir podem ajudá-lo a obter o máximo do módulo de hash de recurso:

* Adicione um módulo de texto de pré-processamento antes de usar o hash de recurso para pré-processar o texto de entrada. 

* Adicione um módulo selecionar colunas após o módulo hash de recurso para remover as colunas de texto do conjunto de resultados de saída. Você não precisa das colunas de texto depois que os recursos de hash tiverem sido gerados.
    
* Considere usar essas opções de pré-processamento de texto para simplificar os resultados e melhorar a precisão:

    * Quebra de palavras
    * Parando a remoção do Word
    * Normalização de caso
    * Remoção de Pontuação e caracteres especiais
    * Lematização  

O conjunto ideal de métodos de pré-processamento a serem aplicados em qualquer solução depende do domínio, do vocabulário e da necessidade de negócios. Pipeline com seus dados para ver quais métodos de processamento de texto são mais eficazes.

## <a name="next-steps"></a>Próximas etapas
            
Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning