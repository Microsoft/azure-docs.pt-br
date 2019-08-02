---
title: Solucionar problemas de erros de módulo
titleSuffix: Azure Machine Learning service
description: Solucionar problemas de exceções de módulo no Azure Machine Learning Studio usando códigos de erro
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5ce3d86d37940f66065debce30b348371b7aa6af
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568239"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Exceções e códigos de erro para referência de módulo e algoritmos

Saiba mais sobre as mensagens de erro e códigos de exceção que podem ser encontrados usando módulos no Azure Machine Learning Studio. 

Para resolver o problema, procure o erro neste artigo para saber mais sobre as causas comuns. Há duas maneiras de obter o texto completo de uma mensagem de erro no Studio:  
 
- Clique no link **Exibir Log de saída**, no painel direito e role até a parte inferior. A mensagem de erro detalhada é exibida nas duas últimas linhas da janela.  
  
- Selecione o módulo que contém o erro e clique no X vermelho. Somente o texto de erro pertinentes é exibido.  
  
Se o texto da mensagem de erro não é útil, envie-nos informações sobre o contexto e quaisquer desejadas adições ou alterações. Você pode enviar comentários sobre o tópico de erro, ou visite o [Fórum do Azure Machine Learning STUDIO](https://aka.ms/aml-forum-studio) e poste uma pergunta.  


## <a name="error-0001"></a>Erro 0001  
 Ocorrerá uma exceção se uma ou mais colunas do conjunto de dados especificadas não pôde ser encontradas.  
  
 Você receberá esse erro se a seleção de coluna é feita para um módulo, mas as colunas selecionadas não existem no conjunto de dados de entrada. Esse erro pode ocorrer se você tiver digitado manualmente em um nome de coluna ou se o seletor de coluna tiver fornecido uma coluna sugerida que não existia no conjunto de dados quando você executou o teste.  
  
**Resolução:** Reveja o módulo que gere esta exceção e validar se o nome da coluna ou nomes estão corretos e se todas as colunas referenciadas existem.  
  
|Mensagens de Exceção|  
|------------------------|  
|Uma ou mais colunas especificadas não foram encontradas.|  
|A coluna com o nome ou índice "{0}" não encontrado|  
|A coluna com o nome ou índice "{0}"não existe no"{1}"|  
 

## <a name="error-0002"></a>Erro 0002  
 Ocorrerá uma exceção se um ou mais parâmetros não pôde ser analisado ou convertido do tipo especificado em exigido pelo tipo de método de destino.  
  
 Esse erro ocorre no Azure Machine Learning, quando você especificar um parâmetro como entrada e o tipo de valor é diferente do tipo esperado e a conversão implícita não pode ser executada.  
  
**Resolução:** Verifique os requisitos de módulo e determinar qual tipo de valor é necessário (cadeia de caracteres, inteiro, duplo, etc.)  
  
|Mensagens de Exceção|  
|------------------------|  
|Falha ao analisar o parâmetro|  
|Falha ao analisar "{0}" parâmetro|  
|Falha ao analisar (converter) "{0}"do parâmetro para"{1}"|  
|Falha ao converter "{0}"parâmetro de"{1}"para"{2}"|  
|Falha ao converter "{0}"valor de parâmetro"{1}"de"{2}"para"{3}"|  
|Falha ao converter o valor "{0}"na coluna"{1}"de"{2}"para"{3}"com o uso do formato"{4}" fornecido|  
  

## <a name="error-0003"></a>Erro 0003  
 Ocorrerá uma exceção se uma ou mais das entradas for nula ou vazia.  
  
 Você receberá esse erro no Azure Machine Learning se quaisquer entradas ou parâmetros para um módulo são nulos ou vazios.  Esse erro pode ocorrer, por exemplo, quando você não digitou em qualquer valor para um parâmetro. Isso também pode acontecer se você escolher um conjunto de dados que tem valores ausentes, ou um conjunto de dados vazio.  
  
**Resolução:**
 
+ Abra o módulo que produziu a exceção e verifique se todas as entradas foram especificadas. Garanta que todas as entradas forem especificadas. 
+ Certifique-se de que os dados que são carregados do armazenamento do Azure estão acessíveis e se o nome da conta ou a chave não foi alterado.  
+ Verifique os dados de entrada para valores ausentes ou nulos.
+ Se usar uma consulta em uma fonte de dados, verifique se que dados estão sendo retornados no formato esperado. 
+ Verifique se há erros de digitação ou outras alterações na especificação de dados.
  
|Mensagens de Exceção|  
|------------------------|  
|Uma ou mais das entradas são nulos ou vazios|  
|Entrada "{0}" é nulo ou vazio|  
  

## <a name="error-0004"></a>Erro 0004  
 Ocorrerá uma exceção se o parâmetro é menor que ou igual ao valor específico.  
  
 Você receberá esse erro no Azure Machine Learning, se o parâmetro na mensagem estiver abaixo de um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Reveja o módulo de lançar a exceção e modifique o parâmetro para ser maior que o valor especificado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Parâmetro deve ser maior que o valor de limite.|  
|Parâmetro "{0}" valor deve ser maior que {1}.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser maior que {2}|  
  


## <a name="error-0005"></a>Erro 0005  
 Ocorrerá uma exceção se o parâmetro é menor que um valor específico.  
  
 Você receber esse erro no Azure Machine Learning, se o parâmetro na mensagem está abaixo ou igual a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Rever o módulo de lançar a exceção e modifique o parâmetro para ser maior ou igual ao valor especificado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Parâmetro deve ser maior que ou igual ao valor de limite.|  
|Parâmetro "{0}" valor deve ser maior que ou igual a {1}.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser maior que ou igual a {2}.|  
  

## <a name="error-0006"></a>Erro 0006  
 Ocorrerá uma exceção se o parâmetro é maior que ou igual ao valor especificado.  
  
 Você receberá esse erro no Azure Machine Learning, se o parâmetro na mensagem for maior que ou igual a um valor de limite necessário para o módulo processar os dados.  
  
**Resolução:** Reveja o módulo de lançar a exceção e modifique o parâmetro para ser menor do que o valor especificado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser menor que outro.|  
|Parâmetro "{0}"o valor deve ser menor que o parâmetro"{1}" valor.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser menor que {2}.|  
  

## <a name="error-0007"></a>Erro 0007  
 Ocorrerá uma exceção se o parâmetro for maior que um valor específico.  
  
 Você receberá esse erro no Azure Machine Learning se, nas propriedades do módulo, você especificou um valor que é maior que o permitido. Por exemplo, você pode especificar uma data que está fora do intervalo de datas com suporte, ou você pode indicar que cinco colunas usada quando apenas três colunas estão disponíveis. 
 
 Você também poderá ver esse erro se você estiver especificando dois conjuntos de dados que precisam ser correspondentes de alguma forma. Por exemplo, se você está renomeando colunas e especifica as colunas por índice, o número de nomes que você fornece deve corresponder ao número de índices de coluna. Outro exemplo pode ser uma operação matemática que usa duas colunas, onde as colunas devem ter o mesmo número de linhas. 
  
**Resolução:**
 
 + Abra o módulo em questão e examine as configurações de propriedade numérica.
 + Certifique-se de que quaisquer valores de parâmetro caiam dentro do intervalo de valores com suporte para essa propriedade.
 + Se o módulo recebe várias entradas, certifique-se de que as entradas são do mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se o conjunto de dados ou fonte de dados foi alterado. Às vezes, um valor que funcionava com uma versão anterior dos dados falharão depois que o número de colunas, os tipos de dados de coluna ou o tamanho dos dados foi alterado.  
  
|Mensagens de exceção|  
|------------------------|  
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser menor ou igual a outro.|  
|Parâmetro "{0}"o valor deve ser menor ou igual ao parâmetro"{1}" valor.|  
|Parâmetro "{0}"tem o valor"{1}" que deve ser menor ou igual a {2}.|  
  

## <a name="error-0008"></a>Erro 0008  
 Ocorrerá uma exceção se o parâmetro não está no intervalo.  
  
 Você receberá esse erro no Azure Machine Learning se o parâmetro na mensagem está fora dos limites necessários para o módulo processar os dados.  
  
 Por exemplo, esse erro é exibido se você tentar usar [adicionar linhas](add-rows.md) para combinar dois conjuntos de dados que têm um número diferente de colunas.  
  
**Resolução:** Rever o módulo de lançar a exceção e modifique o parâmetro para ser dentro do intervalo especificado.  
  
|Mensagens de Exceção|  
|------------------------|  
|O valor do parâmetro não está no intervalo especificado.|  
|Parâmetro "{0}" valor não está no intervalo.|  
|Parâmetro "{0}" o valor deve estar no intervalo de [{1}, {2}].|  
  

## <a name="error-0009"></a>Erro 0009  
 Ocorrerá uma exceção quando o nome da conta de armazenamento do Azure ou o nome do contêiner for especificado incorretamente.  
  
Esse erro ocorre no Azure Machine Learning Studio quando você especificar parâmetros para uma conta de armazenamento do Azure, mas o nome ou a senha não pode ser resolvida. Erros em senhas ou nomes de conta podem ocorrer por vários motivos:
 
 + A conta é do tipo incorreto. Não há suporte para alguns novos tipos de conta para uso com o Machine Learning Studio. Ver [importação de dados](import-data.md) para obter detalhes.
 + Você inseriu o nome de conta incorreta
 + A conta não existe mais
 + A senha da conta de armazenamento está errada ou foi alterada
 + Você não especificou o nome do contêiner ou o contêiner não existe
 + Você não especificar completamente o caminho do arquivo (caminho para o blob)
   
**Resolução:**

Esses problemas geralmente ocorrem quando você tentar inserir manualmente o caminho de contêiner, senha ou nome de conta. É recomendável que você use o Assistente de novo para o [importação de dados](import-data.md) módulo, que ajuda você a pesquisar e verificar nomes.

Além disso, verifique se a conta, contêiner ou blob foi excluído. Use outro utilitário de armazenamento do Azure para verificar se o nome da conta e a senha tem sido inseridos corretamente e se o contêiner existe. 

Não há suporte para alguns tipos de conta mais recentes pelo Azure Machine Learning. Por exemplo, o novo "quente" ou tipos de armazenamento "frio" não podem ser usados para o aprendizado de máquina. Contas de armazenamento clássicas e contas de armazenamento criadas como "Finalidade geral" funciona bem.

Se o caminho completo para um blob tiver sido especificado, verifique se o caminho é especificado como **blobname/contêiner**, e que o contêiner e o blob existem na conta.  
  
 O caminho não deve conter uma barra à esquerda. Por exemplo **/contêiner/blob** está incorreto e deve ser inserido como **contêiner/blob**.  

  
|Mensagens de Exceção|  
|------------------------|  
|O nome da conta de armazenamento do Azure ou o nome do contêiner está incorreto.|  
|O nome da conta de armazenamento do Azure "{0}"ou nome do contêiner"{1}" está incorreto; era esperado um nome de contêiner do formato contêiner/blob.|  
  

## <a name="error-0010"></a>Erro 0010  
 Ocorrerá uma exceção se a conjuntos de dados de entrada tiver nomes de colunas que deveriam corresponder, mas não o fazem.  
  
 Você receberá esse erro no Azure Machine Learning, se o índice da coluna na mensagem tiver nomes de colunas diferentes em dois conjuntos de dados de entrada.  
  
**Resolução:** Use [editar metadados](edit-metadata.md) ou modificar o conjunto de dados original para ter o mesmo nome de coluna para o índice da coluna especificada.  
  
|Mensagens de Exceção|  
|------------------------|  
|As colunas com índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|  
|Nomes de coluna não são os mesmos para a coluna {0} (com base em zero) de conjuntos de dados de entrada ({1} e {2} , respectivamente).|  
  

## <a name="error-0011"></a>Erro 0011  
 Ocorrerá uma exceção se passado argumento de conjunto de coluna não se aplica a qualquer uma das colunas do conjunto de dados.  
  
 Você receberá esse erro no Azure Machine Learning se a seleção de coluna especificada não corresponde a nenhuma das colunas no conjunto de dados determinado.  
  
 Você também pode obter esse erro se você não selecionou uma coluna e pelo menos uma coluna é necessária para o módulo funcione.  
  
**Resolução:** Modifique a seleção de colunas no módulo para que ela será aplicada às colunas no conjunto de dados.  
  
 Se o módulo requer que você selecione uma coluna específica, como uma coluna de rótulo, verifique se que a coluna à direita está selecionada.  
  
 Se inadequado colunas forem selecionadas, removê-los e execute novamente o teste.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de colunas especificado não se aplica a qualquer uma das colunas do conjunto de dados.|  
|Conjunto de colunas especificado "{0}" não se aplica a qualquer uma das colunas do conjunto de dados.|  
  

## <a name="error-0012"></a>Erro 0012  
 Ocorrerá uma exceção se a instância da classe não pôde ser criada com o conjunto de argumentos passado.  
  
**Resolução:** Esse erro não é acionável pelo usuário e será preterido em uma versão futura.  
  
|Mensagens de Exceção|  
|------------------------|  
|Sem treinamento do modelo, treinar o modelo pela primeira vez.|  
|Modelo não treinado ({0}), use o modelo treinado.|  
  

## <a name="error-0013"></a>Erro 0013  
 Ocorrerá uma exceção se o aprendiz passado para o módulo é um tipo inválido.  
  
 Esse erro ocorre sempre que um modelo treinado é incompatível com o módulo de pontuação conectado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Resolução:**

Determine o tipo de aprendiz que é produzido pelo módulo de treinamento e determine o módulo de pontuação que é apropriado para o aprendiz. 

Se o modelo foi treinado usando qualquer um dos módulos de treinamento especializado, conecte-se o modelo treinado somente para o módulo de pontuação correspondente especializado. 


|Tipo de modelo|Módulo de treinamento| Módulo de pontuação|
|----|----|----|
|qualquer classificação|[Modelo de treinamento](train-model.md) |[Modelo de pontuação](score-model.md)|
|qualquer modelo de regressão|[Modelo de treinamento](train-model.md) |[Modelo de pontuação](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->
  
|Mensagens de Exceção|  
|------------------------|  
|Aprendiz de tipo inválido é passado.|  
|Aprendiz "{0}" tem tipo inválido.|  


## <a name="error-0014"></a>Erro 0014  
 Ocorrerá uma exceção se a contagem exclusiva de valores de coluna for maior que o permitido.  
  
 Esse erro ocorre quando uma coluna contiver muitos valores exclusivos.  Por exemplo, você poderá ver esse erro se você especificar que uma coluna ser tratadas como dados categóricos, mas há muitos valores exclusivos na coluna para permitir que o processamento concluir. Você também poderá ver esse erro se não houver uma incompatibilidade entre o número de valores exclusivos de duas entradas.   
  
**Resolução:**

Abra o módulo que gerou o erro e identificar as colunas usadas como entradas. Para alguns módulos, pode clicar duas vezes o conjunto de dados de entrada e selecione **visualizar** obter estatísticas em colunas individuais, incluindo o número de valores exclusivos e sua distribuição.

Para colunas que você pretende usar para agrupamento ou categorização, tome medidas para reduzir o número de valores exclusivos nas colunas. Você pode reduzir de maneiras diferentes, dependendo do tipo de dados da coluna. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Não é possível encontrar uma resolução que corresponde ao seu cenário? Você pode fornecer comentários sobre este tópico que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer mais direcionada etapas para cenários comuns de solução de problemas.   
  
|Mensagens de Exceção|  
|------------------------|  
|Número de valores exclusivos da coluna é maior que o permitido.|  
|Número de valores exclusivos na coluna: "{0}" excede a contagem de tupla de {1}.|  
  

## <a name="error-0015"></a>Erro 0015  
 Ocorrerá uma exceção se a conexão de banco de dados tiver falhado.  
  
 Você receberá esse erro se você inserir um nome de conta SQL incorreto, senha, o servidor de banco de dados ou nome de banco de dados, ou se uma conexão com o banco de dados não puder ser estabelecida devido a problemas com o banco de dados ou o servidor.  
  
**Resolução:** Verifique se o nome da conta, senha, o servidor de banco de dados e banco de dados foram inseridos corretamente e se a conta especificada tem o nível correto de permissões. Verifique se o banco de dados está acessível no momento.  
  
|Mensagens de Exceção|  
|------------------------|  
|Erro ao estabelecer conexão de banco de dados.|  
|Erro ao estabelecer conexão de banco de dados: {0}.|  
  


## <a name="error-0016"></a>Erro 0016  
 Ocorrerá uma exceção se os conjuntos de dados de entrada passados para o módulo devessem ter tipos de coluna compatíveis, mas não fizer isso.  
  
 Você receberá esse erro no Azure Machine Learning se os tipos de colunas passados em dois ou mais conjuntos de dados não são compatíveis entre si.  
  
**Resolução:** Use [editar metadados](edit-metadata.md) ou modificar o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para garantir que os tipos das colunas são compatíveis.  
  
|Mensagens de Exceção|  
|------------------------|  
|Colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|  
|Colunas {0} e {1} são incompatíveis.|  
|Tipos de elemento de coluna não são compatíveis para a coluna {0} (com base em zero) de conjuntos de dados de entrada ({1} e {2} , respectivamente).|  
  

## <a name="error-0017"></a>Erro 0017  
 Ocorrerá uma exceção se uma coluna selecionada usa um tipo de dados que não é suportado pelo módulo atual.  
  
 Por exemplo, você poderá receber esse erro no Azure Machine Learning se a seleção da coluna inclui uma coluna com um tipo de dados que não pode ser processado pelo módulo, como uma coluna de cadeia de caracteres para uma operação matemática ou uma coluna de classificação em que é uma coluna de recursos categóricos Necessário.  
  
**Resolução:**
 1. Identificar a coluna que é o problema.
 2. Examine os requisitos do módulo.
 3. Modificar a coluna para torná-lo a atender aos requisitos. Talvez você precise usar vários dos módulos a seguir para fazer alterações, dependendo da coluna e a conversão que você está tentando executar:
    + Use [editar metadados](edit-metadata.md) para alterar o tipo de dados das colunas, ou para alterar o uso da coluna de recurso numérico, categórico para não-categóricos, e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, talvez você precise modificar o conjunto de dados de entrada original.

> [!TIP]
> Não é possível encontrar uma resolução que corresponde ao seu cenário? Você pode fornecer comentários sobre este tópico que inclui o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer mais direcionada etapas para cenários comuns de solução de problemas. 
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível processar a coluna do tipo atual. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna do tipo {0}. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna "{1}" do tipo {0}. O tipo não é suportado pelo módulo.|  
|Não é possível processar a coluna "{1}" do tipo {0}. O tipo não é suportado pelo módulo. Nome do parâmetro: {2}|  
  

## <a name="error-0018"></a>Erro 0018  
 Ocorrerá uma exceção se o conjunto de dados de entrada não é válido.  
  
**Resolução:** Esse erro no Azure Machine Learning pode aparecer em vários contextos, portanto, não há não uma única solução. Em geral, o erro indica que os dados fornecidos como entrada para um módulo tem um número incorreto de colunas, ou que o tipo de dados não coincide com os requisitos do módulo. Por exemplo:  
  
-   O módulo requer uma coluna de rótulo, mas nenhuma coluna está marcada como um rótulo, ou você não selecionou uma coluna de rótulo ainda.  
  
-   O módulo requer que dados ser categórica, mas os dados forem numéricos.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Os dados são no formato incorreto.  
  
-   Dados importados contém caracteres inválidos, valores inválidos, ou valores fora do intervalo.  
-   A coluna está vazia ou contém muitos valores ausentes.  
  
 Para determinar os requisitos e como seus dados talvez, examine o tópico da Ajuda para o módulo que consumirão o conjunto de dados como entrada.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de dados não é válido.|  
|{0} contém dados inválidos.|  
|{0} e {1} deve ser prudente coluna consistente.|  
  

## <a name="error-0019"></a>Erro 0019  
 Ocorrerá uma exceção se a coluna deve conter valores classificados, mas isso não acontecer.  
  
 Você receberá esse erro no Azure Machine Learning se os valores de coluna especificados estão fora de ordem.  
  
**Resolução:** Os valores da coluna de classificação modificando manualmente o conjunto de dados de entrada e execute novamente o módulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Valores de coluna não são classificados.|  
|Valores na coluna "{0}" não são classificados.|  
|Valores na coluna "{0}"do conjunto de dados"{1}" não são classificados.|  
  

## <a name="error-0020"></a>Erro 0020  
 Ocorrerá uma exceção se o número de colunas em alguns dos conjuntos de dados passados para o módulo for muito pequeno.  
  
 Você receberá esse erro no Azure Machine Learning se não há colunas suficientes foram selecionadas para um módulo.  
  
**Resolução:** Rever o módulo e verifique se que esse seletor de coluna tem o número correto de colunas selecionadas.  
  
|Mensagens de Exceção|  
|------------------------|  
|Número de colunas no conjunto de dados de entrada é menor que o mínimo permitido.|  
|Número de colunas no conjunto de dados de entrada é menor que o mínimo permitido de {0} coluna (s).|  
|Número de colunas no conjunto de dados de entrada "{0}" é menor que o mínimo permitido de {1} coluna (s).|

## <a name="error-0021"></a>Erro 0021  
 Ocorrerá uma exceção se o número de linhas em alguns dos conjuntos de dados passados para o módulo for muito pequeno.  
  
 Esse erro no visto no Azure Machine Learning quando há não há linhas no conjunto de dados para executar a operação especificada. Por exemplo, você poderá ver esse erro se o conjunto de dados de entrada está vazio ou se você está tentando executar uma operação que exige algum número mínimo de linhas seja válido. Essas operações podem incluir (mas não estão limitadas a) agrupamento ou classificação com base em métodos estatísticos, determinados tipos de agrupamento e aprendizado com contagens.  
  
**Resolução:**
 
 + Abra o módulo que retornou o erro e verifique as propriedades de conjunto de dados e o módulo de entrada. 
 + Verifique se que o conjunto de dados de entrada não está vazio e que há suficiente linhas de dados para atender aos requisitos descritos na Ajuda do módulo.  
 + Se seus dados são carregados de uma fonte externa, certifique-se de que a fonte de dados está disponível e que não há nenhum erro ou na definição de dados que faria com que o processo de importação obter menos linhas.
 + Se você estiver executando uma operação no upstream dos dados do módulo que pode afetar o tipo de dados ou o número de valores, como limpeza, a divisão, ou operações de junção, verifique as saídas dessas operações para determinar o número de linhas retornadas.  



## <a name="error-0022"></a>Erro 0022  
 Ocorrerá uma exceção se o número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.  
  
 Esse erro no Azure Machine Learning pode ocorrer quando o módulo downstream ou a operação requer um número específico de colunas ou entradas, e você forneceu muitas ou poucas entradas ou colunas. Por exemplo:  
  
-   Você especifica uma coluna de rótulo único ou a coluna de chave e selecionou acidentalmente várias colunas.  
  
-   Você está renomeando colunas, mas fornecidos nomes mais ou menos que as colunas.  
  
-   O número de colunas na fonte ou destino foi alterado ou não corresponde ao número de colunas usados pelo módulo.  
  
-   Você forneceu uma lista separada por vírgulas de valores para as entradas, mas o número de valores não corresponde ou não há suporte para várias entradas.  
  
**Resolução:** Rever o módulo e verifique a seleção de colunas para garantir que o número de colunas correto está selecionado. Verifique se as saídas dos módulos de upstream e os requisitos das operações de downstream.  
  
 Se você usou uma das opções de seleção de coluna que podem selecionar várias colunas (índices de colunas, todos os recursos, todos numéricos, etc.), valide o número exato de colunas retornadas pela seleção.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Verifique se que o número ou tipo de colunas upstream não foi alterado.  
  
 Se você estiver usando um conjunto de dados de recomendação para treinar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, correspondente a classificações de item de usuário ou de pares item-usuário. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de dados de recomendação. Para obter mais informações, consulte [dividir dados](split-data.md).  
  
|Mensagens de Exceção|  
|------------------------|  
|Número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|  
|Número de colunas selecionadas no conjunto de dados de entrada não é igual a {0}.|  
|Padrão de seleção de coluna "{0}" fornece o número de colunas selecionadas no conjunto de dados de entrada não é igual a {1}.|  
|Padrão de seleção de coluna "{0}" é esperado para fornecer {1} coluna (s) selecionado no conjunto de dados de entrada, mas {2} coluna (s) é/é fornecido.|  



## <a name="error-0023"></a>Erro 0023  
 Ocorrerá uma exceção se a coluna de destino do conjunto de dados de entrada não é válida para o módulo atual do instrutor.  
  
 Esse erro no Azure Machine Learning ocorre se a coluna de destino (conforme selecionado nos parâmetros do módulo) não é do tipo de dados válido, continha todos os valores ausentes ou não estava categórica conforme o esperado.  
  
**Resolução:** Examine o módulo de entrada para inspecionar o conteúdo da coluna de rótulo/destino. Verifique se que ele não tem todos os valores ausentes. Se o módulo está esperando a coluna de destino seja categórica, certifique-se de que há mais de um valores distintos na coluna de destino.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de dados de entrada tem coluna de destino sem suporte.|  
|Conjunto de dados de entrada tem a coluna de destino sem suporte "{0}".|  
|Conjunto de dados de entrada tem a coluna de destino sem suporte "{0}" para o Aprendiz do tipo {1}.|  
 

## <a name="error-0024"></a>Erro 0024  
Ocorrerá uma exceção se o conjunto de dados não contém uma coluna de rótulo.  

 Esse erro no Azure Machine Learning ocorre quando o módulo requer uma coluna de rótulo e o conjunto de dados não tem uma coluna de rótulo. Por exemplo, avaliação de um conjunto de dados pontuado normalmente requer que uma coluna de rótulo está presente para calcular métricas de precisão.  
 
Isso também pode acontecer que uma coluna de rótulo está presente no conjunto de dados, mas não detectados corretamente pelo Azure Machine Learning.
  
**Resolução:**

+ Abra o módulo que gerou o erro e determinar se uma coluna de rótulo está presente. O nome ou tipo de dados da coluna não importa, desde que a coluna contém um único resultado (ou variável dependente) que você está tentando prever. Se você não tiver certeza de qual coluna tem o rótulo, procure um nome genérico, como *classe* ou *destino*. 
+  Se o conjunto de dados não inclui uma coluna de rótulo, é possível que a coluna de rótulo foi acidentalmente ou explicitamente removida upstream. Também é possível que o conjunto de dados não é a saída de um módulo de pontuação de upstream.
+ Para marcar explicitamente a coluna como a coluna de rótulo, adicione a [editar metadados](edit-metadata.md) módulo e conecte-se o conjunto de dados. Selecione somente a coluna de rótulo e, em seguida, selecione **etiqueta** da **campos** lista suspensa. 
+ Se a coluna incorreta for escolhida como o rótulo, você pode selecionar **limpar rótulo** da **campos** para corrigir os metadados na coluna. 
  
|Mensagens de Exceção|  
|------------------------|  
|Não há nenhuma coluna de rótulo no conjunto de dados.|  
|Não há nenhuma coluna de rótulo em "{0}".|  
  

## <a name="error-0025"></a>Erro 0025  
 Ocorrerá uma exceção se o conjunto de dados não contém uma coluna de classificação.  
  
 Esse erro no Azure Machine Learning ocorre se a entrada para o modelo de evaluate pontuação válida não contém colunas. Por exemplo, o usuário tenta avaliar um conjunto de dados antes que ele foi classificado com um modelo treinado correto ou a coluna de pontuação foi explicitamente descartada upstream. Essa exceção também ocorre se colunas de pontuação em dois conjuntos de dados são incompatíveis. Por exemplo, você pode estar tentando comparar a exatidão de um regressor linear com o de um classificador binário.  
  
**Resolução:** Rever a entrada para o modelo de avaliação e verifique se ele contém uma ou mais colunas de pontuação. Caso contrário, o conjunto de dados não foi classificado ou colunas de pontuação foram descartadas em um módulo de upstream.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não há nenhuma coluna de classificação no conjunto de dados.|  
|Não há nenhuma coluna de classificação em "{0}".|  
|Não há nenhuma coluna de classificação em "{0}" que é produzido por um "{1}". Pontuar o conjunto de dados usando o tipo correto de aprendiz.|  
  

## <a name="error-0026"></a>Erro 0026  
 Ocorrerá uma exceção se não são permitidas colunas com o mesmo nome.  
  
 Esse erro no Azure Machine Learning ocorre se várias colunas tiverem o mesmo nome. Uma forma, você poderá receber esse erro é se o conjunto de dados não tem uma linha de cabeçalho e nomes de coluna são automaticamente atribuídos: Col0, Col1, etc.  
  
**Resolução:** Se as colunas tiverem o mesmo nome, insira um [editar metadados](edit-metadata.md) módulo entre o conjunto de dados de entrada e o módulo. Use o seletor de coluna no [editar metadados](edit-metadata.md) para selecionar colunas para renomear, digitando os novos nomes para o **novos nomes de coluna** caixa de texto.  
  
|Mensagens de Exceção|  
|------------------------|  
|Nomes de colunas iguais são especificados nos argumentos. Nomes de colunas iguais não são permitidos pelo módulo.|  
|Nomes de colunas nos argumentos iguais "{0}"e"{1}" não são permitidos. Especifique nomes diferentes.|  
  

## <a name="error-0027"></a>Erro 0027  
 Ocorrerá uma exceção quando dois objetos precisam ser do mesmo tamanho, mas não são.  
  
 Isso é um erro comum no Azure Machine Learning e pode ser causado por várias condições.  
  
**Resolução:** Não há nenhuma resolução específica. No entanto, você pode verificar as seguintes condições:  
  
-   Se você está renomeando colunas, certifique-se de que cada lista (as colunas de entrada e a lista de novos nomes) tem o mesmo número de itens.  
  
-   Se você estiver unindo ou concatenar dois conjuntos de dados, certifique-se de que eles têm o mesmo esquema.  
  
-   Se você estiver unindo dois conjuntos de dados que têm várias colunas, certifique-se de que as colunas de chave têm os mesmos dados de tipo e, em seguida, selecione a opção **Permitir duplicatas e preservar a ordem das colunas na seleção**.  
  
|Mensagens de Exceção|  
|------------------------|  
|O tamanho dos objetos passados está inconsistente.|  
|O tamanho do "{0}"é inconsistente com tamanho de"{1}".|  
  

## <a name="error-0028"></a>Erro 0028  
 Ocorrerá uma exceção no caso de quando o conjunto de colunas contém nomes de coluna duplicados e não é permitido.  
  
 Esse erro no Azure Machine Learning ocorre quando os nomes de coluna são duplicados; ou seja, não é exclusivo.  
  
**Resolução:** Se alguma coluna tiver mesmo nome, adicione uma instância do [editar metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo de gerar o erro. Use o seletor de coluna no [editar metadados](edit-metadata.md) para selecionar colunas para renomear e digite os novos nomes de colunas para o **novos nomes de coluna** caixa de texto. Se você estiver renomeando várias colunas, certifique-se de que os valores que você digitar o **novos nomes de coluna** são exclusivos.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de colunas contém nomes de colunas duplicados.|  
|O nome "{0}" está duplicado.|  
|O nome "{0}"está duplicado em"{1}".|  
  

## <a name="error-0029"></a>Erro 0029  
 Ocorrerá uma exceção quando um URI inválido for passado.  
  
 Esse erro no Azure Machine Learning ocorre no caso de quando um URI inválido for passado.  Você receberá esse erro se qualquer uma das seguintes condições forem verdadeiras:, ou.  
  
-   A URI de SAS ou pública fornecida para o armazenamento de BLOBs do Azure para leitura ou gravação contém um erro.  
  
-   A janela de tempo para a SAS expirou.  
  
-   A URL da Web por meio da origem HTTP representa um arquivo ou um URI de loopback.  
  
-   A URL da Web via HTTP contém uma URL formatada incorretamente.  
  
-   A URL não pode ser resolvida pela origem remota.  
  
**Resolução:** Examine o módulo e verificar o formato do URI. Se a fonte de dados é uma URL da Web via HTTP, verifique se a fonte pretendida não é um arquivo ou um URI (localhost) de loopback.  
  
|Mensagens de Exceção|  
|------------------------|  
|Uri inválido for passado.|  
  

## <a name="error-0030"></a>Erro 0030  
 Ocorrerá uma exceção no caso de quando ele não é possível baixar um arquivo.  
  
 Essa exceção no Azure Machine Learning ocorre quando não é possível baixar um arquivo. Você receberá esta exceção quando uma leitura de tentativa de uma origem HTTP falhou após nova tentativa de 3 (três) tentativas.  
  
**Resolução:** Verifique se o URI para a fonte de HTTP está correto e se o site está acessível no momento por meio da Internet.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível baixar um arquivo.|  
|Erro ao baixar o arquivo: {0}.|  
  

## <a name="error-0031"></a>Erro 0031  
 Ocorrerá uma exceção se o número de colunas no conjunto de colunas é menor que o necessário.  
  
 Esse erro no Azure Machine Learning ocorre se o número de colunas selecionadas for menor que o necessário.  Você receberá esse erro se o mínimo necessário de número de colunas não está selecionado.  
  
**Resolução:** Adicionar colunas adicionais para a seleção de coluna usando o **seletor de coluna**.  
  
|Mensagens de Exceção|  
|------------------------|  
|Número de colunas no conjunto de colunas é menor que o necessário.|  
|{0} coluna (s) deve ser especificado. O número real de colunas especificadas é {1}.|  

## <a name="error-0032"></a>Erro 0032  
 Ocorrerá uma exceção se o argumento não é um número.  
  
 Você receberá esse erro no Azure Machine Learning, se o argumento for um valor double ou NaN.  
  
**Resolução:** Modifique o argumento especificado para usar um valor válido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Argumento não é um número.|  
|"{0}" não é um número.|  
  

## <a name="error-0033"></a>Erro 0033  
 Ocorrerá uma exceção se o argumento for infinito.  
  
 Esse erro no Azure Machine Learning ocorre se o argumento for infinito. Você receberá esse erro se o argumento seja `double.NegativeInfinity` ou `double.PositiveInfinity`.  
  
**Resolução:** Modificar o argumento especificado para ser um valor válido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Argumento deve ser finito.|  
|"{0}" não é finito.|  
  

## <a name="error-0034"></a>Erro 0034  
 Ocorrerá uma exceção se não houver mais de uma classificação para um par de determinado usuário-item.  
  
 Esse erro no Azure Machine Learning ocorre na recomendação se um par de item-usuário tem o valor de mais de uma classificação.  
  
**Resolução:** Certifique-se de que o par de item-usuário possua apenas um valor de classificação.  
  
|Mensagens de Exceção|  
|------------------------|  
|Existe mais de uma classificação para os valores no conjunto de dados.|  
|Mais de uma classificação de usuário {0} e o item {1} na tabela de dados de previsão de classificação.|  
  

## <a name="error-0035"></a>Erro 0035  
 Ocorrerá uma exceção se nenhum recurso foi fornecido para um determinado usuário ou item.  
  
 Esse erro no Azure Machine Learning ocorre você está tentando usar um modelo de recomendação de pontuação, mas não foi encontrado um vetor de recurso.  
  
**Resolução:**

O recomendador Matchbox tem certos requisitos que devem ser atendidos ao usar recursos de item ou recursos do usuário.  Esse erro indica que um vetor de recurso está ausente para um usuário ou item que você forneceu como entrada.  Você deve garantir que um vetor de recursos está disponível nos dados para cada usuário ou item.  
  
 Por exemplo, se você treinou uma recomendação de modelo usando recursos como a idade do usuário, local ou renda, mas agora deseja criar pontuações para novos usuários que não foram vistos durante o treinamento, você deve fornecer um conjunto equivalente de recursos (ou seja, idade, local, e valores de renda) para os novos usuários para fazer previsões apropriadas para elas. 
 
 Se você não tiver nenhum recurso para esses usuários, considere a engenharia de recursos para gerar os recursos apropriados.  Por exemplo, se você não tiver valores de idade ou Renda de usuário individual, você pode gerar valores aproximados a ser usado para um grupo de usuários. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > Resolução não se aplica ao seu caso? Você está bem-vindo ao enviar comentários sobre este artigo e fornecem informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Nós usaremos essas informações para fornecer mais detalhado de etapas de solução de problemas no futuro.
   
|Mensagens de Exceção|  
|------------------------|  
|Nenhum recurso foi fornecido para um item ou usuário necessário.|  
|Os recursos de {0} exigido, mas não foi fornecido.|  
  

## <a name="error-0036"></a>Erro 0036  
 Ocorrerá uma exceção se vários vetores do recurso foram fornecidos para um determinado usuário ou item.  
  
 Esse erro no Azure Machine Learning ocorre se um vetor de recurso é definido mais de uma vez.  
  
**Resolução:** Certifique-se de que o vetor de recurso não está definido mais de uma vez.  
  
|Mensagens de Exceção|  
|------------------------|  
|Definição de recurso para um usuário ou item duplicada.|  
|Definição de recurso para duplicada {0}.|  
  

## <a name="error-0037"></a>Erro 0037  
 Ocorrerá uma exceção se várias colunas de rótulo estão especificadas e apenas uma for permitida.  
  
 Esse erro no Azure Machine Learning ocorre se mais de uma coluna for selecionada para ser a nova coluna de rótulo. Mais algoritmos de aprendizado supervisionado exigem uma única coluna a ser marcado como o rótulo ou de destino.  
  
**Resolução:** Certifique-se de selecionar uma única coluna como a nova coluna de rótulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Várias colunas de rótulo estão especificadas.|  
  

## <a name="error-0038"></a>Error 0038  
 Exceção ocorre se o número de elementos esperado deve ser um valor exato, mas não é.  
  
 Esse erro no Azure Machine Learning ocorre se o número de elementos esperado deve ser um valor exato, mas não é.  Você receberá esse erro se o número de elementos não for igual ao valor esperado válido.  
  
**Resolução:** Modifique a entrada para que o número correto de elementos.  
  
|Mensagens de Exceção|  
|------------------------|  
|Número de elementos não é válido.|  
|Número de elementos em "{0}" não é válido.|  
|Número de elementos em "{0}" não é igual ao número válido de {1} (s).|  
  

## <a name="error-0039"></a>Erro 0039  
 Ocorrerá uma exceção se uma operação tiver falhado.  
  
 Esse erro no Azure Machine Learning ocorre quando uma operação interna não pode ser concluída.  
  
**Resolução:** Esse erro é causado por muitas condições e não há nenhum recurso específico.  
 A tabela a seguir contém as mensagens genéricas para esse erro, que são seguidas por uma descrição específica da condição. 
 
 Se nenhum detalhe está disponível, [enviar comentários](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) e fornecem informações sobre os módulos que gerou o erro e as condições relacionadas.
  
|Mensagens de Exceção|  
|------------------------|  
|Falha na operação.|  
|Erro ao concluir a operação: {0}.|  
  

## <a name="error-0040"></a>Erro 0040  
 Ocorrerá uma exceção ao chamar um módulo preterido.  
  
 Esse erro no Azure Machine Learning é produzido quando chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido compatível. Consulte o log de saída do módulo para as informações sobre qual módulo usar em vez disso.  
  
|Mensagens de Exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|Módulo "{0}" foi preterido. Usar o módulo "{1}" em vez disso.|  
 

## <a name="error-0041"></a>Erro 0041  
 Ocorrerá uma exceção ao chamar um módulo preterido.  
  
 Esse erro no Azure Machine Learning é produzido quando chamar um módulo preterido.  
  
**Resolução:** Substitua o módulo preterido com um conjunto de serviços com suporte. Essas informações devem estar visíveis no log de saída do módulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|Módulo "{0}" foi preterido. Use os módulos "{1}" para a funcionalidade solicitada.|  
 

## <a name="error-0042"></a>Erro 0042  
 Ocorrerá uma exceção quando não é possível converter a coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível converter a coluna para o tipo especificado.  Você receberá esse erro se um módulo requer um tipo de dados específico, como data e hora, texto, um número de ponto flutuante ou inteiros, mas não é possível converter uma coluna existente para o tipo solicitado.  
 
Por exemplo, você pode selecionar uma coluna e tentar convertê-lo em um tipo de dados numéricos para uso em uma operação matemática e receber esse erro se a coluna contiver dados inválidos. 

Outro motivo que você poderá receber este erro se você tentar usar uma coluna que contém números de ponto flutuante ou muitos valores exclusivos, como uma coluna categórica. 
  
**Resolução:**

+ Abra a página de ajuda para o módulo que gerou o erro e verifique se os requisitos de tipo de dados.
+ Examine os tipos de dados das colunas no conjunto de dados de entrada.
+ Inspecione dados originados em fontes de dados de esquema chamado.
+ Verifique se o conjunto de dados para valores ausentes ou caracteres especiais que podem bloquear a conversão para o tipo de dados desejado. 
    + Tipos de dados numéricos devem ser consistentes: por exemplo, verificar números de ponto flutuante em uma coluna de inteiros.
    + Procure cadeias de caracteres de texto ou valores em uma coluna de números. 
    + Valores booleanos podem ser convertidos em uma representação apropriada dependendo do tipo de dados necessários.
    + Examine as colunas de texto para caracteres não unicode, caracteres de tabulação ou caracteres de controle
    + Dados de data e hora devem ser consistentes para evitar erros de modelagem, mas a limpeza pode ser complexa devido a muitos formatos. Considere o uso <!--the [Execute R Script](execute-r-script.md) or -->[Executar Script Python](execute-python-script.md) módulos para executar a limpeza.  
+ Se necessário, modifique os valores no conjunto de dados de entrada para que a coluna pode ser convertida com êxito. Pode incluir a modificação de compartimentalização, truncamento ou arredondamento de operações, a eliminação de exceções ou imputação de valores ausentes. Consulte os seguintes artigos para alguns cenários comuns de transformação de dados no machine learning:
    + [Limpar dados ausentes](clean-missing-data.md)
    + [Normalizar dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Resolução clara ou não aplicável ao seu caso? Você está bem-vindo ao enviar comentários sobre este artigo e fornecem informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Nós usaremos essas informações para fornecer mais detalhado de etapas de solução de problemas no futuro.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conversão não permitida.|  
|Não foi possível converter a coluna do tipo {0} a coluna do tipo {1}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna do tipo {1}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna "{3}" do tipo {1}.|  
  

## <a name="error-0043"></a>Erro 0043  
 Ocorrerá uma exceção quando o tipo de elemento não implementar igual explicitamente.  
  
 Esse erro no Azure Machine Learning é usado e será preterido.  
  
**Resolução:** Nenhuma.  
  
|Mensagens de Exceção|  
|------------------------|  
|Nenhum método explícito acessível igual encontrado.|  
|Não é possível comparar valores de coluna \\"{0}\\" do tipo {1}. Nenhum método explícito acessível igual encontrado.|  


## <a name="error-0044"></a>Erro 0044  
 Ocorrerá uma exceção quando não é possível derivar o tipo de elemento da coluna de valores existentes.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível inferir o tipo de uma coluna ou colunas em um conjunto de dados. Isso geralmente acontece ao concatenar dois ou mais conjuntos de dados com tipos de elementos diferentes. Se não puder determinar um tipo comum que é capaz de representar todos os valores em uma coluna ou colunas, sem perda de informações do Azure Machine Learning, ele gerará esse erro.  
  
**Resolução:** Certifique-se de que todos os valores em uma determinada coluna em ambos os conjuntos de dados sendo combinados são do mesmo tipo (cadeia de caracteres numérica, booliana, categórica, data, etc.) ou pode ser forçado para o mesmo tipo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível derivar o tipo de elemento da coluna.|  
|Não é possível derivar o tipo de elemento para a coluna "{0}" – todos os elementos são referências nulas.|  
|Não é possível derivar o tipo de elemento para a coluna "{0}"do conjunto de dados"{1}" – todos os elementos são referências nulas.|  
  

## <a name="error-0045"></a>Erro 0045  
 Ocorrerá uma exceção quando não é possível criar uma coluna devido a tipos de elemento mistos na origem.  
  
 Esse erro no Azure Machine Learning é produzido quando os tipos de elemento de dois conjuntos de dados sendo combinados são diferentes.  
  
**Resolução:** Certifique-se de que todos os valores em uma determinada coluna em ambos os conjuntos de dados sendo combinados são do mesmo tipo (numérica, booliana, categórica, cadeia de caracteres, data etc.).  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível criar a coluna com tipos de elemento mistos.|  
|Não é possível criar a coluna com ID "{0}" de tipos de elemento mistos: \n\tType de dados [{1}, {0}] for {2}\n\tType de dados [{3}, {0}] é {4}.|  
  

## <a name="error-0046"></a>Erro 0046  
 Ocorrerá uma exceção quando não é possível criar diretório no caminho especificado.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível criar um diretório no caminho especificado. Você receberá esse erro se qualquer parte do caminho para o diretório de saída para uma consulta de Hive está incorreto ou inacessível.  
  
**Resolução:** Rever o módulo e verifique se o caminho do diretório está formatado corretamente e se ele está acessível com as credenciais atuais.  
  
|Mensagens de Exceção|  
|------------------------|  
|Especifique um diretório de saída válido.|  
|Diretório: {0} não pode ser criado. Especifique um caminho válido.|  
  

## <a name="error-0047"></a>Erro 0047  
 Ocorrerá uma exceção se o número de colunas de recurso em alguns dos conjuntos de dados passados para o módulo for muito pequeno.  
  
 Esse erro no Azure Machine Learning ocorre se o conjunto de dados de entrada para treinamento não contém o número mínimo de colunas exigidas pelo algoritmo. Normalmente, o conjunto de dados está vazio ou contém apenas as colunas de treinamento.  
  
**Resolução:** Examine o conjunto de dados de entrada para tornar claro que há uma ou mais colunas adicionais além da coluna de rótulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Número de colunas de recursos no conjunto de dados de entrada é menor que o mínimo permitido.|  
|Número de colunas de recursos no conjunto de dados de entrada é menor que o mínimo permitido de {0} coluna (s).|  
|Número de colunas de recurso no conjunto de dados de entrada "{0}" é menor que o mínimo permitido de {1} coluna (s).|  
  

## <a name="error-0048"></a>Error 0048  
 Ocorrerá uma exceção no caso de quando ele não é possível abrir um arquivo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível abrir um arquivo para leitura ou gravação. Você pode receber esse erro por esses motivos:  
  
-   O contêiner ou o arquivo de (blob) não existe  
  
-   O nível de acesso do arquivo ou recipiente não lhe permite acessar o arquivo  
  
-   O arquivo é grande demais para leitura ou um formato incorreto  
  
**Resolução:** Rever o módulo e o arquivo que você está tentando ler.  
  
 Verifique se os nomes de contêiner e do arquivo estão corretos.  
  
 Use o portal clássico do Azure ou uma ferramenta de armazenamento do Azure para verificar se você tem permissão para acessar o arquivo.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível abrir um arquivo.|  
|Erro ao abrir o arquivo: {0}.|  


## <a name="error-0049"></a>Erro 0049  
 Ocorrerá uma exceção no caso de quando ele não é possível analisar um arquivo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível analisar um arquivo. Você receberá esse erro se o formato de arquivo selecionado na [importação de dados](import-data.md) módulo não coincide com o formato real do arquivo, ou se o arquivo contém um caractere não reconhecido.  
  
**Resolução:** Rever o módulo e corrija a seleção de formato de arquivo se ele não coincide com o formato do arquivo. Se possível, inspecione o arquivo para confirmar que ele não contém caracteres inválidos.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível analisar um arquivo.|  
|Erro ao analisar o arquivo: {0}.|  
  

## <a name="error-0050"></a>Erro 0050  
 Ocorrerá uma exceção no caso de entrada quando e arquivos de saída são os mesmos.  
  
**Resolução:** Esse erro no Azure Machine Learning é usado e será preterido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Os arquivos especificados para entrada e saída não podem ser o mesmo.|


## <a name="error-0051"></a>Erro 0051  
 Ocorrerá uma exceção no caso de quando vários arquivos de saída são iguais.  
  
**Resolução:** Esse erro no Azure Machine Learning é usado e será preterido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Os arquivos especificados para saídas não podem ser o mesmo.|


## <a name="error-0052"></a>Erro 0052  
 Ocorrerá uma exceção se a chave de conta de armazenamento do Azure está especificado incorretamente.  
  
 Esse erro no Azure Machine Learning ocorre se a chave usada para acessar a conta de armazenamento do Azure está incorreta. Por exemplo, você poderá ver esse erro se a chave de armazenamento do Azure foi truncada quando copiado e colado, ou se a chave incorreta foi usada.  
  
 Para obter mais informações sobre como obter a chave para uma conta de armazenamento do Azure, consulte [exibir, copiar e regenerar chaves de acesso de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Resolução:** Rever o módulo e verificar se a chave de armazenamento do Azure está correta para a conta. Copie a chave novamente no portal clássico do Azure se necessário.  
  
|Mensagens de Exceção|  
|------------------------|  
|A chave de conta de armazenamento do Azure está incorreta.|  
  

## <a name="error-0053"></a>Erro 0053  
 Ocorrerá uma exceção no caso de quando não há nenhum recurso de usuário ou item para obter recomendações de matchbox.  
  
 Esse erro no Azure Machine Learning é produzido quando um vetor de recurso não pode ser encontrado.  
  
**Resolução:** Certifique-se de que um vetor de recurso está presente no conjunto de dados de entrada.  
  
|Mensagens de Exceção|  
|------------------------|  
|Recursos do usuário ou / e os itens são necessários, mas não fornecidas.|  

## <a name="error-0054"></a>Error 0054  
 Ocorrerá uma exceção se houver poucos valores distintos na coluna para concluir a operação.  
  
**Resolução:** Esse erro no Azure Machine Learning é usado e será preterido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Dados têm poucos valores distintos na coluna especificada para concluir a operação.|  
|Dados têm poucos valores distintos na coluna especificada para concluir a operação. O mínimo necessário é {0} elementos.|  
|Dados têm poucos valores distintos na coluna "{1}" para concluir a operação. O mínimo necessário é {0} elementos.|  
  

## <a name="error-0055"></a>Erro 0055  
 Ocorrerá uma exceção ao chamar um módulo preterido.  Esse erro no Azure Machine Learning é exibida se você tentar chamar um módulo que foi preterido.
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Acessando o módulo preterido.|  
|Módulo "{0}" foi preterido.|  

## <a name="error-0056"></a>Erro 0056  
 Ocorrerá uma exceção se as colunas selecionadas para uma operação viola os requisitos.  
  
 Esse erro no Azure Machine Learning ocorre quando se trata de escolher colunas para uma operação que exige que a coluna ser de um tipo de dados específico. 
 
 Esse erro também pode ocorrer se a coluna é o tipo de dados correto, mas o módulo que você está usando requer que a coluna também ser marcada como um recurso, um rótulo ou uma coluna categórica.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Resolução:**
  
1.  Examine o tipo de dados das colunas selecionadas atualmente. 

2. Verificar se as colunas selecionadas sejam categóricas, rótulo, ou colunas de recurso.  
  
3.  Examine o tópico da Ajuda para o módulo no qual você fez a seleção de coluna, para determinar se há requisitos específicos para o uso de tipo ou coluna de dados.  
  
3.  Use [editar metadados](edit-metadata.md) para alterar o tipo de coluna para a duração dessa operação. Certifique-se de alterar o tipo de coluna para seu valor original, usando outra instância do [editar metadados](edit-metadata.md), se você precisar dela para operações de downstream.  
  
|Mensagens de Exceção|  
|------------------------|  
|Um ou mais colunas selecionadas não estavam em uma categoria permitida.|  
|Coluna com o nome "{0}" não é uma categoria permitida.|  
  

## <a name="error-0057"></a>Erro 0057  
 Ocorrerá uma exceção ao tentar criar um arquivo ou blob que já existe.  
  
 Essa exceção ocorre quando você estiver usando o [exportar dados](export-data.md) módulo ou outro módulo para salvar os resultados de um experimento no Azure Machine Learning para o armazenamento de BLOBs do Azure, mas tentar criar um arquivo ou blob que já existe.   
  
**Resolução:**
 
 Você receberá esse erro só se anteriormente você definiu a propriedade **modo de gravação do armazenamento de BLOBs do Azure** à **erro**. Por design, esse módulo gera um erro se você tentar gravar um conjunto de dados em um blob que já existe.
 
 - Abra as propriedades do módulo e altere a propriedade **modo de gravação do armazenamento de BLOBs do Azure** à **substituir**.
 - Como alternativa, você pode digitar o nome de um arquivo ou blob de destino diferentes e não se esqueça de especificar um blob que ainda não existir.  
  
|Mensagens de Exceção|  
|------------------------|  
|O arquivo ou Blob já existe.|  
|O arquivo ou Blob "{0}" já existe.|  
  

## <a name="error-0058"></a>Erro 0058  
 Esse erro no Azure Machine Learning ocorre se o conjunto de dados não contiver a coluna de rótulo esperada.  
  
 Essa exceção também pode ocorrer quando a coluna de rótulo fornecido não coincide com os dados ou o tipo de dados esperado pelo aprendiz ou tem valores incorretos. Por exemplo, essa exceção é produzida quando o uso de uma coluna de rótulo de valores reais ao treinar um classificador binário.  
  
**Resolução:** A resolução depende do Aprendiz ou instrutor que você está usando e os tipos de dados das colunas no conjunto de dados. Primeiro, verifique se os requisitos do algoritmo de aprendizado de máquina ou módulo de treinamento.  
  
 Examine o conjunto de dados de entrada. Verifique se que a coluna podem ser tratados como o rótulo tem os certos tipo de dados para o modelo que você está criando.  
  
 Verificar as entradas para valores ausentes e eliminar ou substituí-las se necessário.  
  
 Se necessário, adicione a [editar metadados](edit-metadata.md) módulo e certifique-se de que a coluna de rótulo está marcada como um rótulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|A coluna de rótulo não é a esperada|  
|A coluna de rótulo não é conforme o esperado em "{0}".|  
|A coluna de rótulo "{0}"não é esperado no"{1}".|  
  

## <a name="error-0059"></a>Error 0059  
 Ocorrerá uma exceção se um índice de coluna especificado em um seletor de coluna não pode ser analisado.  
  
 Esse erro no Azure Machine Learning ocorre se um índice de coluna especificado ao usar o seletor de coluna não pode ser analisado.  Você receberá esse erro quando o índice da coluna está em um formato inválido que não pode ser analisado.  
  
**Resolução:** Modificar o índice da coluna para usar um valor de índice válido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Um ou mais índices de coluna especificados ou intervalos de índice não pôde ser analisados.|  
|Índice de coluna ou intervalo "{0}" não pôde ser analisado.|  
  

## <a name="error-0060"></a>Erro 0060  
 Ocorrerá uma exceção quando um fora do intervalo da coluna de intervalo é especificado em um seletor de coluna.  
  
 Esse erro no Azure Machine Learning ocorre quando um intervalo de coluna fora do intervalo é especificado no seletor de coluna. Você receberá esse erro se o intervalo da coluna no seletor de coluna não correspondem às colunas no conjunto de dados.  
  
**Resolução:** Modificar o intervalo de coluna no seletor de coluna para corresponder às colunas no conjunto de dados.  
  
|Mensagens de Exceção|  
|------------------------|  
|Inválido ou fora do intervalo de índice de coluna do intervalo especificado.|  
|Intervalo da coluna "{0}" é inválido ou está fora do intervalo.|  
  

## <a name="error-0061"></a>Erro 0061  
 Ocorrerá uma exceção ao tentar adicionar uma linha a uma DataTable que tem um número diferente de colunas da tabela.  
  
 Esse erro no Azure Machine Learning ocorre quando você tenta adicionar uma linha para um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Você receberá esse erro se a linha que está sendo adicionada ao conjunto de dados tem um número diferente de colunas do conjunto de dados de entrada.  A linha não pode ser acrescentada ao conjunto de dados se o número de colunas é diferente.  
  
**Resolução:** Modificar o conjunto de dados de entrada para ter o mesmo número de colunas como a linha adicionada, ou modificar a linha adicionada ao ter o mesmo número de colunas como o conjunto de dados.  
  
|Mensagens de Exceção|  
|------------------------|  
|Todas as tabelas devem ter o mesmo número de colunas.|  
  

## <a name="error-0062"></a>Erro 0062  
 Ocorrerá uma exceção durante a tentativa de comparar dois modelos com tipos diferentes de aprendiz.  
  
 Esse erro no Azure Machine Learning é produzido quando não não possível comparar as métricas de avaliação para dois diferentes conjuntos de dados classificados. Nesse caso, não é possível comparar a eficiência dos modelos usados para produzir dois conjuntos de dados classificados.  
  
**Resolução:** Verifique se que os resultados de pontuação são produzidos pelo mesmo tipo de modelo de aprendizado de máquina (classificação binária, regressão, classificação de várias classes, recomendação, clustering, detecção de anomalias, etc.) Todos os modelos que você compara devem ter o mesmo tipo de aprendiz.  
  
|Mensagens de Exceção|  
|------------------------|  
|Todos os modelos devem ter o mesmo tipo de aprendiz.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Mensagens de Exceção|  
|------------------------|  
|Erro durante a avaliação de script R.|  
|O seguinte erro ocorreu durante a avaliação de script R:---início da mensagem de erro do R--- {0} ---fim da mensagem de erro do R--|  
|Durante a avaliação de script R "{1}" Ocorreu o seguinte erro:---início da mensagem de erro do R--- {0} ---fim da mensagem de erro do R--|  
  


## <a name="error-0064"></a>Erro 0064  
 Ocorrerá uma exceção se o nome da conta de armazenamento do Azure ou a chave de armazenamento foi especificada incorretamente.  
  
 Esse erro no Azure Machine Learning ocorre se o nome da conta de armazenamento do Azure ou a chave de armazenamento for especificada incorretamente. Você receberá esse erro se você inserir um nome de conta incorreta ou a senha da conta de armazenamento. Isso pode ocorrer se você inserir manualmente o nome de conta ou senha. Ele também pode ocorrer se a conta tiver sido excluída.  
  
**Resolução:** Verifique se que o nome da conta e a senha tem sido inseridos corretamente e se a conta existe.  
  
|Mensagens de Exceção|  
|------------------------|  
|O nome da conta de armazenamento do Azure ou a chave de armazenamento está incorreto.|  
|O nome da conta de armazenamento do Azure "{0}" ou a chave de armazenamento para o nome da conta está incorreta.|  
  

## <a name="error-0065"></a>Erro 0065  
 Ocorrerá uma exceção se o nome de blob do Azure estiver especificado incorretamente.  
  
 Esse erro no Azure Machine Learning ocorre se o nome do blob do Azure estiver especificado incorretamente.  Você receberá o erro se:  
  
-   O blob não pode ser encontrado no contêiner especificado.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Apenas o contêiner foi especificado como a origem em um [importação de dados](import-data.md) solicitar quando o formato CSV ou Excel era com codificação; concatenação do conteúdo de todos os blobs dentro de um contêiner não é permitida com esses formatos.  
  
-   Um URI de SAS não contém o nome de um blob válido.  
  
**Resolução:** Examine o módulo de lançar a exceção. Verifique se que o blob especificado existe no contêiner na conta de armazenamento e permissões permitem que você veja o blob. Verifique se a entrada está no formato **containername/filename** se você tiver o Excel ou CSV com formatos de codificação. Verifique se um URI de SAS contém o nome de um blob válido.  
  
|Mensagens de Exceção|  
|------------------------|  
|O blob de armazenamento do Azure está incorreto.|  
|O nome do blob de armazenamento do Azure "{0}" está incorreto|  
  

## <a name="error-0066"></a>Erro 0066  
 Ocorrerá uma exceção se um recurso não pôde ser carregado para um Blob do Azure.  
  
 Esse erro no Azure Machine Learning ocorre se um recurso não pôde ser carregado para um Blob do Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são salvos na mesma conta de armazenamento do Azure como a conta que contém o arquivo de entrada.  
  
**Resolução:** Examine o módulo. Verifique se o nome da conta do Azure, a chave de armazenamento e o contêiner estão corretas e se a conta tem permissão para gravar no contêiner.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não foi possível carregar o recurso de armazenamento do Azure.|  
|O arquivo "{0}" não pôde ser carregado para o armazenamento do Azure como {1}.|  
  

## <a name="error-0067"></a>Erro 0067  
 Ocorrerá uma exceção se um conjunto de dados tem um número diferente de colunas que o esperado.  
  
 Esse erro no Azure Machine Learning ocorre se um conjunto de dados tem um número diferente de colunas que o esperado.  Você receberá esse erro quando o número de colunas no conjunto de dados é diferente do número de colunas que o módulo espera durante a execução.  
  
**Resolução:** Modifica o conjunto de dados de entrada ou os parâmetros.  
  
|Mensagens de Exceção|  
|------------------------|  
|Número inesperado de colunas na datatable.|  
|Esperado "{0}"colunas, mas encontrado"{1}" colunas em vez disso.|  
  

## <a name="error-0068"></a>Erro 0068  
 Ocorrerá uma exceção se o script do Hive especificado não está correto.  
  
 Esse erro no Azure Machine Learning ocorre se houver erros de sintaxe em um script de Hive QL, ou se o interpretador de Hive encontra um erro ao executar o script ou consulta.  
  
**Resolução:**

A mensagem de erro do Hive é normalmente relatada no Log de erros para que você pode tomar ação com base no erro específico. 

+ Abra o módulo e inspecione a consulta em busca de erros.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning efetuando logon no console do Hive do cluster do Hadoop e executar a consulta.  
+ Tente colocar comentários em seu script de Hive em uma linha separada em vez de combinação de instruções executáveis e comentários em uma única linha.  

### <a name="resources"></a>Recursos

Consulte os seguintes artigos para obter ajuda com consultas do Hive para o machine learning:

+ [Criar tabelas Hive e carregar dados do armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Explorar dados em tabelas com consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Criar recursos de dados em um cluster Hadoop usando as consultas do Hive](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive para usuários roteiro do SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de Exceção|  
|------------------------|  
|Script do hive está incorreto.|  
|Script do hive {0} não está correto.|  
  

## <a name="error-0069"></a>Erro 0069  
 Ocorrerá uma exceção se o script SQL especificado não está correto.  
  
 Esse erro no Azure Machine Learning ocorre se o script SQL especificado tem problemas de sintaxe, ou se as colunas ou a tabela especificada no script não é válido. 
 
 Você receberá esse erro se o mecanismo do SQL encontrar qualquer erro ao executar o script ou consulta. A mensagem de erro SQL é normalmente relatada no Log de erros para que você pode tomar ação com base no erro específico.  
  
**Resolução:** Rever o módulo e verifique se a consulta SQL para erros.  
  
 Verifique se a consulta funciona corretamente fora do Azure ML efetuando logon diretamente no servidor de banco de dados e executar a consulta.  
  
 Se houver uma mensagem SQL gerado relatada pela exceção módulo, agir com base no erro relatado. Por exemplo, as mensagens de erro, às vezes, incluem orientações específicas sobre o erro provavelmente:
+ *O banco de dados ausente ou não existe coluna*, indicando que você pode ter digitado um nome de coluna incorreto. Se você tiver certeza do nome da coluna está correto, tente usar colchetes ou aspas para delimitar o identificador da coluna.
+ *Erro de lógica SQL quase \<palavra-chave SQL\>* , indicando que você pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de Exceção|  
|------------------------|  
|Script SQL está incorreto.|  
|Consulta SQL "{0}" não está correto.|  
|Consulta SQL "{0}" não está correto: {1}|  
  

## <a name="error-0070"></a>Erro 0070  
 Ocorrerá uma exceção ao tentar acessar a tabela do Azure inexistente.  
  
 Esse erro no Azure Machine Learning ocorre quando você tenta acessar uma tabela do Azure com inexistente. Se você especificar uma tabela no armazenamento do Azure, que não existe durante a leitura ou gravação para o armazenamento de tabela do Azure, você receberá esse erro. Isso pode acontecer se você digitar incorretamente o nome da tabela desejada, ou se tiver uma incompatibilidade entre o nome de destino e o tipo de armazenamento. Por exemplo, você pretende ler de uma tabela, mas inseriu o nome de um blob em vez disso.  
  
**Resolução:** Examine o módulo para verificar se o nome da tabela está correto.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não existe tabela do Azure.|  
|Tabela do Azure "{0}" não existe.|  
  
## <a name="error-0071"></a>Erro 0071  
 Ocorrerá uma exceção se as credenciais fornecidas estiverem incorretas.  
  
 Esse erro no Azure Machine Learning ocorre se as credenciais fornecidas estão incorretas.  
  
 Você também poderá receber esse erro se o módulo não pode se conectar a um cluster HDInsight.  
  
**Resolução:** Examine as entradas para o módulo e verifique se o nome da conta e a senha.  
  
 Verifique os seguintes problemas que podem causar um erro:  
  
-   O esquema do conjunto de dados não coincide com o esquema da datatable de destino.  
  
-   Nomes de coluna estão ausentes ou incorretas  
  
-   Você estiver gravando em uma tabela que tem nomes de coluna com os caracteres inválidos. Normalmente, você pode colocar esses nomes de coluna entre colchetes, mas se isso não funcionar, edite os nomes de coluna para usar apenas letras e sublinhados (_)  
  
-   Cadeias de caracteres que você está tentando gravar contenham aspas simples  
  
 Se você estiver tentando se conectar a um cluster HDInsight, verifique se que o cluster de destino está acessível com as credenciais fornecidas.  
  
|Mensagens de Exceção|  
|------------------------|  
|Credenciais incorretas são passadas.|  
|Nome de usuário incorreto "{0}" ou senha é passada.|  
  

## <a name="error-0072"></a>Erro 0072  
 Ocorrerá uma exceção no caso de tempo limite da conexão.  
  
 Esse erro no Azure Machine Learning ocorre quando uma conexão atinge o tempo limite. Você receberá esse erro se não houver problemas de conectividade com a fonte de dados ou o destino, como conectividade de internet lenta, ou se o conjunto de dados é grande e/ou a consulta SQL para ler os dados executa o processamento complicado.  
  
**Resolução:** Determine se há problemas com conexões lentas para o armazenamento do Azure ou na internet.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tempo limite de Conexão esgotado.|  
  

## <a name="error-0073"></a>Erro 0073  
 Ocorrerá uma exceção se ocorrer um erro ao converter uma coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando não é possível converter a coluna em outro tipo.  Você receberá esse erro se um módulo requer um tipo específico e não é possível converter a coluna para o novo tipo.  
  
**Resolução:** Modifique o conjunto de dados de entrada para que a coluna pode ser convertida com base na exceção interna.  
  
|Mensagens de Exceção|  
|------------------------|  
|Falha ao converter a coluna.|  
|Falha ao converter a coluna para {0}.|  
  

## <a name="error-0074"></a>Erro 0074  
 Ocorrerá uma exceção quando o [editar metadados](edit-metadata.md) tenta converter uma coluna esparsa em categórica.  
  
 Esse erro no Azure Machine Learning ocorre quando o [editar metadados](edit-metadata.md) tenta converter uma coluna esparsa em categórica.  Você receberá esse erro ao tentar converter as colunas esparsas em categóricos com o **tornar categórico** opção.  Do Azure machine Learning não oferece suporte esparsos matrizes categóricos, para que o módulo falhará.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Colunas esparsas não podem ser convertidas em categóricas.|  
  

## <a name="error-0075"></a>Erro 0075  
Ocorrerá uma exceção quando uma função de agrupamento inválida for usada ao quantizar um conjunto de dados.  
  
Esse erro no Azure Machine Learning ocorre quando você está tentando guardar dados usando um método sem suporte ou quando as combinações de parâmetro são inválidas.  
  
**Resolução:**

Tratamento de erro para esse evento foi introduzido em uma versão anterior do Azure Machine Learning que permissão mais personalização de métodos de agrupamento. Atualmente, todos os métodos de compartimentalização baseiam-se em uma seleção em uma lista suspensa, tecnicamente não deve ser possível obter esse erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Função de agrupamento inválido usada.|  
  

## <a name="error-0077"></a>Erro 0077  
 Exceção ocorre quando gravações de arquivo blob desconhecido modo passado.  
  
 Esse erro no Azure Machine Learning ocorre se um argumento inválido for passado nas especificações para um destino de arquivo de blob ou o código-fonte.  
  
**Resolução:** Em quase todos os módulos importar ou exportar dados para e do armazenamento de BLOBs do Azure, os valores de parâmetro de controlar o modo de gravação são atribuídos usando uma lista suspensa; Portanto, não é possível passar um valor inválido, e esse erro não deve aparecer. Esse erro será preterido em uma versão posterior.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não há suporte para blob grava modo.|  
|Modo de gravações de blob sem suporte: {0}.|  
  

## <a name="error-0078"></a>Erro 0078  
 Ocorrerá uma exceção quando a opção para o HTTP [importação de dados](import-data.md) recebe um código de status 3xx indicando o redirecionamento.  
  
 Esse erro no Azure Machine Learning ocorre quando a opção para o HTTP [importação de dados](import-data.md) recebe um 3xx (301, 302, 304, etc.) o código de status indicando o redirecionamento. Você receberá esse erro se você tentar se conectar a uma fonte HTTP que redireciona o navegador para outra página. Para segurança motivos, redirecionando sites não são permitidos como fontes de dados para o Azure Machine Learning.  
  
**Resolução:** Se o site é um site confiável, digite a URL redirecionado diretamente.  
  
|Mensagens de Exceção|  
|------------------------|  
|Redirecionamento de HTTP não permitido|  
  

## <a name="error-0079"></a>Erro 0079  
 Ocorrerá uma exceção se o nome do contêiner de armazenamento do Azure está especificado incorretamente.  
  
 Esse erro no Azure Machine Learning ocorre se o nome do contêiner de armazenamento do Azure estiver especificado incorretamente. Você receberá esse erro se você não especificou o contêiner e o nome de blob (arquivo) usando **o caminho para o blob que começa com contêiner** opção ao gravar no armazenamento de BLOBs do Azure.  
  
**Resolução:** Examine os [exportar dados](export-data.md) módulo e verifique se o caminho especificado para o blob que contém o contêiner e o nome do arquivo no formato **contêiner/nome de arquivo**.  
  
|Mensagens de Exceção|  
|------------------------|  
|O nome do contêiner de armazenamento do Azure está incorreto.|  
|O nome do contêiner de armazenamento do Azure "{0}" está incorreto; era esperado um nome de contêiner do formato contêiner/blob.|  
  

## <a name="error-0080"></a>Erro 0080  
 Ocorrerá uma exceção quando a coluna com todos os valores ausentes não é permitida pelo módulo.  
  
 Esse erro no Azure Machine Learning é produzido quando um ou mais das colunas consumidas pelo módulo contém todos os valores ausentes. Por exemplo, se um módulo é computar estatísticas agregadas para cada coluna, ele não pode operar em uma coluna que contém nenhum dado. Nesses casos, a execução do módulo é interrompida com essa exceção.  
  
**Resolução:** Examine o conjunto de dados de entrada e remova todas as colunas que contêm todos os valores ausentes.  
  
|Mensagens de Exceção|  
|------------------------|  
|Colunas com todos os valores ausentes não são permitidas.|  
|Coluna {0} tem todos os valores ausentes.|  
  

## <a name="error-0081"></a>Erro 0081  
 Ocorrerá uma exceção no módulo PCA se o número de dimensões para reduzir é igual ao número de colunas de recurso no conjunto de dados entrado, que contém pelo menos uma coluna de recurso esparsa.  
  
 Esse erro no Azure Machine Learning é gerado se as seguintes condições forem atendidas: (a) o conjunto de dados de entrada tem pelo menos uma coluna esparsa e (b) o número final de dimensões solicitado é o mesmo que o número de dimensões de entrada.  
  
**Resolução:** Considere reduzir o número de dimensões na saída para ser menor do que o número de dimensões na entrada. Isso é comum em aplicativos do PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Para o conjunto de dados contendo colunas de recursos esparsas número de dimensões para reduzir deve ser menor que o número de colunas de recurso.|  
 

## <a name="error-0082"></a>Erro 0082  
 Ocorrerá uma exceção quando um modelo não pode ser desserializado com êxito.  
  
 Esse erro no Azure Machine Learning ocorre quando uma modelo de aprendizado de máquina salva ou transformação não pode ser carregada por uma versão mais recente do tempo de execução do Azure Machine Learning como resultado de uma alteração significativa.  
  
**Resolução:** O teste de treinamento que produziu o modelo ou a transformação deve ser executar novamente e o modelo ou transformação deve ser salvo novamente.  
  
|Mensagens de Exceção|  
|------------------------|  
|Modelo não pôde ser desserializado porque ele provavelmente está serializado com um formato de serialização anterior. Treinar novamente e salve o modelo.|  
  

## <a name="error-0083"></a>Erro 0083  
 Ocorrerá uma exceção se o conjunto de dados usado para treinamento não pode ser usado para o tipo concreto de aprendiz.  
  
 Esse erro no Azure Machine Learning é produzido quando o conjunto de dados é incompatível com o aprendiz que está sendo treinado. Por exemplo, o conjunto de dados pode conter pelo menos um valor ausente em cada linha, e como resultado, o conjunto de dados inteiro deverá ser ignorado durante o treinamento. Em outros casos, alguns algoritmos de aprendizado de máquina, como detecção de anomalias não esperam o rótulos esteja presente em podem gerar essa exceção se os rótulos estiverem presentes no conjunto de dados.  
  
**Resolução:** Consulte a documentação do Aprendiz que está sendo usado para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver todas as colunas necessárias estão presentes.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de dados usado para treinamento é inválido.|  
|{0} contém dados inválidos para treinamento.|  
|{0} contém dados inválidos para treinamento. Tipo de aprendiz: {1}.|  
  

## <a name="error-0084"></a>Erro 0084  
 Ocorrerá uma exceção quando pontuações produzidas a partir de um Script de R são avaliadas. Isso é atualmente sem suporte.  
  
 Esse erro no Azure Machine Learning ocorre se você tentar usar um dos módulos para avaliar um modelo com a saída de um script de R que contém as pontuações.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Há suporte atualmente para avaliar os resultados produzidos por R.|  
  

## <a name="error-0085"></a>Erro 0085  
 Ocorrerá uma exceção quando a avaliação de script falhará com um erro.  
  
 Esse erro no Azure Machine Learning ocorre quando você estiver executando um script personalizado que contém erros de sintaxe.  
  
**Resolução:** Revise seu código em um editor externo e verificar se há erros.  
  
|Mensagens de Exceção|  
|------------------------|  
|Erro durante a avaliação de script.|  
|O seguinte erro ocorreu durante a avaliação de script, exibir o log de saída para obter mais informações:---início da mensagem de erro do {0} interpretador--- {1} ---fim da mensagem de erro do {0} interpretador-- ------|  
  

## <a name="error-0086"></a>Erro 0086  
 Ocorrerá uma exceção quando uma transformação de contagem é inválida.  
  
 Esse erro no Azure Machine Learning ocorre quando você seleciona uma transformação com base em uma tabela de contagem, mas a transformação selecionada é incompatível com os dados atuais, ou com a nova tabela de contagem.  
  
**Resolução:** O módulo suporta salvando as contagens e regras que compõem a transformação em dois formatos diferentes. Se você estiver mesclando tabelas de contagem, verifique se ambas as tabelas que você pretende mesclar usam o mesmo formato.  
  
Em geral, uma transformação com base em Contagem só pode ser aplicada aos conjuntos de dados que têm o mesmo esquema que o conjunto de dados no qual a transformação foi originalmente criada.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de Exceção|  
|------------------------|  
|Transformação de contagem inválida especificada.|  
|A transformação de contagem na porta de entrada '{0}' é inválido.|  
|A transformação de contagem na porta de entrada '{0}'não pode ser mesclada com a transformação de contagem na porta de entrada'{1}'. Verifique se os metadados usados para a contagem de correspondências.|  
  

## <a name="error-0087"></a>Erro 0087  
 Ocorrerá uma exceção quando um tipo de tabela de contagem inválido é especificado para o aprendizado com contagens de módulos.  
  
 Esse erro no Azure Machine Learning ocorre quando você tentar importar uma tabela de contagem existente, mas a tabela é incompatível com os dados atuais, ou com a nova tabela de contagem.  
  
**Resolução:** Há diferentes formatos para salvar as contagens e regras que compõem a transformação. Se você estiver mesclando tabelas de contagem, verifique se que ambos usam o mesmo formato.  
  
 Em geral, uma transformação com base em Contagem só pode ser aplicada aos conjuntos de dados que têm o mesmo esquema que o conjunto de dados no qual a transformação foi originalmente criada.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>Erro 0088  
 Exceção ocorre quando uma contagem inválida tipo é especificado para o aprendizado com contagens de módulos.  
  
 Esse erro no Azure Machine Learning ocorre quando você tenta usar um método diferente de contagem que há suporte para personalização com base na contagem.  
  
**Resolução:** Em geral, os métodos de contagem são escolhidos em uma lista suspensa, então você não deve ver este erro.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Mensagens de Exceção|  
|------------------------|  
|Tipo de contagem de inválido é especificado.|  
|O tipo especificado de contagem '{0}' não é um tipo de contagem válido.|  
  

## <a name="error-0089"></a>Erro 0089  
 Ocorrerá uma exceção quando o número de classes especificado é menor que o número real de classes em um conjunto de dados usado para contagem.  
  
 Esse erro no Azure Machine Learning ocorre quando você estiver criando uma tabela de contagem e a coluna de rótulo contém um número diferente de classes do que o especificado nos parâmetros do módulo.  
  
**Resolução:** Verifique seu conjunto de dados e descobrir exatamente como muitos valores distintos (classes possíveis) há na coluna rótulo. Quando você cria a tabela de contagem, você deve especificar pelo menos esse número de classes.  
  
 A tabela de contagem não pode determinar automaticamente o número de classes disponíveis.  
  
 Quando você cria a tabela de contagem, você não pode especificar 0 ou qualquer número que é menor que o número real de classes na coluna rótulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|O número de classes está incorreto. Certifique-se de que o número de classes que você especificar no painel de parâmetro é maior que ou igual ao número de classes na coluna rótulo.|  
|O número de classes especificado é '{0}', que não é maior que um valor de rótulo'{1}' no conjunto de dados usado para a contagem. Certifique-se de que o número de classes que você especificar no painel de parâmetro é maior que ou igual ao número de classes na coluna rótulo.|  
  

## <a name="error-0090"></a>Erro 0090  
 Ocorrerá uma exceção quando ocorre falha na criação de tabela do Hive.  
  
 Esse erro no Azure Machine Learning ocorre quando você estiver usando [exportar dados](export-data.md) ou outra opção para salvar dados em um cluster do HDInsight e a tabela Hive especificada não pode ser criada.  
  
**Resolução:** Verifique o nome da conta de armazenamento do Azure associado ao cluster e verificar se você está usando a mesma conta nas propriedades do módulo.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não foi possível criar a tabela de Hive. Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que o que é passado por meio do parâmetro de módulo.|  
|A tabela do Hive "{0}" não pôde ser criado. Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que o que é passado por meio do parâmetro de módulo.|  
|A tabela do Hive "{0}" não pôde ser criado. Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é "{1}".|  
 

## <a name="error-0100"></a>Erro 0100  
 Ocorrerá uma exceção quando um idioma sem suporte é especificado para um módulo personalizado.  
  
 Esse erro no Azure Machine Learning ocorre quando a criação de um módulo personalizado e a propriedade name do **linguagem** elemento em um arquivo de definição de xml do módulo personalizado tem um valor inválido. Atualmente, o único valor válido para essa propriedade é `R`. Por exemplo:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Verificar se a propriedade name do **linguagem** elemento no arquivo de definição xml de módulo personalizado é definido como `R`. Salve o arquivo, atualizar o pacote de zip do módulo personalizado e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de Exceção|  
|------------------------|  
|Idioma de módulo personalizado sem suporte especificado|  
  

## <a name="error-0101"></a>Erro 0101  
 Todas as IDs de porta e o parâmetro devem ser exclusivas.  
  
 Esse erro no Azure Machine Learning ocorre quando uma ou mais portas ou os parâmetros recebem o mesmo valor de ID em um arquivo XML de definição de módulo personalizado.  
  
**Resolução:** Verifique se os valores de ID em todas as portas e os parâmetros são exclusivos. Salve o arquivo xml, atualize o pacote de zip do módulo personalizado e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de Exceção|  
|------------------------|  
|Todas as portas e IDs de parâmetro para um módulo devem ser exclusivos|  
|Módulo '{0}' tem a porta/argumento IDs duplicado. Todas as IDs de argumento/porta devem ser exclusivas para um módulo.|  
  

## <a name="error-0102"></a>Erro 0102  
 Gerada quando um arquivo ZIP não pode ser extraído.  
  
 Esse erro no Azure Machine Learning ocorre quando você estiver importando um pacote compactado com a extensão. zip, mas não do pacote é um arquivo zip, ou o arquivo não usa um formato de zip com suporte.  
  
**Resolução:** Verifique se o arquivo selecionado é um arquivo. zip válido e que ela foi compactada usando um dos algoritmos de compactação com suporte.  
  
 Se você receber esse erro ao importar conjuntos de dados em formato compactado, verifique se que todos os arquivos contidos usam um dos formatos de arquivo com suporte e estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Tente adicione novamente os arquivos desejados para uma nova pasta zipada compactada e tente adicionar o módulo personalizado novamente.  
  
|Mensagens de Exceção|  
|------------------------|  
|ZIP fornecido arquivo não está no formato correto|  


## <a name="error-0103"></a>Erro 0103  
 Gerada quando um arquivo ZIP não contém todos os arquivos. XML  
  
 Esse erro no Azure Machine Learning ocorre quando o pacote de zip do módulo personalizado não contém quaisquer arquivos de definição (. xml) do módulo. Esses arquivos precisam residir na raiz do pacote zip (por exemplo, não em uma subpasta.)  
  
**Resolução:** Verifique se um ou mais arquivos de definição de módulo xml na pasta raiz do pacote zip extraindo-o para uma pasta temporária na unidade de disco. Todos os arquivos xml devem ser diretamente na pasta que você extraiu o pacote zip para. Certifique-se de quando você cria o pacote zip que você não selecionar uma pasta que contém arquivos xml para zip como isso criará uma subpasta dentro do pacote zip com o mesmo nome que a pasta selecionada para compactar.  
  
|Mensagens de Exceção|  
|------------------------|  
|ZIP fornecido o arquivo não contém qualquer arquivo de definição de módulo (arquivos. xml)|  


## <a name="error-0104"></a>Erro 0104  
 Gerada quando um arquivo de definição de módulo faz referência a um script que não pode ser localizado  
  
 Esse erro no Azure Machine Learning é gerado quando um arquivo de definição de xml do módulo personalizado faz referência a um arquivo de script do **linguagem** elemento que não existe no pacote zip. O caminho do arquivo de script é definido na **sourceFile** propriedade da **idioma** elemento. O caminho para o arquivo de origem é relativa à raiz do pacote zip (mesmo local que os arquivos de definição de xml do módulo). Se o arquivo de script estiver em uma subpasta, o caminho relativo para o arquivo de script deve ser especificado. Por exemplo, se todos os scripts foram armazenados em um **myScripts** pasta dentro do pacote de zip, o **idioma** elemento precisaria adicionar este caminho para o **sourceFile** a propriedade como abaixo. Por exemplo:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Resolução:** Certifique-se de que o valor da **sourceFile** propriedade no **idioma** elemento da definição de xml do módulo personalizado está correto e se o arquivo de origem existe no caminho relativo no pacote zip correto.  
  
|Mensagens de Exceção|  
|------------------------|  
|Arquivo de script R referenciado não existe.|  
|Arquivo de script R referenciado '{0}' não pode ser encontrado. Certifique-se de que o caminho relativo para o arquivo está correto do local de definições.|  


## <a name="error-0105"></a>Erro 0105  
 Esse erro é exibido quando um arquivo de definição de módulo contém um tipo de parâmetro sem suporte  
  
 Esse erro no Azure Machine Learning é produzido quando você criar uma definição de xml do módulo personalizado e o tipo de um parâmetro ou um argumento na definição de não corresponde a um tipo com suporte.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de qualquer **Arg** elemento no arquivo de definição xml de módulo personalizado é um tipo com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de parâmetro sem suporte.|  
|Tipo de parâmetro sem suporte '{0}' especificado.|  


## <a name="error-0106"></a>Erro 0106  
 Gerado quando um arquivo de definição de módulo define um tipo de entrada sem suporte  
  
 Esse erro no Azure Machine Learning é gerado quando o tipo de uma porta de entrada em um módulo personalizado de definição de XML não corresponde a um tipo com suporte.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de um elemento de entrada no arquivo de definição XML de módulo personalizado é um tipo com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não há suporte para o tipo de entrada.|  
|Tipo de entrada sem suporte '{0}' especificado.|  


## <a name="error-0107"></a>Erro 0107  
 Gerado quando um arquivo de definição de módulo define um tipo de saída sem suporte  
  
 Esse erro no Azure Machine Learning é gerado quando o tipo de uma porta de saída em uma definição de xml do módulo personalizado não corresponde a um tipo com suporte.  
  
**Resolução:** Certifique-se de que a propriedade de tipo de um elemento de saída no arquivo de definição xml de módulo personalizado é um tipo com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de saída sem suporte.|  
|Tipo de saída sem suporte '{0}' especificado.|  


## <a name="error-0108"></a>Erro 0108  
 Gerado quando um arquivo de definição de módulo define mais portas de entrada ou saídas para que o permitido  
  
 Esse erro no Azure Machine Learning é produzido quando muitas portas de entrada ou saídas são definidas em uma definição de xml do módulo personalizado.  
  
**Resolução:** Verifica se o número máximo de portas de entrada e saídas definidos na definição de módulo personalizado de xml não exceder o número máximo de portas com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Excedido o número de portas de entrada ou saídas com suporte.|  
|Excedido o número de suporte para '{0}' portas. Número máximo permitido de '{0}'portas é'{1}'.| 

## <a name="error-0109"></a>Erro 0109  
 Gerado quando um arquivo de definição de módulo define um seletor de coluna incorretamente  
  
 Esse erro no Azure Machine Learning é produzido quando a sintaxe para um argumento de seletor de coluna contiver um erro em uma definição de xml do módulo personalizado.  
  
**Resolução:** Esse erro é gerado quando a sintaxe para um argumento de seletor de coluna contiver um erro em uma definição de xml do módulo personalizado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Sintaxe sem suporte para o seletor de coluna.|  
  

## <a name="error-0110"></a>Erro 0110  
 Gerado quando um arquivo de definição de módulo define um seletor de coluna que faz referência a uma ID de porta de entrada não existente  
  
 Esse erro no Azure Machine Learning é gerado quando o *portId* propriedade dentro do elemento de propriedades de um argumento do tipo Columnpickerfor não coincide com o valor da ID de uma porta de entrada.  
  
**Resolução:** Verifique se que a propriedade portId corresponde o valor da ID de uma porta de entrada definido na definição de xml de módulo personalizado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Seletor de coluna faz referência a uma ID de porta de entrada não existente.|  
|Seletor de coluna faz referência a uma ID de porta de entrada não existente '{0}'.|  
  

## <a name="error-0111"></a>Erro 0111  
 Gerado quando um arquivo de definição de módulo define uma propriedade inválida  
  
 Esse erro no Azure Machine Learning é gerado quando uma propriedade inválida é atribuída a um elemento em que o módulo personalizado de definição XML.  
  
**Resolução:** Verifique se que a propriedade é compatível com o elemento de módulo personalizado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Definição da propriedade é inválida.|  
|Definição da propriedade '{0}' é inválido.|  
  

## <a name="error-0112"></a>Erro 0112  
 Gerada quando um arquivo de definição de módulo não pode ser analisado.  
  
 Esse erro no Azure Machine Learning é gerado quando ocorre um erro no formato xml que impede que o módulo personalizado de definição XML do que está sendo analisada como um arquivo XML válido.  
  
**Resolução:** Certifique-se de que cada elemento é aberto e fechado corretamente. Certifique-se de que não há nenhum erro na formatação de XML.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível analisar o arquivo de definição de módulo.|  
|Não é possível analisar o arquivo de definição de módulo '{0}'.|  
  

## <a name="error-0113"></a>Erro 0113  
 Gerada quando um arquivo de definição de módulo contém erros.  
  
 Esse erro no Azure Machine Learning é gerado quando o arquivo de definição XML de módulo personalizado pode ser analisado, mas contém erros, como definição de elementos que não tem suportada por módulos personalizados.  
  
**Resolução:** Verifique se que o arquivo de definição de módulo personalizado define elementos e propriedades que são compatíveis com módulos personalizados.  
  
|Mensagens de Exceção|  
|------------------------|  
|Arquivo de definição de módulo contém erros.|  
|Arquivo de definição de módulo '{0}' contém erros.|  
|Arquivo de definição de módulo '{0}' contém erros. [https://doi.org/10.13012/J8PN93H8]({1})|  
  

## <a name="error-0114"></a>Erro 0114  
 Gerada quando a criação de um módulo personalizado falhar.  
  
 Esse erro no Azure Machine Learning é gerado quando um módulo personalizado de build falhar. Isso ocorre quando um ou mais erros relacionados a módulo personalizados são encontrados ao adicionar o módulo personalizado. Outros erros são relatados nessa mensagem de erro.  
  
**Resolução:** Resolva os erros relatados nessa mensagem de exceção.  
  
|Mensagens de Exceção|  
|------------------------|  
|Falha ao compilar o módulo personalizado.|  
|Compilações com falha com erros de módulo personalizado: {0}|  
  

## <a name="error-0115"></a>Erro 0115  
 Gerada quando um script de padrão de módulo personalizado tem uma extensão sem suporte.  
  
 Esse erro no Azure Machine Learning ocorre quando você fornece um script para um módulo personalizado que usa uma extensão de nome de arquivo desconhecido.  
  
**Resolução:** Verifique se a extensão de formato e o nome de arquivo do arquivo de qualquer arquivo de script incluídos no módulo personalizado.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não há suporte para a extensão de script padrão.|  
|Extensão de arquivo sem suporte {0} para script padrão.|  
  

## <a name="error-0121"></a>Erro 0121  
 Gerada quando o SQL grava falhará porque a tabela não é gravável  
  
 Esse erro no Azure Machine Learning é gerado quando você estiver usando o [exportar dados](export-data.md) módulo para salvar os resultados em uma tabela no banco de dados SQL e a tabela não pode ser gravado. Normalmente, você verá esse erro se o [exportar dados](export-data.md) módulo com êxito estabelece uma conexão com a instância do SQL Server, mas, em seguida, é possível gravar o conteúdo do conjunto de dados do Azure ML na tabela.  
  
**Resolução:**
 - Abra o painel de propriedades do [exportar dados](export-data.md) módulo e verificar se os nomes de tabela e banco de dados são inseridos corretamente. 
 - Examine o esquema do conjunto de dados você está exportando e certifique-se de que os dados são compatíveis com a tabela de destino.
 - Verifique se o SQL entrar associado com o nome de usuário e senha tem permissões para gravar na tabela. 
 - Se a exceção contém informações adicionais sobre erros do SQL Server, use essas informações para fazer correções.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conectado ao servidor, não é possível gravar a tabela.|  
|Não é possível gravar a tabela Sql: {0}|  


## <a name="error-0122"></a>Erro 0122  
 Ocorrerá uma exceção se várias colunas de peso estão especificadas e apenas uma for permitida.  
  
 Esse erro no Azure Machine Learning ocorre quando muitas colunas foram selecionado como colunas de peso.  
  
**Resolução:** Examine o conjunto de dados de entrada e seus metadados. Certifique-se de que apenas uma coluna contém os pesos.  
  
|Mensagens de Exceção|  
|------------------------|  
|Várias colunas de peso estão especificadas.|  


## <a name="error-0123"></a>Erro 0123  
 Ocorrerá uma exceção se a coluna de vetores foi especificada para a coluna rótulo.  
  
 Esse erro no Azure Machine Learning ocorre se você usar um vetor como a coluna de rótulo.  
  
**Resolução:** Alterar o formato de dados da coluna, se necessário, ou escolha uma coluna diferente.  
  
|Mensagens de Exceção|  
|------------------------|  
|Coluna de vetores foi especificada como coluna de rótulo.|  


## <a name="error-0124"></a>Erro 0124  
 Ocorrerá uma exceção se colunas não numéricas forem especificadas para ser a coluna de peso.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|A coluna não numérica é especificada como a coluna de peso.|  
  


## <a name="error-0125"></a>Erro 0125  
 Gerada quando o esquema para vários conjuntos de dados não coincide.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Esquema de conjunto de dados não coincide.|  


## <a name="error-0126"></a>Erro 0126  
 Ocorrerá uma exceção se o usuário especificar um domínio SQL que não tem suporte no Azure ML.  
  
 Esse erro é gerado quando o usuário Especifica um domínio do SQL que não tem suporte no Azure Machine Learning. Você receberá esse erro se você está tentando se conectar a um servidor de banco de dados em um domínio que não está na lista de permissões. Atualmente, os domínios SQL permitidos são: ". database.windows.net",". cloudapp.net", ou ". database.secure.windows.net". Ou seja, o servidor deve ser um servidor SQL Azure ou um servidor em uma máquina virtual no Azure.  
  
**Resolução:** Examine o módulo. Verifique se o servidor de banco de dados SQL pertence a um dos domínios aceitos:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Mensagens de Exceção|  
|------------------------|  
|Domínio do SQL sem suporte.|  
|O domínio SQL {0} não é suportado atualmente no Azure ML|  
  

## <a name="error-0127"></a>Erro 0127  
 Tamanho de pixel da imagem excede o limite permitido  
  
 Esse erro ocorre se você estiver lendo imagens de um conjunto de dados de imagem para classificação e as imagens são maiores do que o modelo pode manipular.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Tamanho de pixel da imagem excede o limite permitido.|  
|O tamanho de pixel no arquivo de imagem '{0}' excede o limite permitido: '{1}'|  


## <a name="error-0128"></a>Erro 0128  
 Número de probabilidades condicionais para colunas categóricas excede o limite.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Número de probabilidades condicionais para colunas categóricas excede o limite.|  
|Número de probabilidades condicionais para colunas categóricas excede o limite. Colunas de{0}'e'{1}' são o par problemático.|  


## <a name="error-0129"></a>Erro 0129  
 Número de colunas no conjunto de dados excede o limite permitido.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Número de colunas no conjunto de dados excede o limite permitido.|  
|Número de colunas no conjunto de dados em '{0}'excede o permitido.'|  
|Número de colunas no conjunto de dados em '{0}'excede o limite permitido de'{1}'.'|  
|Número de colunas no conjunto de dados em '{0}'excede o permitido'{1}'limite de'{2}'.'|  
## <a name="error-0130"></a>Erro 0130  
 Ocorrerá uma exceção quando todas as linhas no conjunto de dados de treinamento contenham valores ausentes.  
  
 Isso ocorre quando alguma coluna no conjunto de dados de treinamento está vazia.  
  
**Resolução:** Use o [limpar dados ausentes](clean-missing-data.md) módulo para remover colunas com todos os valores ausentes.  
  
|Mensagens de Exceção|  
|------------------------|  
|Todas as linhas no conjunto de dados de treinamento contenham valores ausentes.  Considere usar o módulo de limpar dados ausentes para remover valores ausentes.|  
 

## <a name="error-0131"></a>Erro 0131  
 Ocorrerá uma exceção se a falha de um ou mais conjuntos de dados em um arquivo zip seja descompactado e registrado corretamente  
  
 Esse erro é gerado quando um ou mais conjuntos de dados em um arquivo zip não conseguir ser descompactado e ler corretamente. Você receberá esse erro se a descompactação falha porque o próprio arquivo zip ou um dos arquivos em que ele está corrompido ou há um erro de sistema ao tentar descompactar e expandir um arquivo.  
  
**Resolução:** Use os detalhes fornecidos na mensagem de erro para determinar como proceder.  
  
|Mensagens de Exceção|  
|------------------------|  
|Carregar conjuntos de dados compactados falhou|  
|Compactado dataset {0} falhou com a seguinte mensagem: {1}|  
|Compactado dataset {0} falhou com um {1} exceção com a mensagem: {2}|  
  

## <a name="error-0132"></a>Erro 0132  
 Nenhum nome de arquivo foi especificado para desempacotar; vários arquivos foram encontrados no arquivo zip.  
  
 Esse erro é gerado quando nenhum nome de arquivo foi especificado para desempacotar; vários arquivos foram encontrados no arquivo zip. Você receberá esse erro se o arquivo. zip contém mais de um arquivo compactado, mas você não especificou um arquivo para extração na **conjunto de dados para desempacotar** texto na caixa de **propriedade** painel do módulo. Atualmente, apenas um arquivo pode ser extraído sempre que o módulo é executado.  
  
**Resolução:** A mensagem de erro fornece uma lista dos arquivos encontrados no arquivo. zip. Copie o nome do arquivo desejado e cole-o **conjunto de dados para desempacotar** caixa de texto.  
  
|Mensagens de Exceção|  
|------------------------|  
|Arquivo zip contém vários arquivos; Você deve especificar o arquivo para expandir.|  
|O arquivo contém mais de um arquivo. Especifique o arquivo para expandir. Foram encontrados os seguintes arquivos: {0}|  
  

## <a name="error-0133"></a>Erro 0133  
 O arquivo especificado não foi encontrado no arquivo zip  
  
 Esse erro é gerado quando o nome de arquivo inserido na **conjunto de dados para desempacotar** campo da **propriedade** painel não coincide com o nome de qualquer arquivo encontrado no arquivo. zip. As causas mais comuns desse erro são um erro de digitação ou procurar o arquivo errado para o arquivo expandir.  
  
**Resolução:** Examine o módulo. Se o nome do arquivo que você pretende descompactar aparece na lista de arquivos encontrados, copie o nome do arquivo e cole-o na **conjunto de dados para desempacotar** caixa da propriedade. Se você não vir o nome de arquivo desejado na lista, verifique se que você tenha o arquivo. zip correto e o nome correto para o arquivo desejado.  
  
|Mensagens de Exceção|  
|------------------------|  
|O arquivo especificado não foi encontrado o arquivo zip de int.|  
|O arquivo especificado não foi encontrado. Encontrar os arquivos a seguir: {0}|  
  

## <a name="error-0134"></a>Erro 0134
Ocorrerá uma exceção quando a coluna de rótulo está ausente ou tem um número insuficiente de linhas rotuladas.  
  
Esse erro ocorre quando o módulo requer uma coluna de rótulo, mas você não incluiu um na seleção de coluna ou a coluna de rótulo não tem muitos valores.

Esse erro também pode ocorrer quando uma operação anterior altera o conjunto de dados, de modo que as linhas insuficientes estão disponíveis para uma operação de downstream. Por exemplo, suponha que você use uma expressão na **partição e exemplo** módulo dividir um conjunto de dados por valores. Se nenhuma correspondência for encontrada para a expressão, um dos conjuntos de dados resultante da partição estariam vazio.

Resolução: 

 Se você incluir uma coluna de rótulo da seleção de coluna, mas ele não for reconhecido, use o [editar metadados](edit-metadata.md) módulo para marcá-la como uma coluna de rótulo.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Em seguida, você pode usar o [limpar dados ausentes](clean-missing-data.md) módulo para remover linhas com valores ausentes na coluna rótulo. 

 Verifique seus conjuntos de dados de entrada para certificar-se de que eles contenham dados válidos e linhas suficientes para atender aos requisitos da operação. Muitos algoritmos irá gerar uma mensagem de erro se eles exigem algum número mínimo de linhas de dados, mas os dados contiverem apenas algumas linhas ou apenas um cabeçalho.
  
|Mensagens de Exceção|
|------------------------|
|Ocorrerá uma exceção quando a coluna de rótulo está ausente ou tem um número insuficiente de linhas rotuladas.|  
|Ocorrerá uma exceção quando a coluna de rótulo está ausente ou tem menos de {0} linhas rotuladas|  
  

## <a name="error-0135"></a>Erro 0135  
 Há suporte para apenas com base em centroide do cluster.  
  
**Resolução:** Você pode encontrar essa mensagem de erro se você tentar avaliar um modelo de clustering com base em um algoritmo de clustering personalizado que não usa de centroides para inicializar o cluster.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Há suporte para apenas com base em centroide do cluster.|  
  

## <a name="error-0136"></a>Erro 0136  
 Nenhum nome de arquivo foi retornado; não é possível processar o arquivo como resultado.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Nenhum nome de arquivo foi retornado; não é possível processar o arquivo como resultado.|  
  

## <a name="error-0137"></a>Erro 0137  
 SDK de armazenamento do Azure encontrou um erro ao converter entre as propriedades da tabela e colunas do conjunto de dados durante a leitura ou gravação.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Erro de conversão entre as colunas de propriedade e o conjunto de dados do armazenamento de tabela do Azure.|  
|Erro de conversão entre as colunas de propriedade e o conjunto de dados do armazenamento de tabela do Azure. Informações adicionais: {0}|  

## <a name="error-0138"></a>Error 0138  
 Memória esgotada, não é possível para a execução completa do módulo. Reduzindo o conjunto de dados pode ajudar a atenuar o problema.  
  
 Esse erro ocorre quando o módulo que está sendo executado requer mais memória do que está disponível no contêiner do Azure. Isso pode acontecer se você estiver trabalhando com um grande conjunto de dados e a operação atual não couber na memória.  
  
**Resolução:** Se você está tentando ler um grande conjunto de dados e a operação não pode ser concluída, reduzindo o conjunto de dados pode ajudar.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Mensagens de Exceção|  
|------------------------|  
|Memória esgotada, não é possível para a execução completa do módulo.|  
  

## <a name="error-0139"></a>Erro 0139  
 Ocorrerá uma exceção quando não é possível converter uma coluna em outro tipo.  
  
 Esse erro no Azure Machine Learning ocorre quando você tentar converter uma coluna em um tipo de dados diferentes, mas que o tipo não é suportado pela operação atual ou pelo módulo.  
  
 O erro também pode aparecer quando um módulo tenta converter implicitamente os dados para atender aos requisitos do módulo atual, mas a conversão não for possível.  
  
**Resolução:**

1. Examine os dados de entrada e determinar o tipo de dados exato da coluna que você deseja usar e o tipo de dados da coluna que está produzindo o erro. Às vezes, você pode pensar que o tipo de dados está correto, mas encontrar o que uma operação de upstream tiver modificado o tipo de dados ou o uso de uma coluna. Use o [editar metadados](edit-metadata.md) módulo para redefinir os metadados de coluna para seu estado original. 
2. Examine a página de Ajuda do módulo para verificar os requisitos para a operação especificada. Determine quais tipos de dados são suportados pelo módulo atual, e o intervalo de valores é suportado. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Considere se é possível converter ou converter a coluna para um tipo de dados diferente. Todos os seguintes módulos fornecem considerável flexibilidade e poder para modificação de dados: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Executar Script Python](execute-python-script.md).  

> [!NOTE]
> Ainda não estiver funcionando? Recomenda-se de fornecer comentários adicionais sobre o problema, para nos ajudar a desenvolver a orientação para a melhor solução. Basta enviar comentários nesta página e forneça o nome do módulo que gerou o erro e a conversão de tipo de dados que falhou.
  
|Mensagens de Exceção|  
|------------------------|  
|Conversão não permitida.|  
|Não foi possível converter: {0}.|  
|Não foi possível converter: {0}, na linha {1}.|  
|Não foi possível converter a coluna do tipo {0} a coluna do tipo {1} na linha {2}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna do tipo {1} na linha {3}.|  
|Não foi possível converter a coluna "{2}" do tipo {0} a coluna "{3}" do tipo {1} na linha {4}.| 

## <a name="error-0140"></a>Erro 0140  
 Ocorrerá uma exceção se passado argumento de conjunto de coluna não contém outras colunas, exceto a coluna de rótulo.  
  
 Esse erro ocorre se você conectou um conjunto de dados a um módulo que requer várias colunas, incluindo recursos, mas você forneceu apenas a coluna de rótulo.  
  
**Resolução:** Escolha pelo menos uma coluna de recurso para incluir no conjunto de dados.  
  
|Mensagens de Exceção|  
|------------------------|  
|Conjunto de colunas especificado não contém outras colunas, exceto a coluna de rótulo.|  
  

## <a name="error-0141"></a>Erro 0141  
 Ocorrerá uma exceção se o número das colunas numéricas selecionadas e valores exclusivos na categórica e colunas de cadeia de caracteres é muito pequeno.  
  
 Esse erro no Azure Machine Learning ocorre quando não há valores suficientes exclusivos na coluna selecionada para executar a operação.  
  
**Resolução:** Algumas operações executam operações estatísticas no recurso e colunas categóricas, e se não há valores suficientes, a operação pode falhar ou retornar um resultado inválido. Verifique seu conjunto de dados para ver quantos valores existem nas colunas de rótulo e de recurso e determinar se a operação que você está tentando executar é estatisticamente válida.  
  
 Se o conjunto de dados de origem for válido, você também pode verificar se alguma operação de metadados ou de manipulação de dados upstream tem os dados alterados e removidos alguns valores.  
  
 Se as operações de upstream incluem a divisão, amostragem ou reamostragem, verifique se as saídas contêm o número esperado de linhas e valores.  
  
|Mensagens de Exceção|  
|------------------------|  
|O número das colunas numéricas selecionadas e valores exclusivos na categórica e colunas de cadeia de caracteres é muito pequeno.|  
|O número total das colunas numéricas selecionadas e valores exclusivos na categórica e colunas de cadeia de caracteres (atualmente {0}) deve ser pelo menos {1}|  
  

## <a name="error-0142"></a>Erro 0142  
 Ocorrerá uma exceção quando o sistema não é possível carregar o certificado para autenticar.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível carregar o certificado.|  
|O certificado {0} não pode ser carregado. Sua impressão digital é {1}.|  
  

## <a name="error-0143"></a>Erro 0143  
 Não é possível analisar a URL fornecida pelo usuário que deve para ser do GitHub.  
  
 Esse erro no Azure Machine Learning ocorre quando você especificar uma URL inválida e o módulo requer uma URL válida do GitHub.  
  
**Resolução:** Verifique se que a URL se refere a um repositório GitHub válido. Não há suporte para outros tipos de site.  
  
|Mensagens de Exceção|  
|------------------------|  
|URL não é do github.com.|  
|URL não é do github.com: {0}|  

## <a name="error-0144"></a>Erro 0144  
 Url do GitHub fornecido pelo usuário está ausentes na parte esperada.  
  
 Esse erro no Azure Machine Learning ocorre quando você especifica uma fonte de arquivo do GitHub usando o formato de URL inválido.  
  
**Resolução:** Verifique se a URL do repositório do GitHub é válida e termina com \blob\ ou \tree\\.  
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível analisar a URL do GitHub.|  
|Não é possível analisar a URL do GitHub (esperando ' \blob\\' ou ' \tree\\' após o nome do repositório): {0}|  

## <a name="error-0145"></a>Erro 0145  
 Não é possível criar o diretório de replicação por algum motivo.  
  
 Esse erro no Azure Machine Learning ocorre quando o módulo Falha ao criar o diretório especificado.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível criar o diretório de replicação.|  
  

## <a name="error-0146"></a>Erro 0146  
 Quando os arquivos de usuário são descompactados no diretório local, o caminho combinado pode ser muito longo.  
  
 Esse erro no Azure Machine Learning ocorre quando você está extraindo arquivos, mas alguns nomes de arquivo são muito longos quando descompactado.  
  
**Resolução:** Edite os nomes de arquivo, de modo que combinado do caminho e nome do arquivo é não mais de 248 caracteres.  
  
|Mensagens de Exceção|  
|------------------------|  
|Caminho de replicação é maior do que 248 caracteres, diminua o nome do script ou o caminho.|  

## <a name="error-0147"></a>Erro 0147  
 Não foi possível baixar coisas do GitHub, por algum motivo  
  
 Esse erro no Azure Machine Learning ocorre quando você não pode ler ou baixar os arquivos especificados do GitHub.  
  
**Resolução:** O problema pode ser temporário. Você pode tentar acessar os arquivos em outro momento. Ou verifique se que você tenha as permissões necessárias e que a fonte é válida.  
  
|Mensagens de Exceção|  
|------------------------|  
|Erro de acesso do GitHub.|  
|Erro de acesso do GitHub. [https://doi.org/10.13012/J8PN93H8]({0})|  
  

## <a name="error-0148"></a>Erro 0148  
 Problemas de acesso não autorizado durante a extração de dados ou criação de diretório.  
  
 Esse erro no Azure Machine Learning ocorre quando você está tentando criar um diretório ou ler dados de armazenamento, mas não tem as permissões necessárias.  
  
**Resolução:**
  
|Mensagens de Exceção|  
|------------------------|  
|Exceção de acesso não autorizado ao extrair os dados.|  
  

## <a name="error-0149"></a>Erro 0149  
 O arquivo de usuário não existe dentro do pacote do GitHub.  
  
 Esse erro no Azure Machine Learning ocorre quando o arquivo especificado não pode ser encontrado.  
  
Resolução: 
  
|Mensagens de Exceção|  
|------------------------|  
|Arquivo do GitHub não for encontrado.|  
|Arquivo do GitHub não for encontrado.: {0}|  
  

## <a name="error-0150"></a>Erro 0150  
 Os scripts que vêm do pacote de usuário não pôde ser descompactados, provavelmente devido a uma colisão com arquivos do GitHub.  
  
 Esse erro no Azure Machine Learning ocorre quando um script não pode ser extraído, normalmente quando há um arquivo existente de mesmo nome.  
  
Resolução:
  
|Mensagens de Exceção|  
|------------------------|  
|Não é possível descompactar o pacote; colisão de nomes possíveis com os arquivos do GitHub.|  
  

## <a name="error-0151"></a>Erro 0151  
 Ocorreu um erro ao gravar no armazenamento em nuvem. Verifique a URL.  
  
 Esse erro no Azure Machine Learning ocorre quando o módulo tenta gravar dados no armazenamento em nuvem, mas a URL é inválido ou não está disponível.  
  
Resolução: Verifique a URL e verificar se ele é gravável.  
  
|Mensagens de Exceção|  
|------------------------|  
|Erro ao gravar no armazenamento (possivelmente uma url incorreta) em nuvem.|  
|Erro ao gravar no armazenamento em nuvem: {0}. Verifique a url.|  
  
## <a name="error-0152"></a>Erro 0152  
 O tipo de nuvem do Azure foi especificado incorretamente no contexto do módulo.  
  
|Mensagens de exceção|  
|------------------------|  
|Tipo incorreto de nuvem do Azure|  
|Tipo incorreto de nuvem do Azure: {0}|  
  
## <a name="error-0153"></a>Erro 0153  
 O ponto de extremidade de armazenamento especificado é inválido.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo incorreto de nuvem do Azure|  
|Ponto de extremidade de armazenamento ruim: {0}|  

## <a name="error-0154"></a>Error 0154  
 O nome do servidor especificado não pôde ser resolvido  
  
|Mensagens de Exceção|  
|------------------------|  
|O nome do servidor especificado não pôde ser resolvido|  
|O servidor especificado {0}. documents.azure.com não pôde ser resolvido|

## <a name="error-0155"></a>Erro 0155  
 O cliente de DocDb gerou uma exceção  
  
|Mensagens de Exceção|  
|------------------------|  
|O cliente de DocDb gerou uma exceção|  
|Cliente de DocDb: {0}|

## <a name="error-0156"></a>Erro 0156  
 Resposta inválida do servidor HCatalog.  
  
|Mensagens de Exceção|  
|------------------------|  
|Resposta inválida do servidor HCatalog. Verifique se todos os serviços estão em execução.|  
|Resposta inválida do servidor HCatalog. Verifique se todos os serviços estão em execução. Detalhes do erro: {0}|

## <a name="error-0157"></a>Erro 0157  
 Ocorreu um erro ao ler do Azure Cosmos DB devido a esquemas inconsistentes ou de outro documento. Leitor requer que todos os documentos para ter o mesmo esquema.  
  
|Mensagens de Exceção|  
|------------------------|  
|Documentos detectados com esquemas diferentes. Verifique se que todos os documentos têm o mesmo esquema|

## <a name="error-1000"></a>Erro 1000  
Exceção de biblioteca interna.  
  
Esse erro é fornecido capturar, caso contrário, erros de mecanismo interno sem tratamento. Portanto, a causa desse erro pode ser diferente dependendo do módulo que gerou o erro.  
  
Para obter mais ajuda, é recomendável que você postar a mensagem detalhada que acompanha o erro para o fórum do Azure Machine Learning, juntamente com uma descrição do cenário, incluindo os dados usados como entradas. Esses comentários nos ajudarão a priorizar a erros e identificar os problemas mais importantes para continuar trabalhando.  
  
|Mensagens de Exceção|  
|------------------------|  
|Exceção de biblioteca.|  
|Exceção de biblioteca: {0}|  
|{0} Exceção de biblioteca: {1}|  
