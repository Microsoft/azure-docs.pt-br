---
title: Extrair recursos de N-Gram de referência de módulo de texto
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo extrair N-Gram no designer de Azure Machine Learning para personalizarr dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861435"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrair recursos de N-Gram de referência de módulo de texto

Este artigo descreve um módulo no designer de Azure Machine Learning. Use o módulo extrair recursos N-Gram de texto para *Personalizar* dados de texto não estruturados. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuração dos recursos de extrair N-Gram do módulo de texto

O módulo dá suporte aos seguintes cenários para usar um dicionário de n-Gram:

* [Crie um novo dicionário de n-Gram](#create-a-new-n-gram-dictionary) a partir de uma coluna de texto livre.

* [Use um conjunto existente de recursos de texto](#use-an-existing-n-gram-dictionary) para personalizar uma coluna de texto livre.

* [Pontuar ou implantar um modelo](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) que usa n-grams.

### <a name="create-a-new-n-gram-dictionary"></a>Criar um novo dicionário de n-Gram

1.  Adicione os recursos extrair N-Gram do módulo de texto ao seu pipeline e conecte o conjunto de módulos que contém o texto que você deseja processar.

1.  Use **coluna de texto** para escolher uma coluna de tipo de cadeia de caracteres que contém o texto que você deseja extrair. Como os resultados são detalhados, você pode processar apenas uma única coluna de cada vez.

1. Defina o **modo de vocabulário** a ser **criado** para indicar que você está criando uma nova lista de recursos de n-Gram. 

1. Defina o **tamanho de n-gramas** para indicar o tamanho *máximo* dos n-gramas a serem extraídos e armazenados. 

    Por exemplo, se você inserir 3, unigrams, bigrams e trigrams serão criados.

1. **Função de peso** especifica como criar o vetor de recurso de documento e como extrair o vocabulário de documentos.

    * **Peso binário**: atribui um valor de presença binária para os n-grams extraídos. O valor de cada n-Gram é 1 quando ele existe no documento; caso contrário, 0.

    * **TF Weight**: atribui uma pontuação de frequência de termo (TF) para os n-grams extraídos. O valor de cada n-Gram é sua frequência de ocorrência no documento.

    * **Peso IDF**: atribui uma pontuação de IDF (frequência de documento inversa) para os n-gramas extraídos. O valor de cada n-Gram é o log do tamanho de Corpus dividido por sua frequência de ocorrência no Corpus inteiro.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF Weight**: atribui uma pontuação de termo de frequência/documento inverso (TF/IDF) para os n-grams extraídos. O valor de cada n-Gram é sua pontuação de TF multiplicada por sua pontuação de IDF.

1. Defina o **comprimento mínimo da palavra** como o número mínimo de letras que podem ser usadas em qualquer *palavra única* em um n-Gram.

1. Use o **comprimento máximo da palavra** para definir o número máximo de letras que podem ser usadas em qualquer *palavra única* em um n-grama.

    Por padrão, são permitidos até 25 caracteres por palavra ou token.

1. Use a **frequência mínima absoluta do documento n-Gram** para definir as ocorrências mínimas necessárias para que qualquer n-Gram seja incluído no dicionário de n-Gram. 

    Por exemplo, se você usar o valor padrão de 5, qualquer n-Gram deverá aparecer pelo menos cinco vezes no corpus a ser incluído no dicionário de n-Gram. 

1.  Defina a **taxa máxima de documentos de n-grama** para a proporção máxima do número de linhas que contêm um n-Gram específico, além do número de linhas no Corpus geral.

    Por exemplo, uma taxa de 1 indicaria que, mesmo se um n-Gram específico estiver presente em cada linha, o n-Gram poderá ser adicionado ao dicionário de n-Gram. Normalmente, uma palavra que ocorre em cada linha seria considerada uma palavra de ruído e seria removida. Para filtrar as palavras de ruído dependentes do domínio, tente reduzir essa taxa.

    > [!IMPORTANT]
    > A taxa de ocorrência de palavras específicas não é uniforme. Varia de documento para documento. Por exemplo, se você estiver analisando comentários de clientes sobre um produto específico, o nome do produto poderá ser muito alta, e perto de uma palavra de ruído, mas ser um termo significativo em outros contextos.

1. Selecione a opção **normalizar vetores de recurso de n-Gram** para normalizar os vetores de recurso. Se essa opção estiver habilitada, cada vetor de recurso de n-Gram será dividido por sua norma de L2.

1. Envie o pipeline.

### <a name="use-an-existing-n-gram-dictionary"></a>Usar um dicionário de n-Gram existente

1.  Adicione os recursos extrair N-Gram do módulo de texto ao seu pipeline e conecte o conjunto de módulos que contém o texto que você deseja processar à porta do **conjunto** de recursos.

1.  Use a **coluna texto** para selecionar a coluna de texto que contém o texto que você deseja personalizar. Por padrão, o módulo seleciona todas as colunas do tipo **cadeia de caracteres**. Para obter melhores resultados, processe uma única coluna de cada vez.

1. Adicione o conjunto de dados salvo que contém um dicionário de n-Gram gerado anteriormente e conecte-o à porta de **vocabulário de entrada** . Você também pode conectar a saída de **vocabulário de resultado** de uma instância upstream do módulo extrair recursos N-Gram de texto.

1. Para o **modo vocabulário**, selecione a opção atualização **somente leitura** na lista suspensa.

   A opção **ReadOnly** representa o corpus de entrada para o vocabulário de entrada. Em vez de calcular frequências de termo do novo conjunto de dados de texto (na entrada à esquerda), os pesos de n-Gram do vocabulário de entrada são aplicados como estão.

   > [!TIP]
   > Use esta opção quando estiver pontuando um classificador de texto.

1.  Para todas as outras opções, consulte as descrições de propriedade na [seção anterior](#create-a-new-n-gram-dictionary).

1.  Envie o pipeline.

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>Pipeline de inferência de compilação que usa n-grams para implantar um ponto de extremidade em tempo real

Um pipeline de treinamento que contém o **recurso extrair N-grams do texto e do** **modelo de Pontuação** para fazer previsão no conjunto de dados de teste, é criado na seguinte estrutura:

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="Exemplo de pipeline de treinamento para extrair N-grams" border="true":::

**O modo de vocabulário** do **recurso extrair N-grams circulado do módulo de texto** é **criado**, e o modo de **vocabulário** do módulo que se conecta ao módulo **modelo de Pontuação** é **ReadOnly**.

Depois de enviar o pipeline de treinamento acima com êxito, você pode registrar a saída do módulo circulado como conjunto de registros.

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="registrar conjunto de registros" border="true":::

Em seguida, você pode criar um pipeline de inferência em tempo real. Depois de criar o pipeline de inferência, você precisa ajustar seu pipeline de inferência manualmente da seguinte maneira:

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="pipeline de inferência" border="true":::

Em seguida, envie o pipeline de inferência e implante um ponto de extremidade em tempo real.

## <a name="results"></a>Resultados

O módulo extrair recursos N-Gram do texto cria dois tipos de saída: 

* **Conjunto de resultados**: essa saída é um resumo do texto analisado combinado com os n-gramas que foram extraídos. As colunas que você não selecionou na opção **coluna de texto** são passadas para a saída. Para cada coluna de texto que você analisa, o módulo gera estas colunas:

  * **Matriz de ocorrências de n-Gram**: o módulo gera uma coluna para cada n-Gram encontrado no total de Corpus e adiciona uma pontuação em cada coluna para indicar o peso do n-grama para essa linha. 

* **Vocabulário de resultado**: o vocabulário contém o dicionário de n-Gram real, junto com as pontuações de frequência de termos que são geradas como parte da análise. Você pode salvar o conjunto de informações para reutilização com um conjunto diferente de entradas ou para uma atualização posterior. Você também pode reutilizar o vocabulário para modelagem e pontuação.

### <a name="result-vocabulary"></a>Vocabulário de resultado

O vocabulário contém o dicionário de n-Gram com as pontuações de frequência de termos que são geradas como parte da análise. As pontuações de DF e IDF são geradas independentemente de outras opções.

+ **ID**: um identificador gerado para cada n-Gram exclusivo.
+ **NGram**: o n-Gram. Espaços ou outros separadores de palavras são substituídos pelo caractere de sublinhado.
+ **DF**: a pontuação de frequência do termo para o n-Gram no Corpus original.
+ **IDF**: a pontuação de frequência de documento inversa para o n-Gram no Corpus original.

Você pode atualizar manualmente esse conjunto de um, mas pode introduzir erros. Por exemplo:

* Um erro será gerado se o módulo encontrar linhas duplicadas com a mesma chave no vocabulário de entrada. Certifique-se de que duas linhas no vocabulário tenham a mesma palavra.
* O esquema de entrada dos conjuntos de dados de vocabulário deve corresponder exatamente, incluindo nomes de coluna e tipos de coluna. 
* A coluna de **ID** e a coluna **DF** devem ser do tipo inteiro. 
* A coluna **IDF** deve ser do tipo float.

> [!Note]
> Não conecte a saída de dados ao módulo treinar modelo diretamente. Você deve remover as colunas de texto livre antes que elas sejam inseridas no modelo de treinamento. Caso contrário, as colunas de texto livre serão tratadas como recursos categóricos.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.
