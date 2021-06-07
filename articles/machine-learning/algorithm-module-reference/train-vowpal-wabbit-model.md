---
title: Treinar o modelo Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de Wabbit de treinamento do Vowpal para criar um modelo de aprendizado de máquina usando uma instância do Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657647"
---
# <a name="train-vowpal-wabbit-model"></a>Treinar o modelo Vowpal Wabbit
Este artigo descreve como usar o módulo **modelo de Wabbit de treinamento do Vowpal** no designer de Azure Machine Learning para criar um modelo de aprendizado de máquina usando Vowpal Wabbit.  

Para usar o Vowpal Wabbit para aprendizado de máquina, formate sua entrada de acordo com os requisitos do Vowpal wabbit e prepare os dados no formato necessário. Use este módulo para especificar argumentos de linha de comando Vowpal Wabbit. 

Quando o pipeline é executado, uma instância de Vowpal Wabbit é carregada no tempo de execução do experimento, junto com os dados especificados. Quando o treinamento for concluído, o modelo será serializado de volta para o espaço de trabalho. Você pode usar o modelo imediatamente para pontuar os dados. 

Para treinar de forma incremental um modelo existente em novos dados, conecte um modelo salvo à porta de entrada do **modelo Vowpal Wabbit pré-treinado** do **modelo Train Vowpal Wabbit** e adicione os novos dados à outra porta de entrada.  

## <a name="what-is-vowpal-wabbit"></a>O que é Vowpal Wabbit?  

O VW (Vowpal Wabbit) é uma estrutura de aprendizado de máquina rápida e paralela que foi desenvolvida para a computação distribuída pelo Yahoo! Pesquisar. Posteriormente, foi transportado para o Windows e adaptado por John Langford (Microsoft Research) para computação científica nas arquiteturas paralelas.  

Os recursos do Vowpal Wabbit que são importantes para o aprendizado de máquina incluem aprendizado contínuo (aprendizado online), redução de dimensionalidade e aprendizado interativo. Vowpal Wabbit também é uma solução de problemas quando você não pode ajustar os dados do modelo na memória.  

Os principais usuários do Vowpal Wabbit são cientistas de dados que usaram anteriormente a estrutura para tarefas de aprendizado de máquina, como classificação, regressão, modelagem de tópico ou fatoração de matriz. O wrapper do Azure para Vowpal Wabbit tem características de desempenho muito semelhantes para a versão local, para que você possa usar os recursos avançados e o desempenho nativo do Vowpal wabbit e publicar facilmente o modelo treinado como um serviço operacional.  

O módulo [hash de recurso](feature-hashing.md) também inclui a funcionalidade fornecida por Vowpal Wabbit, que permite transformar conjuntos de linhas de texto em recursos binários usando um algoritmo de hash.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Como configurar o modelo Vowpal Wabbit  

Esta seção descreve como treinar um novo modelo e como adicionar novos dados a um modelo existente.

Ao contrário de outros módulos no designer, esse módulo Especifica os parâmetros do módulo e treina o modelo. Se você tiver um modelo existente, poderá adicioná-lo como uma entrada opcional para treinar o modelo de forma incremental.

+ [Preparar dados de entrada em um dos formatos necessários](#prepare-the-input-data)
+ [Treinar um novo modelo](#create-and-train-a-vowpal-wabbit-model)
+ [Treinar um modelo existente de forma incremental](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Para treinar um modelo usando esse módulo, o conjunto de dados de entrada deve consistir em uma única coluna de texto em um dos dois formatos com suporte: **SVMLight** ou **VW**. Isso não significa que Vowpal Wabbit analisa apenas dados de texto, apenas que os recursos e os valores devem ser preparados no formato de arquivo de texto necessário.  

Os dados podem ser lidos de dois tipos de DataSets, conjunto de dados de arquivo ou conjunto de dados de tabela. Ambos os conjuntos de valores devem estar no formato SVMLight ou VW. O formato de dados Vowpal Wabbit tem a vantagem de não requerer um formato de coluna, o que economiza espaço ao lidar com dados esparsos. Para obter mais informações sobre esse formato, consulte a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Criar e treinar um modelo Vowpal Wabbit

1. Adicione o módulo **modelo de Wabbit de treinamento Vowpal** ao seu experimento. 
  
2. Adicione o conjunto de dados de treinamento e conecte-o aos **dados de treinamento**. Se o conjunto de dados de treinamento for um diretório, que contém o arquivo de dado de treinamento, especifique o nome do arquivo de dados de treinamento com o **nome do arquivo de dados de treinamento**. Se o conjunto de dados de treinamento for um único arquivo, deixe **o nome do arquivo de dado de treinamento** como vazio.

3. Na caixa de texto **argumentos de VW** , digite os argumentos de linha de comando para o executável Vowpal Wabbit.

     Por exemplo, você pode adicionar *`–l`* para especificar a taxa de aprendizagem ou *`-b`* para indicar o número de bits de hash.  

     Para obter mais informações, consulte a seção [Vowpal Wabbit Parameters](#supported-and-unsupported-parameters) .  

4. **Nome do arquivo de dados de treinamento**: digite o nome do arquivo que contém os dados de entrada. Esse argumento só é usado quando o conjunto de e de treinamento é um diretório.

5. **Especificar tipo de arquivo**: indique qual formato usa seus dados de treinamento. Vowpal Wabbit dá suporte a esses dois formatos de arquivo de entrada:  

    - **VW** representa o formato interno usado por Vowpal Wabbit. Consulte a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) para obter detalhes. 
    - **SVMLight** é um formato usado por outras ferramentas de aprendizado de máquina. 

6. **Arquivo de modelo legível de saída**: selecione a opção se desejar que o módulo salve o modelo legível nos registros de execução. Esse argumento corresponde ao `--readable_model` parâmetro na linha de comando VW.  

7. **Arquivo de hash invertido de saída**: selecione a opção se desejar que o módulo salve a função de hash invertida em um arquivo nos registros de execução. Esse argumento corresponde ao `--invert_hash` parâmetro na linha de comando VW.  

8. Envie o pipeline.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Treinar novamente um modelo existente do Vowpal Wabbit

O Vowpal Wabbit dá suporte ao treinamento incremental adicionando novos dados a um modelo existente. Há duas maneiras de obter um modelo existente para treinar novamente:

+ Use a saída de outro módulo de **modelo Vowpal Wabbit de treinamento** no mesmo pipeline.  
  
+ Localize um modelo salvo na categoria **conjuntos de valores** do painel de navegação esquerdo do designer e arraste-o para o pipeline.  

1. Adicione o módulo **modelo de Wabbit de treinamento Vowpal** ao seu pipeline.  
2. Conecte o modelo treinado anteriormente à porta de entrada do **modelo Vowpal Wabbit pré-treinado** do módulo.
3. Conecte os novos dados de treinamento à porta de entrada de **dados de treinamento** do módulo.
4. No painel parâmetros do **modelo Train Vowpal Wabbit**, especifique o formato dos novos dados de treinamento e também o nome do arquivo de dados de treinamento se o conjunto de dado de entrada for um diretório.
5. Selecione as opções **arquivo de modelo legível** e **saída de arquivo de hash invertido** se os arquivos correspondentes precisarem ser salvos nos registros de execução.

6. Envie o pipeline.  
7. Selecione o módulo e selecione **registrar conjunto de registros** na guia **saídas + logs** no painel direito para preservar o modelo atualizado em seu espaço de trabalho do Azure Machine Learning.  Se você não especificar um novo nome, o modelo atualizado substituirá o modelo salvo existente.

## <a name="results"></a>Resultados

+ Para gerar pontuações do modelo, use [pontuar Vowpal Wabbit Model](score-vowpal-wabbit-model.md).

> [!NOTE]
> Se você precisar implantar o modelo treinado no designer, verifique se o modelo de [Pontuação Vowpal Wabbit](score-vowpal-wabbit-model.md) em vez do **modelo de Pontuação** está conectado à entrada do [módulo saída do serviço Web](web-service-input-output.md) no pipeline de inferência.

## <a name="technical-notes"></a>Observações técnicas

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="advantages-of-vowpal-wabbit"></a>Vantagens do Vowpal Wabbit

O Vowpal Wabbit fornece aprendizado extremamente rápido sobre recursos não lineares como n-gramas.  

O Vowpal Wabbit usa técnicas de *aprendizado online*, como stochastic gradient descent (SGD) para ajustar um modelo em um registro de cada vez. Assim, ele itera rapidamente sobre dados brutos e pode desenvolver um bom indicador com mais rapidez do que a maioria dos outros modelos. Essa abordagem também evita a necessidade de ler todos os dados de treinamento na memória.  

O Vowpal Wabbit converte todos os dados em hash, não apenas os dados de texto, mas outras variáveis categóricas. O uso de hashes torna a pesquisa de pesos de regressão mais eficiente, o que é crítico para descendente de gradiente estocástico em vigor.  

###  <a name="supported-and-unsupported-parameters"></a>Parâmetros com e sem suporte 

Esta seção descreve o suporte para parâmetros de linha de comando do Vowpal Wabbit no designer de Azure Machine Learning. 

Em geral, há suporte para todos, exceto para um conjunto limitado de argumentos. Para obter uma lista completa de argumentos, use a [página wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

Não há suporte para os seguintes parâmetros:

-   As opções de entrada/saída especificadas em [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Essas propriedades já estão configuradas automaticamente pelo módulo.  
  
-   Além disso, qualquer opção que gera várias saídas ou usa várias entradas não é permitida. Isso inclui *`--cbt`* , *`--lda`* e *`--wap`* .  
  
-   Há suporte apenas para algoritmos de aprendizado supervisionados. Portanto, não há suporte para essas opções: *`–active`* , `--rank` , *`--search`* etc. 

### <a name="restrictions"></a>Restrições

Como o objetivo do serviço é dar suporte a usuários experientes do Vowpal Wabbit, os dados de entrada devem ser preparados antecipadamente usando o formato de texto nativo Vowpal Wabbit, em vez do formato de conjunto de dados usado por outros módulos.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
