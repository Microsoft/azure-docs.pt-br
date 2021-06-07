---
title: Solucionar problemas de erros do módulo do designer
titleSuffix: Azure Machine Learning
description: Saiba como você pode ler e solucionar problemas de códigos de erro de módulo automatizado no designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: b917e3fc93c59de85c5236c18e31d7bbc9d891f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98065466"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>Exceções e códigos de erro para o designer

Este artigo descreve as mensagens de erro e os códigos de exceção no designer de Azure Machine Learning para ajudá-lo a solucionar problemas de pipelines do Machine Learning.

Você pode encontrar a mensagem de erro no designer seguindo estas etapas:  

- Selecione o módulo com falha, acesse a guia **Saídas + logs**. Você pode encontrar o log detalhado no arquivo **70_driver_log.txt** na categoria **azureml-logs**.

- Para obter um erro de módulo detalhado, você pode verificá-lo no error_info.json na categoria **module_statistics**.

Veja a seguir códigos de erro de módulos no designer.

## <a name="error-0001"></a>Erro 0001  
 Ocorrerá uma exceção se uma ou mais das colunas especificadas do conjunto de dados não puder ser encontrada.  

 Você receberá esse erro se uma seleção de coluna for feita para um módulo, mas as colunas selecionadas não existirem no conjunto de dados de entrada. Esse erro poderá ocorrer se você tiver digitado manualmente um nome de coluna ou se o seletor de coluna tiver fornecido uma coluna sugerida que não existia no conjunto de dados quando você executou o pipeline.  

**Resolução:** reveja o módulo gerando essa exceção e valide se o nome ou os nomes da coluna estão corretos e se todas as colunas referenciadas existem.  

|Mensagens de Exceção|
|------------------------|
|Uma ou mais colunas especificadas não foram encontradas.|
|A coluna com o nome ou o índice "{column_id}" não foi encontrada.|
|A coluna com o nome ou índice "{column_id}" não existe em "{arg_name_missing_column}".|
|A coluna com o nome ou índice "{column_id}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|
|Colunas com o nome ou índice "{column_names}" não foram encontradas.|
|Colunas com o nome ou índice "{column_names}" não existem em "{arg_name_missing_column}".|
|A coluna com o nome ou índice "{column_names}" não existe em "{arg_name_missing_column}", mas existe em "{arg_name_has_column}".|


## <a name="error-0002"></a>Erro 0002  
 Ocorrerá uma exceção se um ou mais parâmetros não puder ser analisado ou convertido do tipo especificado no exigido pelo tipo de método de destino.  

 Esse erro ocorre no Azure Machine Learning quando você especifica um parâmetro como entrada e o tipo de valor é diferente do tipo esperado e a conversão implícita não pode ser executada.  

**Resolução:** verifique os requisitos do módulo e determine qual tipo de valor é necessário (cadeia de caracteres, inteiro, duplo etc.)  

|Mensagens de Exceção|
|------------------------|
|Falha ao analisar o parâmetro.|
|Falha ao analisar o parâmetro "{arg_name_or_column}".|
|Falha ao converter o parâmetro "{arg_name_or_column}" em "{to_type}".|
|Falha ao converter o parâmetro "{arg_name_or_column}" de "{from_type}" em "{to_type}".|
|Falha ao converter o valor "{arg_value}" do parâmetro "{arg_name_or_column}" de "{from_type}" em "{to_type}".|
|Falha ao converter o valor "{arg_value}" na coluna "{arg_name_or_column}" de "{from_type}" em "{to_type}" com o uso do formato "{fmt}" fornecido.|


## <a name="error-0003"></a>Erro 0003  
 Ocorrerá uma exceção se uma ou mais das entradas for nula ou estiver vazia.  

 Você receberá esse erro no Azure Machine Learning se as entradas ou os parâmetros para um módulo forem nulos ou vazios.  Esse erro pode ocorrer, por exemplo, quando você não digitou nenhum valor para um parâmetro. Isso também poderá acontecer se você escolher um conjunto de dados que tem valores ausentes ou um conjunto de dados vazio.  

**Resolução:**

+ abra o módulo que produziu a exceção e verifique se todas as entradas foram especificadas. Verifique se todas as entradas necessárias foram especificadas. 
+ Verifique se os dados carregados do armazenamento do Azure estão acessíveis e se o nome ou a chave da conta não foi alterado.  
+ Verifique os dados de entrada quanto a valores ausentes ou nulos.
+ Se estiver usando uma consulta em uma fonte de dados, verifique se os dados estão sendo retornados no formato esperado. 
+ Verifique se há erros de digitação ou outras alterações na especificação de dados.
  
|Mensagens de Exceção|
|------------------------|
|Uma ou mais entradas são nulas ou estão vazias.|
|A entrada "{name}" é nula ou está vazia.|


## <a name="error-0004"></a>Erro 0004  
 Ocorrerá uma exceção se o parâmetro for inferior ou igual ao valor específico.  

 Você receberá esse erro no Azure Machine Learning se o parâmetro na mensagem for inferior que um valor limite necessário para o módulo processar os dados.  

**Resolução:** reveja o módulo gerando a exceção e modifique o parâmetro para que ele seja maior que o valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior que o valor do limite.|
|O valor do parâmetro "{arg_name}" deve ser maior que {lower_boundary}.|
|O parâmetro "{arg_name}" tem o valor "{actual_value}" que deve ser maior que {lower_boundary}.|


## <a name="error-0005"></a>Erro 0005  
 Ocorrerá uma exceção se o parâmetro for menor que um valor específico.  

 Você receberá esse erro no Azure Machine Learning se o parâmetro na mensagem for inferior ou igual a um valor limite necessário para o módulo processar os dados.  

**Resolução:** reveja o módulo gerando a exceção e modifique o parâmetro para que ele seja maior ou igual ao valor especificado.  

|Mensagens de Exceção|
|------------------------|
|O parâmetro deve ser maior ou igual ao valor do limite.|
|O valor do parâmetro "{arg_name}" deve ser maior ou igual a {lower_boundary}.|
|O parâmetro "{arg_name}" tem o valor "{value}" que deve ser maior ou igual a {lower_boundary}.|


## <a name="error-0006"></a>Erro 0006  
 Ocorrerá uma exceção se o parâmetro for maior ou igual ao valor especificado.  

 Você receberá esse erro no Azure Machine Learning se o parâmetro na mensagem for superior ou igual a um valor limite necessário para o módulo processar os dados.  

**Resolução:** reveja o módulo gerando a exceção e modifique o parâmetro para que ele seja menor que o valor especificado.  

|Mensagens de Exceção|
|------------------------|
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser menor que o outro.|
|O valor do parâmetro "{arg_name}" deve ser menor que o valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem o valor "{value}" que deve ser menor que {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>Erro 0007  
 Ocorrerá uma exceção se o parâmetro for maior que um valor específico.  

 Você receberá esse erro no Azure Machine Learning se, nas propriedades do módulo, você tiver especificado um valor maior que o permitido. Por exemplo, você pode especificar um dado que está fora do intervalo de datas com suporte ou indicar que cinco colunas serão usadas quando apenas três colunas estiverem disponíveis. 

 Você também poderá ver esse erro se estiver especificando dois conjuntos de dados que precisam corresponder de alguma forma. Por exemplo, se você estiver renomeando colunas e especificá-las por índice, o número de nomes que você fornecer deverá corresponder ao número de índices de coluna. Outro exemplo pode ser uma operação matemática que usa duas colunas, em que elas devem ter o mesmo número de linhas. 

**Resolução:**

 + abra o módulo em questão e examine as configurações de propriedade numérica.
 + Verifique se os valores de parâmetro estão dentro do intervalo de valores com suporte para essa propriedade.
 + Se o módulo usar várias entradas, verifique se elas têm o mesmo tamanho.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Verifique se houve alterações no conjunto de dados ou na fonte de dados. Às vezes, um valor que funcionava com uma versão anterior dos dados falhará depois que o número de colunas, os tipos de dados da coluna ou o tamanho dos dados forem alterados.  

|Mensagens de exceção|
|------------------------|
|Incompatibilidade de parâmetros. Um dos parâmetros deve ser inferior ou igual a outro.|
|O valor do parâmetro "{arg_name}" deve ser inferior ou igual ao valor do parâmetro "{upper_boundary_parameter_name}".|
|O parâmetro "{arg_name}" tem o valor "{actual_value}" que deve ser inferior ou igual a {upper_boundary}.|
|O valor {actual_value} do parâmetro "{arg_name}" deve ser inferior ou igual ao valor {upper_boundary} do parâmetro "{upper_boundary_parameter_name}".|
|O valor {actual_value} do parâmetro "{arg_name}" deve ser inferior ou igual ao valor {upper_boundary} do parâmetro {upper_boundary_meaning}.|


## <a name="error-0008"></a>Erro 0008  
 Ocorrerá uma exceção se o parâmetro não estiver no intervalo.  

 Você receberá esse erro no Azure Machine Learning se o parâmetro na mensagem estiver fora dos limites necessários para o módulo processar os dados.  

 Por exemplo, esse erro será exibido se você tentar usar [Adicionar Linhas](add-rows.md) para combinar dois conjuntos de valores que têm um número diferente de colunas.  

**Resolução:** reveja o módulo gerando a exceção e modifique o parâmetro para que ele esteja dentro do intervalo especificado.  

|Mensagens de Exceção|
|------------------------|
|O valor do parâmetro não está no intervalo especificado.|
|O valor do parâmetro "{arg_name}" não está no intervalo.|
|O valor do parâmetro "{arg_name}" deve estar no intervalo [{lower_boundary}, {upper_boundary}].|
|O valor do parâmetro "{arg_name}" não está no intervalo. {reason}|


## <a name="error-0009"></a>Erro 0009  
 Ocorrerá uma exceção quando o nome da conta ou o nome do contêiner de armazenamento do Azure for especificado incorretamente.  

Esse erro ocorre no designer do Azure Machine Learning quando você especifica parâmetros para uma conta de armazenamento do Azure, mas o nome ou a senha não podem ser resolvidos. Erros em senhas ou em nomes de conta podem ocorrer por vários motivos:

 + A conta é do tipo incorreto. Não há suporte para alguns novos tipos de conta para uso com o designer do Machine Learning. Confira [Importar dados](import-data.md) para obter detalhes.
 + Você inseriu o nome da conta incorreto
 + A conta não existe mais
 + A senha da conta de armazenamento está incorreta ou foi alterada
 + Você não especificou o nome do contêiner ou o contêiner não existe
 + Você não especificou totalmente o caminho do arquivo (caminho para o blob)
   

**Resolução:**

esses problemas geralmente ocorrem quando você tenta inserir manualmente o nome da conta, a senha ou o caminho do contêiner. Recomendamos usar o novo assistente para o módulo [Importar Dados](import-data.md), que ajuda você a pesquisar e verificar nomes.

Verifique também se a conta, o contêiner ou o blob foi excluído. Use outro utilitário de armazenamento do Azure para verificar se o nome da conta e a senha foram inseridos corretamente e se o contêiner existe. 

Alguns tipos de conta mais recentes não são compatíveis com o Azure Machine Learning. Por exemplo, os novos tipos de armazenamento “quente” ou “frio” não podem ser usados para machine learning. As contas de armazenamento clássicas e as contas de armazenamento criadas como "uso geral" funcionam bem.

Se o caminho completo para um blob tiver sido especificado, verifique se o caminho foi especificado como **contêiner/nome do blob** e se o contêiner e o blob existem na conta.  

 O caminho não deve conter uma barra à esquerda. Por exemplo **/contêiner/blob** está incorreto e deve ser inserido como **contêiner/blob**.  


|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento do Azure ou o nome do contêiner está incorreto.|
|O nome da conta de armazenamento do Azure "{account_name}" ou o nome do contêiner "{container_name}" está incorreto; era esperado um nome de contêiner do formato contêiner/blob.|


## <a name="error-0010"></a>Erro 0010  
 Ocorrerá uma exceção se os conjuntos de dados da entrada tiverem nomes de colunas que deveriam corresponder, mas não correspondem.  

 Você receberá esse erro no Azure Machine Learning se o índice da coluna na mensagem tiver nomes de coluna diferentes em dois conjuntos de dados de entrada.  

**Resolução:** use [Editar metadados](edit-metadata.md) ou modifique o conjunto de dados original para ter o mesmo nome da coluna para o índice de coluna especificado.  

|Mensagens de Exceção|
|------------------------|
|As colunas com índice correspondente em conjuntos de dados de entrada têm nomes diferentes.|
|Os nomes de coluna não são os mesmos para a coluna {col_index} (baseada em zero) dos conjuntos de dados de entrada ({dataset1} e {dataset2}, respectivamente).|


## <a name="error-0011"></a>Erro 0011  
 Ocorrerá uma exceção se o argumento de conjunto de colunas passado não se aplicar a nenhuma das colunas do conjunto de dados.  

 Você receberá esse erro no Azure Machine Learning se a seleção de coluna especificada não corresponder a nenhuma das colunas no conjunto de dados fornecido.  

 Você também poderá obter esse erro se não tiver selecionado uma coluna e pelo menos uma coluna for necessária para que o módulo funcione.  

**Resolução:** modifique a seleção de coluna no módulo para que ela seja aplicada às colunas no conjunto de dados.  

 Se o módulo exigir que você selecione uma coluna específica, como uma coluna de rótulo, verifique se a coluna à direita está selecionada.  

 Se forem selecionadas colunas inadequadas, remova-as e execute novamente o pipeline.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas especificado não se aplica a nenhuma das colunas de conjunto de dados.|
|O conjunto de colunas especificado "{column_set}" não se aplica a nenhuma das colunas do conjunto de dados.|


## <a name="error-0012"></a>Erro 0012  
 Ocorrerá uma exceção se não tiver sido possível criar a instância da classe com o conjunto de argumentos passado.  

**Resolução:** esse erro não é acionável pelo usuário e será preterido em uma versão futura.  

|Mensagens de Exceção|
|------------------------|
|Modelo não treinado; treine primeiro o modelo.|
|Modelo não treinado ({arg_name}); use o modelo treinado.|


## <a name="error-0013"></a>Erro 0013  
 A exceção ocorrerá se o aprendizado passado para o módulo for um tipo inválido.  

 Esse erro ocorre sempre que um modelo treinado é incompatível com o módulo de pontuação conectado. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Resolução:**

determine o tipo de aprendiz produzido pelo módulo de treinamento e o módulo de pontuação apropriado para o aprendiz. 

Se o modelo tiver sido treinado usando qualquer um dos módulos de treinamento especializados, conecte o modelo treinado somente ao módulo de pontuação especializado correspondente. 


|Tipo de modelo|Módulo de treinamento| Módulo de pontuação|
|----|----|----|
|qualquer classificador|[Treinar Modelo](train-model.md) |[Pontuar Modelo](score-model.md)|
|qualquer modelo de regressão|[Treinar Modelo](train-model.md) |[Pontuar Modelo](score-model.md)|

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
|O aprendiz de tipo inválido foi passado.|
|O aprendiz "{arg_name}" tem um tipo inválido.|
|O aprendiz "{arg_name}" tem um tipo "{learner_type}" inválido.|
|O aprendiz de tipo inválido foi passado. Mensagem de exceção: {exception_message}|


## <a name="error-0014"></a>Erro 0014  
 Ocorrerá uma exceção se a contagem de valores exclusivos da coluna for maior que o permitido.  

 Esse erro ocorre quando uma coluna contém um número excessivo de valores exclusivos, como uma coluna de ID ou coluna de texto. Você poderá ver esse erro se especificar que uma coluna seja tratada como dados categóricos, mas há muitos valores exclusivos na coluna para permitir que o processamento seja concluído. Você também poderá ver esse erro se houver uma incompatibilidade entre o número de valores exclusivos em duas entradas.   

O erro de valores exclusivos será maior que o permitido se atender às **duas** condições a seguir:

- Mais de 97% instâncias de uma coluna são valores exclusivos, o que significa que quase todas as categorias são diferentes umas das outras.
- Uma coluna tem mais de 1000 valores exclusivos.

**Resolução:**

abra o módulo que gerou o erro e identifique as colunas usadas como entradas. Para alguns módulos, você pode clicar com o botão direito do mouse na entrada do conjunto de dados e selecionar **Visualizar** para obter estatísticas em colunas individuais, incluindo o número de valores exclusivos e a distribuição.

Para as colunas que você pretende usar para agrupamento ou categorização, execute as etapas para reduzir o número de valores exclusivos em colunas. Você pode reduzir de maneiras diferentes, dependendo do tipo de dados da coluna. 

Para colunas de ID que não são recursos significativos durante o treinamento de um modelo, você pode usar [Editar metadados](../algorithm-module-reference/edit-metadata.md) para marcar essa coluna como **recurso claro** e ela não será usada durante o treinamento de um modelo. 

Para colunas de texto, você pode usar o [hash de recurso](../algorithm-module-reference/feature-hashing.md) ou [extrair recursos de N-Gram do módulo de texto](../algorithm-module-reference/extract-n-gram-features-from-text.md) para pré-processar colunas de texto.
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Não foi possível encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer comentários sobre esse tópico que inclua o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer etapas de solução de problemas mais direcionadas para cenários comuns.   

|Mensagens de Exceção|
|------------------------|
|A quantidade de valores exclusivos da coluna é maior que o permitido.|
|O número de valores exclusivos na coluna: "{column_name}" é maior que o permitido.|
|O número de valores exclusivos na coluna: "{column_name}" excede a contagem de tupla de {limitation}.|


## <a name="error-0015"></a>Erro 0015  
 Ocorrerá uma exceção se a conexão de banco de dados tiver falhado.  

 Você receberá esse erro se inserir um nome de conta SQL, uma senha, um servidor de banco de dados ou nome de banco de dados incorretamente ou se uma conexão com o banco de dados não puder ser estabelecida devido a problemas com o banco de dados ou o servidor.  

**Resolução:** verifique se o nome da conta, a senha, o servidor de banco de dados e o banco de dados foram inseridos corretamente e se a conta especificada tem o nível correto de permissões. Verifique se o banco de dados está acessível no momento.  

|Mensagens de Exceção|
|------------------------|
|Erro ao estabelecer a conexão de banco de dados.|
|Erro ao estabelecer a conexão de banco de dados: {connection_str}.|


## <a name="error-0016"></a>Erro 0016  
 Ocorrerá uma exceção se os conjuntos de dados de entrada passados para o módulo devessem ter tipos de coluna compatíveis, o que não acontece.  

 Você receberá esse erro no Azure Machine Learning se os tipos das colunas passados em dois ou mais conjuntos de dados não forem compatíveis entre si.  

**Resolução:** use [Editar Metadados](edit-metadata.md) ou modifique o conjunto de dados de entrada original<!--, or use [Convert to Dataset](convert-to-dataset.md)--> para verificar se os tipos das colunas são compatíveis.  

|Mensagens de Exceção|
|------------------------|
|As colunas com índice correspondente em conjuntos de dados de entrada têm tipos incompatíveis.|
|As colunas '{first_col_names}' são incompatíveis entre os dados de treinamento e teste.|
|As colunas '{first_col_names}' e '{second_col_names}' são incompatíveis.|
|Os tipos de elemento de coluna não são compatíveis com a coluna '{first_col_names}' (baseada em zero) dos conjuntos de dados de entrada ({first_dataset_names} e {second_dataset_names}, respectivamente).|


## <a name="error-0017"></a>Erro 0017  
 Ocorrerá uma exceção se uma coluna selecionada usar um tipo de dados não compatível com o módulo atual.  

 Por exemplo, você poderá receber esse erro no Azure Machine Learning se a seleção de coluna incluir uma coluna com um tipo de dados que não puder ser processado pelo módulo, como uma coluna de cadeia de caracteres para uma operação matemática ou uma coluna de pontuação em que uma coluna de recurso categórico é necessária.  

**Resolução:**
 1. identifique a coluna que é o problema.
 2. Examine os requisitos do módulo.
 3. Modifique a coluna para que ela esteja em conformidade com os requisitos. Talvez seja necessário usar vários dos seguintes módulos para fazer alterações, dependendo da coluna e da conversão que você está tentando:
    + Use [Editar Metadados](edit-metadata.md) para alterar o tipo de dados de colunas ou para alterar o uso da coluna de recurso para numérico, categórico para não categórica e assim por diante.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Como último recurso, talvez seja necessário modificar o conjunto de dados de entrada original.

> [!TIP]
> Não foi possível encontrar uma resolução que corresponda ao seu cenário? Você pode fornecer comentários sobre esse tópico que inclua o nome do módulo que gerou o erro e o tipo de dados e a cardinalidade da coluna. Usaremos as informações para fornecer etapas de solução de problemas mais direcionadas para cenários comuns. 

|Mensagens de Exceção|
|------------------------|
|Não é possível processar a coluna do tipo atual. O tipo não é compatível com o módulo.|
|Não é possível processar a coluna do tipo {col_type}. O tipo não é compatível com o módulo.|
|Não é possível processar a coluna “{col_name}” do tipo {col_type}. O tipo não é compatível com o módulo.|
|Não é possível processar a coluna “{col_name}” do tipo {col_type}. O tipo não é compatível com o módulo. Nome do parâmetro: {arg_name}.|


## <a name="error-0018"></a>Erro 0018  
 Ocorrerá uma exceção se o conjunto de dados de entrada não for válido.  

**Resolução:** no Azure Machine Learning, esse erro pode aparecer em muitos contextos, portanto não há uma resolução. Em geral, o erro indica que os dados fornecidos como entrada para um módulo têm o número incorreto de colunas ou que o tipo de dados não corresponde aos requisitos do módulo. Por exemplo:  

-   o módulo requer uma coluna de rótulo, mas nenhuma coluna foi marcada como um rótulo ou você ainda não selecionou uma coluna de rótulo.  
  
-   O módulo requer que os dados sejam categóricos, mas seus dados são numéricos.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Os dados estão no formato incorreto.  
  
-   Os dados importados contêm caracteres inválidos, valores inválidos ou valores fora do intervalo.  
-   A coluna está vazia ou contém muitos valores ausentes.  

 Para determinar os requisitos e como seus dados podem ser, examine o tópico de ajuda para o módulo que consumirá o conjunto de dados como entrada.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados não é válido.|
|{dataset1} contém dados inválidos.|
|{dataset1} e {dataset2} devem ser consistentes com columnwise.|
|{dataset1} contém dados inválidos, {reason}.|
|{dataset1} contém {invalid_data_category}. {troubleshoot_hint}|
|{dataset1} não é válido, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>Erro 0019  
 Ocorrerá uma exceção se for esperado que a coluna contenha valores classificados, mas ela não contiver.  

 Você receberá esse erro no Azure Machine Learning se os valores de coluna especificados estiverem fora de ordem.  

**Resolução:** classifique os valores de coluna modificando manualmente o conjunto de dados de entrada e execute o módulo novamente.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna não estão classificados.|
|Os valores da coluna "{col_index}" não estão classificados.|
|Os valores da coluna "{col_index}" do conjunto de "{dataset}" não estão classificados.|
|Os valores do argumento "{arg_name}" não estão classificados na ordem "{sorting_order}".|


## <a name="error-0020"></a>Erro 0020  
 Ocorrerá uma exceção se o número de colunas em alguns dos conjuntos de dados transmitidos para o módulo for muito pequeno.  

 Você receberá esse erro no Azure Machine Learning se não houver colunas suficientes selecionadas para um módulo.  

**Resolução:** reveja o módulo e verifique se o seletor de coluna tem o número correto de colunas selecionado.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é menor que o mínimo permitido.|
|O número de colunas no conjunto de dados de entrada é menor que o mínimo permitido de coluna {required_columns_count}.|
|O número de colunas no conjunto de dados de entrada "{arg_name}" é menor que o mínimo permitido de colunas {required_columns_count}.|


## <a name="error-0021"></a>Erro 0021  
 Ocorrerá uma exceção se o número de linhas em alguns dos conjuntos de dados passados para o módulo for muito pequeno.  

 Esse erro é visto no Azure Machine Learning quando não há linhas suficientes no conjunto de dados para executar a operação especificada. Por exemplo, você poderá ver esse erro se o conjunto de dados de entrada estiver vazio ou se você estiver tentando executar uma operação que exige que um número mínimo de linhas seja válido. Essas operações podem incluir (entre outros) o agrupamento ou a classificação com base em métodos estatísticos, determinados tipos de compartimentalização e aprendizado com contagens.  

**Resolução:**

 + abra o módulo que retornou o erro e verifique as propriedades do conjunto de dados de entrada e do módulo. 
 + Verifique se o conjunto de dados de entrada não está vazio e se há linhas de dados suficientes para atender aos requisitos descritos na ajuda do módulo.  
 + Se os dados forem carregados de uma fonte externa, verifique se a fonte de dados está disponível e se não há erro ou alteração na definição de dados que faria o processo de importação obter menos linhas.
 + Se você estiver executando uma operação no dados upstream do módulo que possa afetar o tipo de dados ou o número de valores, como operações de limpeza, divisão ou junção, verifique as saídas dessas operações para determinar o número de linhas retornadas.  

|Mensagens de Exceção|
|------------------------|
|O número de linhas no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de linhas no conjunto de dados de entrada é menor que o mínimo permitido de {required_row_count} linhas.|
|O número de linhas no conjunto de dados de entrada é menor que o mínimo permitido de {required_row_count} linhas. {reason}|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é menor que o mínimo permitido de {required_rows_count} linhas.|
|O número de linhas no conjunto de dados de entrada "{arg_name}" é {actual_rows_count}, menor que o mínimo permitido de {required_row_count} linhas.|
|O número de linhas "{row_type}" no conjunto de dados de entrada "{arg_name}" é {actual_rows_count}, menos do que o mínimo permitido de {required_rows_count} linhas.|


## <a name="error-0022"></a>Erro 0022  
 Ocorrerá uma exceção se o número de colunas selecionadas no conjunto de dados de entrada não for igual ao número esperado.  

 No Azure Machine Learning, esse erro pode ocorrer quando o módulo ou a operação downstream requer um número específico de colunas ou entradas e você forneceu poucas ou muitas colunas ou entradas. Por exemplo:  

-   você especifica uma coluna de rótulo ou de chave e selecionou várias colunas acidentalmente.  
  
-   Você está renomeando colunas, mas forneceu mais ou menos nomes do que colunas.  
  
-   O número de colunas na origem ou destino mudou foi alterado ou não corresponde ao número de colunas usado pelo módulo.  
  
-   Você forneceu uma lista de valores separados por vírgula para entradas, mas o número de valores não corresponde ou não há suporte para várias entradas.  

**Resolução:** reveja o módulo e verifique a seleção de coluna para que o número correto de colunas esteja selecionado. Verifique as saídas dos módulos upstream e os requisitos das operações downstream.  

 Se você usou uma das opções de seleção de coluna que pode selecionar várias colunas (índices de coluna, todos os recursos, todas numéricas etc.), valide o número exato de colunas retornadas pela seleção.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Verifique se o número ou o tipo de colunas upstream não foi alterado.  

 Se você estiver usando um conjunto de dados de recomendação para treinar um modelo, lembre-se de que o recomendador espera um número limitado de colunas, que corresponde a pares de item de usuário ou classificações de item de usuário. Remova colunas adicionais antes de treinar o modelo ou dividir conjuntos de dados de recomendação. Para obter mais informações, confira [Dividir Dados](split-data.md).  

|Mensagens de Exceção|
|------------------------|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual ao número esperado.|
|O número de colunas selecionadas no conjunto de dados de entrada não é igual a {expected_col_count}.|
|O padrão de seleção de coluna "{selection_pattern_friendly_name}" fornece o número de colunas selecionadas no conjunto de dados de entrada diferente de {expected_col_count}.|
|Espera-se que o padrão de seleção de coluna "{selection_pattern_friendly_name}" forneça {expected_col_count} colunas selecionadas no conjunto de dados de entrada, mas, na verdade, {selected_col_count} colunas foi/foram fornecidas.|


## <a name="error-0023"></a>Erro 0023  

Ocorrerá uma exceção se a coluna de destino do conjunto de dados de entrada não for válida para o módulo atual do instrutor.  

No Azure Machine Learning, esse erro ocorrerá se a coluna de destino (conforme selecionado nos parâmetros do módulo) não for do tipo de dados válido, contiver todos os valores ausentes ou não tiver sido categórica como o esperado.  

**Resolução:** reveja a entrada do módulo para inspecionar o conteúdo da coluna de rótulo/destino. Verifique se ela não tem todos os valores ausentes. Se o módulo estiver esperando que a coluna de destino seja categórica, verifique se há mais de um valor distinto na coluna de destino.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados de entrada tem uma coluna de destino sem suporte.|
|O conjunto de dados de entrada tem uma coluna de destino "{column_index}" sem suporte.|
|O conjunto de dados de entrada tem uma coluna de destino "{column_index}" sem suporte para o aprendiz do tipo {learner_tpye}.|


## <a name="error-0024"></a>Erro 0024  
Ocorrerá uma exceção se o conjunto de dados não contiver uma coluna de rótulo.  

 No Azure Machine Learning, esse erro ocorre quando o módulo requer uma coluna de rótulo e o conjunto de dados não tem uma coluna de rótulo. Por exemplo, a avaliação de um conjunto de dados classificado geralmente requer que uma coluna de rótulo esteja presente para calcular métricas de precisão.  

Também pode ser provável que uma coluna de rótulo esteja presente no conjunto de dados, mas não seja detectada corretamente pelo Azure Machine Learning.

**Resolução:**

+ abra o módulo que gerou o erro e determine se uma coluna de rótulo está presente. O nome ou tipo de dados da coluna não importa, desde que ela contenha um resultado (ou variável dependente) que você esteja tentando prever. Se você não tiver certeza de qual coluna tem o rótulo, procure um nome genérico, como *classe* ou *destino*. 
+  Se o conjunto de dados não incluir uma coluna de rótulo, talvez a coluna de rótulo tenha sido removida upstream explícita ou acidentalmente. Também pode ser que o conjunto de dados não seja a saída de um módulo de pontuação upstream.
+ Para marcar explicitamente a coluna como a coluna de rótulo, adicione o módulo [Editar Metadados](edit-metadata.md) e conecte o conjunto de dados. Selecione apenas a coluna de rótulo e, em seguida, selecione **Rótulo** na lista suspensa **Campos**. 
+ Se a coluna errada for escolhida como o rótulo, você poderá selecionar **Limpar rótulo** em **Campos** para corrigir os metadados na coluna. 
  
|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de rótulo no conjunto de dados.|
|Não há nenhuma coluna de rótulo em "{dataset_name}".|


## <a name="error-0025"></a>Erro 0025  
 Ocorrerá uma exceção se o conjunto de dados não contiver uma coluna de pontuação.  

 No Azure Machine Learning, esse erro ocorrerá se a entrada do modelo de avaliação não contiver colunas de pontuação válidas. Por exemplo, o usuário tenta avaliar um conjunto de dados antes de ele ser pontuado com um modelo treinado correto ou a coluna de pontuação foi explicitamente descartada upstream. Essa exceção também ocorrerá se as colunas de pontuação nos dois conjuntos de dados forem incompatíveis. Por exemplo, você pode estar tentando comparar a precisão de um regressor linear com um classificador binário.  

**Resolução:** reveja a entrada do modelo de avaliação e examine se ela contém uma ou mais colunas de pontuação. Caso contrário, o conjunto de dados não terá sido pontuado ou as colunas de pontuação terão sido descartadas em um módulo upstream.  

|Mensagens de Exceção|
|------------------------|
|Não há nenhuma coluna de pontuação no conjunto de dados.|
|Não há nenhuma coluna de pontuação em "{dataset_name}".|
|Não há nenhuma coluna de pontuação em "{dataset_name}" produzida por um "{learner_type}". Pontuar um conjunto de dados usando o tipo correto de aprendiz.|


## <a name="error-0026"></a>Erro 0026  
 Ocorrerá uma exceção se não forem permitidas colunas com o mesmo nome.  

 No Azure Machine Learning, esse erro ocorrerá se várias colunas tiverem o mesmo nome. Uma forma de receber esse erro é se o conjunto de dados não tem uma linha de cabeçalho e os nomes de coluna são atribuídos automaticamente: Col0, Col1 etc.  

**Resolução:** se as colunas tiverem o mesmo nome, insira um módulo [Editar Metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo. Use o seletor de coluna em [Editar Metadados](edit-metadata.md) para selecionar colunas a serem renomeadas, digitando os novos nomes na caixa de texto **Novos nomes de coluna**.  

|Mensagens de Exceção|
|------------------------|
|Nomes de colunas iguais são especificados nos argumentos. Não são permitidos nomes de colunas iguais por módulo.|
|Não são permitidos nomes de coluna iguais "{arg_name_1}" e "{arg_name_2}". Especifique nomes diferentes.|


## <a name="error-0027"></a>Erro 0027  
 Ocorrerá uma exceção caso os dois objetos precisem ser do mesmo tamanho, mas não forem.  

 Esse é um erro comum no Azure Machine Learning e pode ser causado por muitas condições.  

**Resolução:** não há resolução específica. No entanto, você pode verificar se há condições como as seguintes:  

-   Se você estiver renomeando colunas, verifique se cada lista (as colunas de entrada e a lista de novos nomes) tem o mesmo número de itens.  
  
-   Se você estiver unindo ou concatenando dois conjuntos de dados, verifique se eles têm o mesmo esquema.  
  
-   Se você estiver unindo dois conjuntos de dados que têm várias colunas, verifique se as principais colunas têm o mesmo tipo de dados e selecione a opção **Permitir duplicatas e preservar a ordem de colunas na seleção**.  

|Mensagens de Exceção|
|------------------------|
|O tamanho dos objetos passados é inconsistente.|
|O tamanho de "{friendly_name1}" é inconsistente com o tamanho de "{friendly_name2}".|


## <a name="error-0028"></a>Erro 0028  
 Ocorrerá uma exceção caso o conjunto de colunas contenha nomes de colunas duplicados e isso não for permitido.  

 No Azure Machine Learning, esse erro ocorre quando os nomes de coluna são duplicados, ou seja, não exclusivos.  

**Resolução:** se as colunas tiverem o mesmo nome, adicione uma instância de [Editar Metadados](edit-metadata.md) entre o conjunto de dados de entrada e o módulo que está gerando o erro. Use o Seletor de Coluna em [Editar Metadados](edit-metadata.md) para selecionar colunas a serem renomeadas e digite os novos nomes na caixa de texto **Novos nomes de coluna**. Se estiver renomeando várias colunas, verifique se os valores digitados em **Novos nomes de coluna** são exclusivos.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de colunas contém nomes de coluna duplicados.|
|O nome "{duplicated_name}" está duplicado.|
|O nome "{duplicated_name}" está duplicado em "{arg_name}".|
|O nome "{duplicated_name}" está duplicado. Detalhes: {details}|


## <a name="error-0029"></a>Erro 0029  
 Ocorrerá uma exceção caso um URI inválido seja passado.  

 No Azure Machine Learning, esse erro ocorre caso um URI inválido seja passado.  Você receberá esse erro se qualquer uma das seguintes condições for verdadeira:  

-   O URI Público ou da SAS fornecido para o Armazenamento de Blobs do Azure para leitura ou gravação contém um erro.  
  
-   A janela de tempo para a SAS expirou.  
  
-   A URL da Web via origem HTTP representa um arquivo ou um URI de loopback.  
  
-   A URL da Web via HTTP contém uma URL formatada incorretamente.  
  
-   A URL não pode ser resolvida pela origem remota.  

**Resolução:** reveja o módulo e verifique o formato do URI. Se a fonte de dados for uma URL da Web via HTTP, verifique se a origem pretendida não é um arquivo ou um URI de loopback (localhost).  

|Mensagens de Exceção|
|------------------------|
|Um URI inválido foi passado.|
|O URI "{invalid_url}" é inválido.|


## <a name="error-0030"></a>Erro 0030  
 Ocorrerá uma exceção quando não for possível baixar um arquivo.  

 Essa exceção no Azure Machine Learning ocorre quando não é possível baixar um arquivo. Você receberá essa exceção quando uma tentativa de leitura de uma origem HTTP falhar após 3 (três) repetições.  

**Resolução:** verifique se o URI para a origem HTTP está correto e se o site está acessível no momento pela Internet.  

|Mensagens de Exceção|
|------------------------|
|Não é possível baixar um arquivo.|
|Erro ao baixar o arquivo: {file_url}.|


## <a name="error-0031"></a>Erro 0031  
 Ocorrerá uma exceção se o número de colunas no conjunto de colunas for menor que o necessário.  

 No Azure Machine Learning, esse erro ocorrerá se o número de colunas selecionado for menor que o necessário.  Você receberá esse erro se o número mínimo necessário de colunas não estiver selecionado.  

**Resolução:** adicione mais colunas à seleção de colunas usando o **Seletor de Coluna**.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de colunas é menor que o necessário.|
|Pelo menos {required_columns_count} colunas devem ser especificadas para o argumento de entrada "{arg_name}".|
|Pelo menos {required_columns_count} colunas devem ser especificadas para o argumento de entrada "{arg_name}". O número real de colunas especificadas é {input_columns_count}.|


## <a name="error-0032"></a>Erro 0032  
 Ocorrerá uma exceção se o argumento não for um número.  

 Você receberá esse erro no Azure Machine Learning se o argumento for double ou NaN.  

**Resolução:** modifique o argumento especificado para usar um valor válido.  

|Mensagens de Exceção|
|------------------------|
|O argumento não é um número.|
|"{arg_name}" não é um número.|


## <a name="error-0033"></a>Erro 0033  
 Ocorrerá uma exceção se o argumento for infinito.  

 No Azure Machine Learning, esse erro ocorrerá se o argumento for infinito. Você receberá esse erro se o argumento for `double.NegativeInfinity` ou `double.PositiveInfinity`.  

**Resolução:** modifique o argumento especificado para ser um valor válido.  

|Mensagens de Exceção|
|------------------------|
|O argumento deve ser finito.|
|"{arg_name}" não é finito.|
|A coluna "{column_name}" contém valores infinitos.|


## <a name="error-0034"></a>Erro 0034  
 Ocorrerá uma exceção se houver mais de uma classificação para um determinado par usuário-item.  

 No Azure Machine Learning, esse erro ocorrerá na recomendação se um par usuário-item tiver mais de um valor de classificação.  

**Resolução:** verifique se o par usuário-item tem apenas um valor de classificação.  

|Mensagens de Exceção|
|------------------------|
|Existe mais de uma classificação para os valores no conjunto de dados.|
|Mais de uma classificação para o usuário {user} e o item {item} na tabela de dados de previsão de classificação.|
|Mais de uma classificação para o usuário {user} e item {item} no {dataset}.|


## <a name="error-0035"></a>Erro 0035  
 Ocorrerá uma exceção se nenhum recurso for fornecido por um determinado usuário ou item.  

 No Azure Machine Learning, esse erro ocorrerá se você estiver tentando usar um modelo de recomendação para pontuação, mas um vetor de recurso não puder ser encontrado.  

**Resolução:**

o recomendador Matchbox tem determinados requisitos que devem ser atendidos ao usar recursos de item ou recursos de usuário.  Esse erro indica que um vetor de recurso está ausente para um usuário ou item que você forneceu como entrada. Verifique se um vetor de recursos está disponível nos dados para cada usuário ou item.  

 Por exemplo, se você tiver treinado um modelo de recomendação usando recursos como a idade, a localização ou a renda do usuário, mas agora desejar criar pontuações para novos usuários que não foram vistos durante o treinamento, será preciso fornecer algum conjunto equivalente de recursos (ou seja, valores de idade, localização e renda) para os novos usuários a fim de fazer previsões apropriadas para eles. 

 Se você não tiver nenhum recurso para esses usuários, considere a engenharia de recursos para gerar os recursos apropriados.  Por exemplo, se você não tiver valores de idade ou de renda do usuário individuais, poderá gerar valores aproximados a serem usados para um grupo de usuários. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > A resolução não se aplica ao seu caso? Você é bem-vindo a enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o número de linhas na coluna. Usaremos essas informações para fornecer etapas de solução de problemas mais detalhadas no futuro.

|Mensagens de Exceção|
|------------------------|
|Nenhum recurso foi fornecido para um item ou usuário necessário.|
|Recursos para {required_feature_name} são necessários, mas não fornecidos.|


## <a name="error-0036"></a>Erro 0036  
 Ocorrerá uma exceção se vários vetores do recurso forem fornecidos para um determinado usuário ou item.  

 No Azure Machine Learning, esse erro ocorrerá se um vetor de recurso for definido mais de uma vez.  

**Resolução:** verifique se o vetor de recurso não está definido mais de uma vez.  

|Mensagens de Exceção|
|------------------------|
|Definição de recurso duplicada para um usuário ou item.|


## <a name="error-0037"></a>Erro 0037  
 Ocorrerá uma exceção se várias colunas de rótulo forem especificadas e apenas uma for permitida.  

 No Azure Machine Learning, esse erro ocorrerá se mais de uma coluna estiver selecionada para ser a nova coluna de rótulo. A maioria dos algoritmos de aprendizado supervisionados exige que uma coluna seja marcada como o destino ou o rótulo.  

**Resolução:** selecione uma coluna como a nova coluna de rótulo.  

|Mensagens de Exceção|
|------------------------|
|Várias colunas de rótulo estão especificadas.|
|Várias colunas de rótulo estão especificadas em "{dataset_name}".|


## <a name="error-0039"></a>Erro 0039  
 Ocorrerá uma exceção se uma operação tiver falhado.  

 No Azure Machine Learning, esse erro ocorrerá quando uma operação interna não puder ser concluída.  

**Resolução:** esse erro é causado por muitas condições e não há uma medida específica.  
 A tabela a seguir contém mensagens genéricas para esse erro, que são seguidas por uma descrição específica da condição. 

 Se não houver detalhes disponíveis, acesse a [página de perguntas de P e R da Microsoft para enviar comentários](/answers/topics/azure-machine-learning-studio-classic.html) e forneça informações sobre os módulos que geraram o erro e as condições relacionadas.

|Mensagens de Exceção|
|------------------------|
|Falha na operação.|
|Erro ao concluir a operação: "{failed_operation}".|
|Erro ao concluir a operação: "{failed_operation}". Motivo: "{reason}".|


## <a name="error-0042"></a>Erro 0042  
 Ocorrerá uma exceção quando não for possível converter a coluna em outro tipo.  

 No Azure Machine Learning, esse erro ocorrerá quando não for possível converter a coluna no tipo especificado.  Você receberá esse erro se um módulo exigir um tipo de dados específico, como data e hora, texto, um número de ponto flutuante ou inteiro, mas não for possível converter uma coluna existente no tipo necessário.  

Por exemplo, você poderá selecionar uma coluna e tentar convertê-la em um tipo de dados numérico para uso em uma operação matemática e obter esse erro se a coluna contiver dados inválidos. 

Outro motivo para você receber esse erro é se tentar usar uma coluna que contém números de ponto flutuante ou muitos valores exclusivos como uma coluna categórica. 

**Resolução:**

+ abra a página de ajuda do módulo que gerou o erro e verifique os requisitos de tipo de dados.
+ Examine os tipos de dados das colunas no conjunto de dados de entrada.
+ Inspecione os dados originados nas chamadas fontes de dados sem esquema.
+ Verifique se há valores ausentes ou caracteres especiais no conjunto de dados que possam bloquear a conversão no tipo de dados desejado. 
    + Os tipos de dados numéricos devem ser consistentes, por exemplo, verifique se há números de ponto flutuante em uma coluna de inteiros.
    + Procure cadeias de caracteres de texto ou valores NA em uma coluna de número. 
    + Valores boolianos podem ser convertidos em uma representação apropriada dependendo do tipo de dados necessário.
    + Examinar colunas de texto quanto a caracteres não Unicode, caracteres de tabulação ou caracteres de controle
    + Os dados de data e hora devem ser consistentes para evitar erros de modelagem, mas a limpeza pode ser complexa devido aos muitos formatos. Considere usar <!--the [Execute R Script](execute-r-script.md) or -->[Execute os módulos de](execute-python-script.md) script do Python para executar a limpeza.  
+ Se necessário, modifique os valores no conjunto de dados de entrada para que a coluna possa ser convertida com êxito. A modificação pode incluir operações de compartimentalização, truncamento ou arredondamento, eliminação de exceções ou imputação de valores ausentes. Confira os seguintes artigos para ver alguns cenários comuns de transformação de dados no machine learning:
    + [Limpar Dados Ausentes](clean-missing-data.md)
    + [Normalizar Dados](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> A resolução não está clara ou não se aplica ao seu caso? Você é bem-vindo a enviar comentários sobre este artigo e fornecer informações sobre o cenário, incluindo o módulo e o tipo de dados da coluna. Usaremos essas informações para fornecer etapas de solução de problemas mais detalhadas no futuro.  

|Mensagens de Exceção|
|------------------------|
|Conversão não permitida.|
|Não foi possível converter a coluna do tipo {type1} na coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} na coluna do tipo {type2}.|
|Não foi possível converter a coluna "{col_name1}" do tipo {type1} na coluna "{col_name2}" do tipo {type2}.|


## <a name="error-0044"></a>Erro 0044  
 Ocorrerá uma exceção quando não for possível derivar o tipo de elemento da coluna com base nos valores existentes.  

 No Azure Machine Learning, esse erro ocorre quando não é possível inferir o tipo de uma coluna ou colunas em um conjunto de dados. Isso normalmente acontece ao concatenar dois ou mais conjuntos de dados com diferentes tipos de elementos. Se o Azure Machine Learning não puder determinar um tipo comum que possa representar todos os valores em uma coluna ou colunas sem perda de informações, ele gerará esse erro.  

**Resolução:** verifique se todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (numérico, Booliano, categórico, cadeia de caracteres, data etc.) ou podem ser forçados para o mesmo tipo.  

|Mensagens de Exceção|
|------------------------|
|Não é possível derivar o tipo de elemento da coluna.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" – todos os elementos são referências nulas.|
|Não é possível derivar o tipo de elemento para a coluna "{column_name}" do conjunto de dados "{dataset_name}" – todos os elementos são referências nulas.|


## <a name="error-0045"></a>Erro 0045  
 Ocorrerá uma exceção quando não for possível criar uma coluna devido aos tipos de elemento mistos na origem.  

 No Azure Machine Learning, esse erro é produzido quando os tipos de elementos de dois conjuntos de valores que estão sendo combinados são diferentes.  

**Resolução:** verifique se todos os valores em uma determinada coluna em ambos os conjuntos de dados que estão sendo combinados são do mesmo tipo (numérico, Booliano, categórico, cadeia de caracteres, data etc.).  

|Mensagens de Exceção|
|------------------------|
|Não foi possível criar colunas com tipos de elemento mistos.|
|Não é possível criar a coluna com a ID "{column_id}" de tipos de elemento mistos:<br />O tipo de dados[{row_1}, {column_id}] é "{type_1}". <br />O tipo de dados[{row_2}, {column_id}] é "{type_2}".|
|Não é possível criar a coluna com a ID "{column_id}" de tipos de elemento mistos:<br />O tipo na parte {chunk_id_1} é "{type_1}". <br />O tipo na parte {chunk_id_2} é "{type_2}" com o tamanho da parte: {chunk_size}.|


## <a name="error-0046"></a>Erro 0046  
 Ocorrerá uma exceção quando não for possível criar um diretório no caminho especificado.  

 No Azure Machine Learning, esse erro ocorrerá quando não for possível criar um diretório no caminho especificado. Você receberá esse erro se qualquer parte do caminho para o diretório de saída de uma consulta do Hive estiver incorreta ou inacessível.  

**Resolução:** reveja o módulo e verifique se o caminho do diretório está formatado corretamente e se está acessível com as credenciais atuais.  

|Mensagens de Exceção|
|------------------------|
|Especifique um diretório de saída válido.|
|Diretório: não foi possível criar {path}. Especifique um caminho válido.|


## <a name="error-0047"></a>Erro 0047  
 Ocorrerá uma exceção se o número de colunas de recurso em alguns dos conjuntos de dados transmitidos para o módulo for muito pequeno.  

 No Azure Machine Learning, esse erro ocorrerá se o conjunto de dados de entrada para treinamento não contiver o número mínimo de colunas exigido pelo algoritmo. Normalmente, o conjunto de dados está vazio ou contém apenas colunas de treinamento.  

**Resolução:** reveja o conjunto de dados de entrada para ter certeza de que há uma ou mais colunas adicionais separadas da coluna de rótulo.  

|Mensagens de Exceção|
|------------------------|
|O número de colunas de recurso no conjunto de dados de entrada é menor que o mínimo permitido.|
|O número de colunas de recurso no conjunto de dados de entrada é menor que o mínimo permitido de coluna {required_columns_count}.|
|O número de colunas de recurso no conjunto de dados de entrada "{arg_name}" é menor que o mínimo permitido de colunas {required_columns_count}.|


## <a name="error-0048"></a>Erro 0048  
 Ocorrerá uma exceção quando não for possível abrir um arquivo.  

 No Azure Machine Learning, esse erro ocorrerá quando não for possível abrir um arquivo para leitura ou gravação. Você pode receber esse erro por estes motivos:  

-   O contêiner ou o arquivo (blob) não existe  
  
-   O nível de acesso do arquivo ou do contêiner não permite que você acesse o arquivo  
  
-   O arquivo é muito grande para ser lido ou o formato dele é incorreto  

**Resolução:** reveja o módulo e o arquivo que você está tentando ler.  

 Verifique se os nomes do contêiner e do arquivo estão corretos.  

 Use o portal clássico do Azure ou uma ferramenta de armazenamento do Azure para verificar se você tem permissão para acessar o arquivo.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Mensagens de Exceção|
|------------------------|
|Não é possível abrir um arquivo.|
|Erro ao abrir o arquivo: {file_name}.|
|Erro ao abrir o arquivo: {file_name}. Mensagem de exceção de armazenamento: {exception}.|


## <a name="error-0049"></a>Erro 0049  
 Ocorrerá uma exceção quando não for possível analisar um arquivo.  

 No Azure Machine Learning, esse erro ocorrerá quando não for possível analisar um arquivo. Você receberá esse erro se o formato de arquivo selecionado no módulo [Importar Dados](import-data.md) não corresponder ao formato real do arquivo ou se o arquivo contiver um caractere irreconhecível.  

**Resolução:** reveja o módulo e corrija a seleção de formato de arquivo se ela não corresponder ao formato do arquivo. Se possível, inspecione o arquivo para confirmar se ele não contém nenhum caractere ilícito.  

|Mensagens de Exceção|
|------------------------|
|Não é possível analisar um arquivo.|
|Erro ao analisar o arquivo {file_format}.|
|Erro ao analisar o arquivo {file_format}: {file_name}.|
|Erro ao analisar o arquivo {file_format}. Motivo: {failure_reason}.|
|Erro ao analisar o arquivo {file_format}: {file_name}. Motivo: {failure_reason}.|


## <a name="error-0052"></a>Erro 0052  
 Ocorrerá uma exceção se a chave da conta de armazenamento do Azure for especificada incorretamente.  

 No Azure Machine Learning, esse erro ocorrerá se a chave usada para acessar a conta de armazenamento do Azure estiver incorreta. Por exemplo, você pode ver esse erro se a chave de armazenamento do Azure estava truncada quando copiada e colada ou se a chave errada foi usada.  

 Para obter mais informações sobre obter a chave para uma conta de armazenamento do Azure, confira [Exibir, copiar e regenerar as chaves de acesso de armazenamento](../../storage/common/storage-account-create.md).  

**Resolução:** reveja o módulo e verifique se a chave de armazenamento do Azure está correta para a conta; copie a chave novamente no portal clássico do Azure, se necessário.  

|Mensagens de Exceção|
|------------------------|
|A chave da conta de armazenamento do Azure está incorreta.|


## <a name="error-0053"></a>Erro 0053  
 Ocorrerá uma exceção no caso de não haver nenhum recurso ou item de usuário para recomendações de matchbox.  

 No Azure Machine Learning, esse erro é produzido quando um vetor de recurso não pode ser encontrado.  

**Resolução:** verifique se um vetor de recurso está presente no conjunto de dados de entrada.  

|Mensagens de Exceção|
|------------------------|
|Os recursos e/ou itens do usuário são necessários, mas não foram fornecidos.|


## <a name="error-0056"></a>Erro 0056  
 Ocorrerá uma exceção se as colunas selecionadas para uma operação violarem os requisitos.  

 No Azure Machine Learning, esse erro ocorrerá quando você estiver escolhendo colunas para uma operação que requer que a coluna seja de um tipo de dados específico. 

 Esse erro também poderá ocorrer se a coluna for o tipo de dados correto, mas o módulo que você estiver usando exigir que a coluna também seja marcada como uma coluna de recurso, rótulo ou categórica.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Resolução:**

1.  examine o tipo de dados das colunas selecionadas no momento. 

2. Verifique se as colunas selecionadas são categóricas, de rótulo ou de recurso.  
  
3.  Examine o tópico de ajuda do módulo no qual você fez a seleção da coluna para determinar se há requisitos específicos para o tipo de dados ou o uso da coluna.  
  
3.  Use [Editar Metadados](edit-metadata.md) para alterar o tipo de coluna durante essa operação. Altere o tipo de coluna de volta para o valor original dela, usando outra instância de [Editar Metadados](edit-metadata.md), se precisar dela para operações downstream.  

|Mensagens de Exceção|
|------------------------|
|Uma ou mais colunas selecionadas não estavam em uma categoria permitida.|
|A coluna com o nome "{col_name}" não é uma categoria permitida.|


## <a name="error-0057"></a>Erro 0057  
 Ocorrerá uma exceção ao tentar criar um arquivo ou um blob que já existe.  

 Essa exceção ocorre quando você está usando o módulo [Exportar dados](export-data.md) ou outro módulo para salvar os resultados de um pipeline no Azure Machine Learning no Armazenamento de Blobs do Azure, mas tenta criar um arquivo ou blob que já existe.   

**Resolução:**

 você receberá esse erro somente se tiver definido anteriormente a propriedade **Modo de gravação do Armazenamento de Blobs do Azure** como **Erro**. Por design, esse módulo gerará um erro se você tentar gravar um conjunto de dados em um blob que já existe.

 - Abra as propriedades do módulo e altere a propriedade **Modo de gravação do Armazenamento de Blobs do Azure** como **Substituir**.
 - Como alternativa, você pode digitar o nome de um arquivo ou blob de destino diferente e especificar um blob que ainda não existe.  

|Mensagens de Exceção|
|------------------------|
|O arquivo ou blob já existe.|
|O arquivo ou blob "{file_path}" já existe.|


## <a name="error-0058"></a>Erro 0058  
 No Azure Machine Learning, esse erro ocorrerá se o conjunto de dados não contiver a coluna de rótulo esperada.  

 Essa exceção também poderá ocorrer quando a coluna de rótulo fornecida não corresponder aos dados ou tipo de dados esperados pelo aprendiz ou tiver os valores errados. Por exemplo, essa exceção é produzida ao usar uma coluna de rótulo de valor real ao treinar um classificador binário.  

**Resolução:** a resolução depende do aprendiz ou do treinador que você está usando e dos tipos de dados das colunas no seu conjunto de dados. Primeiro, verifique os requisitos do módulo de treinamento ou de algoritmo do machine learning.  

 Reveja o conjunto de dados de entrada. Verifique se a coluna que você espera ser tratada como o rótulo tem o tipo de dados certo para o modelo que você está criando.  

 Verifique se há valores ausentes nas entradas e elimine-os ou substitua-os, se necessário.  

 Se necessário, adicione o módulo [Editar Metadados](edit-metadata.md) e verifique se a coluna de rótulo está marcada como um rótulo.  

|Mensagens de Exceção|
|------------------------|
|Os valores da coluna de rótulo e os valores da coluna de rótulo pontuados não são comparáveis.|
|A coluna de rótulo não é a esperada em "{dataset_name}".|
|A coluna de rótulo não é a esperada em "{dataset_name}", {reason}.|
|A coluna de rótulo "{column_name}" não é esperada em "{dataset_name}".|
|A coluna de rótulo "{column_name}" não é esperada em "{dataset_name}", {reason}.|


## <a name="error-0059"></a>Erro 0059  
 Ocorrerá uma exceção se um índice de coluna especificado em um seletor de colunas não puder ser analisado.  

 No Azure Machine Learning, esse erro ocorrerá se um índice de coluna especificado ao usar o Seletor de Coluna não puder ser analisado.  Você receberá esse erro quando o índice de coluna estiver em um formato inválido que não pode ser analisado.  

**Resolução:** modifique o índice de coluna para usar um valor de índice válido.  

|Mensagens de Exceção|
|------------------------|
|Não foi possível analisar um ou mais índices de coluna ou intervalos de índice especificados.|
|Não foi possível analisar o índice ou intervalo de coluna "{column_index_or_range}".|


## <a name="error-0060"></a>Erro 0060  
 Ocorrerá uma exceção quando um intervalo de coluna fora do intervalo for especificado em um seletor de colunas.  

 No Azure Machine Learning, esse erro ocorre quando um intervalo de coluna fora do intervalo é especificado no Seletor de Coluna. Você receberá esse erro se o intervalo de coluna no seletor de coluna não corresponder às colunas no conjunto de dados.  

**Resolução:** modifique o intervalo de coluna no seletor de coluna para corresponder às colunas no conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Intervalo de índice de coluna inválido ou fora do intervalo especificado.|
|O intervalo de coluna "{column_range}" é inválido ou está fora do intervalo.|


## <a name="error-0061"></a>Erro 0061  
 Ocorrerá uma exceção ao tentar adicionar uma linha a uma DataTable que tenha um número diferente de colunas da tabela.  

 No Azure Machine Learning, esse erro ocorre quando você tenta adicionar uma linha a um conjunto de dados que tem um número diferente de colunas do que o conjunto de dados.  Você receberá esse erro se a linha que estiver sendo adicionada ao conjunto de dados tiver um número diferente de colunas do conjunto de dados de entrada.  A linha não poderá ser acrescentada ao conjunto de dados se o número de colunas for diferente.  

**Resolução:** modifique o conjunto de dados de entrada para ter o mesmo número de colunas que a linha adicionada ou modifique a linha adicionada para ter o mesmo número de colunas que o conjunto de dados.  

|Mensagens de Exceção|
|------------------------|
|Todas as tabelas devem ter o mesmo número de colunas.|
|As colunas na parte "{chunk_id_1}" são diferentes com a parte "{chunk_id_2}" com o tamanho de parte: {chunk_size}.|
|A contagem de colunas no arquivo "{filename_1}" (count={column_count_1}) é diferente com o arquivo "{filename_2}" (count={column_count_2}).|


## <a name="error-0062"></a>Erro 0062  
 Ocorrerá uma exceção durante a tentativa de comparar dois modelos com tipos diferentes de aprendiz.  

 No Azure Machine Learning, esse erro é produzido quando as métricas de avaliação para dois conjuntos de dados pontuados diferentes não podem ser comparadas. Nesse caso, não é possível comparar a eficácia dos modelos usados para produzir os dois conjuntos de dados pontuados.  

**Resolução:** verifique se os resultados pontuados são produzidos pelo mesmo tipo de modelo de machine learning (classificação binária, regressão, classificação de várias classes, recomendação, clustering, detecção de anomalias etc.) Todos os modelos comparados devem ter o mesmo tipo de aprendiz.  

|Mensagens de Exceção|
|------------------------|
|Todos os modelos devem ter o mesmo tipo de aprendiz.|
|Tipo de aprendiz incompatível obtido: "{actual_learner_type}". Os tipos de aprendiz esperados são: "{expected_learner_type_list}".|


## <a name="error-0064"></a>Erro 0064  
 Ocorrerá uma exceção se o nome da conta de armazenamento do Azure ou a chave de armazenamento for especificado incorretamente.  

 No Azure Machine Learning, esse erro ocorrerá se a chave de armazenamento ou o nome da conta de armazenamento do Azure for especificado incorretamente. Você receberá esse erro se inserir um nome de conta ou senha incorreto para a conta de armazenamento. Isso poderá ocorrer se você inserir manualmente o nome da conta ou a senha. Isso também poderá ocorrer se a conta tiver sido excluída.  

**Resolução:** verifique se o nome da conta e a senha foram inseridos corretamente e se a conta existe.  

|Mensagens de Exceção|
|------------------------|
|O nome da conta de armazenamento ou a chave de armazenamento do Azure está incorreto.|
|O nome da conta de Armazenamento do Azure "{account_name}" ou a chave de armazenamento para o nome da conta está incorreta.|


## <a name="error-0065"></a>Erro 0065  
 Ocorrerá uma exceção se o nome do blob do Azure estiver especificado incorretamente.  

 No Azure Machine Learning, esse erro ocorrerá se o nome do blob do Azure for especificado incorretamente.  Você receberá o erro se:  

-   O blob não puder ser encontrado no contêiner especificado.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Apenas o contêiner tiver sido especificado como a origem em uma solicitação [Importar Dados](import-data.md) quando o formato era Excel ou CSV com codificação; a concatenação do conteúdo de todos os blobs dentro de um contêiner não é permitida com esses formatos.  
  
-   Um URI SAS não contiver o nome de um blob válido.  

**Resolução:** reveja o módulo que gera a exceção. Verifique se o blob especificado existe no contêiner na conta de armazenamento e se as permissões permitem que você veja o blob. Verifique se a entrada está no formato **nome do contâiner/nome do arquivo** se você tiver os formatos Excel ou CSV com codificação. Verifique se um URI SAS contém o nome de um blob válido.  

|Mensagens de Exceção|
|------------------------|
|O nome do Azure Storage Blob está incorreto.|
|O nome do Azure Storage Blob "{blob_name}" está incorreto.|
|O nome do Azure Storage Blob com o prefixo "{blob_name_prefix}" não existe.|
|Falha ao localizar blobs de armazenamento do Azure no contêiner "{container_name}".|
|Falha ao localizar blobs de armazenamento do Azure com o caminho curinga "{blob_wildcard_path}".|


## <a name="error-0066"></a>Erro 0066  
 Ocorrerá uma exceção se um recurso não puder ser carregado em um blob do Azure.  

 No Azure Machine Learning, esse erro ocorrerá se não for possível carregar uma exceção em um blob do Azure.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Ambos são salvos na mesma conta de armazenamento do Azure que a conta que contém o arquivo de entrada.  

**Resolução:** reveja o módulo. Verifique se o nome da conta, a chave de armazenamento e o contêiner do Azure estão corretos e se a conta tem permissão para gravar no contêiner.  

|Mensagens de Exceção|
|------------------------|
|Não foi possível carregar o recurso no armazenamento do Azure.|
|Não foi possível carregar o arquivo "{source_path}" no armazenamento do Azure como "{dest_path}".|


## <a name="error-0067"></a>Erro 0067  
 Ocorrerá uma exceção se um conjunto de dados tiver um número diferente de colunas que o esperado.  

 No Azure Machine Learning, esse erro ocorrerá se um conjunto de dados tiver um número de colunas diferente do esperado.  Você receberá esse erro quando o número de colunas no conjunto de dados for diferente do número de colunas que o módulo espera durante a execução.  

**Resolução:** modifique o conjunto de dados de entrada ou os parâmetros.  

|Mensagens de Exceção|
|------------------------|
|Número de colunas na tabela de dados inesperado.|
|Número de colunas no conjunto de dados "{dataset_name}" inesperado.|
|Esperavam-se colunas "{expected_column_count}", mas foram encontradas colunas "{actual_column_count}" em vez disso.|
|No conjunto de dados de entrada "{dataset_name}", esperavam-se colunas "{expected_column_count}", mas foram localizadas colunas "{actual_column_count}" em vez disso.|


## <a name="error-0068"></a>Erro 0068  
 Ocorrerá uma exceção se o script do Hive especificado não estiver correto.  

 No Azure Machine Learning, esse erro ocorrerá se houver erros de sintaxe em um script SQL do Hive ou se o interpretador do Hive encontrar um erro ao executar a consulta ou o script.  

**Resolução:**

a mensagem de erro do Hive normalmente é informada no Log de Erros para que você possa executar uma ação com base no erro específico. 

+ Abra o módulo e inspecione se há erros na consulta.  
+ Verifique se a consulta funciona corretamente fora do Azure Machine Learning fazendo logon no console do Hive do seu cluster Hadoop e executando a consulta.  
+ Tente inserir comentários em seu script do Hive em uma linha separada em vez de misturar instruções executáveis e comentários em uma linha.  

### <a name="resources"></a>Recursos

Confira os seguintes artigos para obter ajuda com consultas do Hive para machine learning:

+ [Criar tabelas do Hive e carregar dados do Armazenamento de Blobs do Azure](../team-data-science-process/move-hive-tables.md)
+ [Explorar dados em tabelas com consultas do Hive](../team-data-science-process/explore-data-hive-tables.md)
+ [Criar recursos de dados em um cluster Hadoop usando as consultas do Hive](../team-data-science-process/create-features-hive.md)
+ [Roteiro de Usuários do Hive para SQL (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Mensagens de Exceção|
|------------------------|
|O script do Hive está incorreto.|


## <a name="error-0069"></a>Erro 0069  
 Ocorrerá uma exceção se o script SQL especificado não estiver correto.  

 No Azure Machine Learning, esse erro ocorrerá se o script SQL especificado tiver problemas de sintaxe ou se as colunas ou a tabela especificadas no script não forem válidas. 

 Você receberá esse erro se o mecanismo do SQL encontrar algum erro ao executar a consulta ou o script. A mensagem de erro do SQL normalmente é informada no Log de Erros para que você possa executar uma ação com base no erro específico.  

**Resolução:** reveja o módulo e inspecione se há erros na consulta SQL.  

 Verifique se a consulta funciona corretamente fora do Azure ML fazendo logon no servidor de banco de dados diretamente e executando a consulta.  

 Se houver uma mensagem gerada por SQL informada pela exceção do módulo, execute uma ação com base no erro relatado. Por exemplo, as mensagens de erro às vezes incluem diretrizes específicas sobre o erro provável:
+ *Nenhuma coluna ou banco de dados ausente*, indicando que você pode ter digitado um nome de coluna errado. Se você tiver certeza de que o nome da coluna está correto, tente usar colchetes ou aspas nos quais incluir o identificador de coluna.
+ *Erro lógico do SQL \<SQL keyword\> próximo*, indicando que você pode ter um erro de sintaxe antes da palavra-chave especificada

  
|Mensagens de Exceção|
|------------------------|
|O script SQL está incorreto.|
|A consulta SQL "{sql_query}" não está correta.|
|A consulta SQL "{sql_query}" não está correta. Mensagem de exceção: {exception}.|


## <a name="error-0070"></a>Erro 0070  
 Ocorrerá uma exceção ao tentar acessar a tabela do Azure inexistente.  

 No Azure Machine Learning, esse erro ocorrerá quando você tentar acessar uma tabela do Azure não existente. Você receberá esse erro se especificar uma tabela no armazenamento do Azure, que não existe durante a leitura ou gravação no Armazenamento de Tabelas do Azure. Isso poderá acontecer se você digitar incorretamente o nome da tabela desejada ou se houver uma incompatibilidade entre o nome de destino e o tipo de armazenamento. Por exemplo, você pretendeu ler em uma tabela, mas inseriu o nome de um blob.  

**Resolução:** reveja o módulo para verificar se o nome da tabela está correto.  

|Mensagens de Exceção|
|------------------------|
|Não existe tabela do Azure.|
|Não existe a tabela "{table_name}" do Azure.|


## <a name="error-0072"></a>Erro 0072  
 Ocorrerá uma exceção caso a conexão atinja o tempo limite.  

 No Azure Machine Learning, esse erro ocorrerá quando uma conexão atingir o tempo limite. Você receberá esse erro se houver problemas de conectividade no momento com o destino ou a fonte de dados, como conectividade lenta com a Internet, ou se o conjunto de dados for grande e/ou se a consulta SQL a ser lida nos dados executar um processamento complicado.  

**Resolução:** determine se há problemas no momento com conexões lentas com o armazenamento do Azure ou com a Internet.  

|Mensagens de Exceção|
|------------------------|
|Ocorreu um tempo limite de conexão.|


## <a name="error-0073"></a>Erro 0073  
 Ocorrerá uma exceção se ocorrer um erro durante a conversão de uma coluna em outro tipo.  

 No Azure Machine Learning, esse erro ocorrerá quando não for possível converter a coluna em outro tipo.  Você receberá esse erro se um módulo exigir um tipo específico e não for possível converter a coluna no novo tipo.  

**Resolução:** modifique o conjunto de dados de entrada para que a coluna possa ser convertida com base na exceção interna.  

|Mensagens de Exceção|
|------------------------|
|Falha ao converter a coluna.|
|Falha ao converter a coluna em {target_type}.|


## <a name="error-0075"></a>Erro 0075  
Ocorrerá uma exceção quando uma função de agrupamento inválida for usada ao quantizar um conjunto de dados.  

No Azure Machine Learning, esse erro ocorrerá quando você estiver tentando agrupar dados usando um método sem suporte ou quando as combinações de parâmetro forem inválidas.  

**Resolução:**

o tratamento de erro para esse evento foi introduzido em uma versão anterior do Azure Machine Learning que permitia mais personalização dos métodos compartimentalização. No momento, todos os métodos de compartimentalização são baseados em uma seleção de uma lista suspensa, portanto, tecnicamente, não deve mais ser possível obter esse erro.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Mensagens de Exceção|
|------------------------|
|Função de compartimentalização inválida usada.|


## <a name="error-0077"></a>Erro 0077  
 Ocorrerá uma exceção quando o modo de gravação do arquivo blob desconhecido for passado.  

 No Azure Machine Learning, esse erro ocorrerá se um argumento inválido for passado nas especificações para uma origem ou destino do arquivo de blob.  

**Resolução:** em quase todos os módulos que importam ou exportam dados de e para o Armazenamento de Blobs do Azure, os valores de parâmetro que controlam o modo de gravação são atribuídos usando uma lista suspensa; portanto, não é possível passar um valor inválido e esse erro não deve aparecer. Esse erro será preterido em uma versão posterior.  

|Mensagens de Exceção|
|------------------------|
|Modo de gravação de blob sem suporte.|
|Modo de gravação de blob sem suporte: {blob_write_mode}.|


## <a name="error-0078"></a>Erro 0078  
 Ocorrerá uma exceção quando a opção HTTP para [Importar Dados](import-data.md) receber um código de status 3xx indicando o redirecionamento.  

 No Azure Machine Learning, esse erro ocorrerá quando a opção HTTP para [Importar Dados](import-data.md) receber um código de status 3xx (301, 302, 304 etc.) que indica redirecionamento. Você receberá esse erro se tentar se conectar a uma origem HTTP que redireciona o navegador para outra página. Por motivos de segurança, sites de redirecionamento não são permitidos como fontes de dados para o Azure Machine Learning.  

**Resolução:** se o site for confiável, insira a URL de redirecionamento diretamente.  

|Mensagens de Exceção|
|------------------------|
|Redirecionamento de HTTP não permitido.|


## <a name="error-0079"></a>Erro 0079  
 Ocorrerá uma exceção se o nome do contêiner de armazenamento do Azure estiver especificado incorretamente.  

 No Azure Machine Learning, esse erro ocorrerá se o nome do contêiner de armazenamento do Azure for especificado incorretamente. Você receberá esse erro se não tiver especificado o contêiner e o nome do blob (arquivo) usando a opção **o caminho para o blob que começa com o contêiner** ao gravar no Armazenamento de Blobs do Azure.  

**Resolução:** reveja o módulo [Exportar Dados](export-data.md) e verifique se o caminho especificado para o blob contém o contêiner e o nome do arquivo, no formato **contêiner/nome de caminho**.  

|Mensagens de Exceção|
|------------------------|
|O nome do contêiner de armazenamento do Azure está incorreto.|
|O nome do contêiner de armazenamento do Azure "{container_name}" está incorreto; era esperado um nome de contêiner do formato contêiner/blob.|


## <a name="error-0080"></a>Erro 0080  
 Ocorrerá uma exceção quando a coluna com todos os valores ausentes não for permitida pelo módulo.  

 No Azure Machine Learning, esse erro é produzido quando uma ou mais colunas consumidas pelo módulo contêm todos os valores ausentes. Por exemplo, se um módulo estiver calculando estatísticas de agregação para cada coluna, ele não poderá operar em uma coluna que não contenha nenhum dado. Nesses casos, a execução do módulo é interrompida com essa exceção.  

**Resolução:** reveja o conjunto de dados de entrada e remova todas as colunas que contêm todos os valores ausentes.  

|Mensagens de Exceção|
|------------------------|
|Colunas com todos os valores ausentes não são permitidas.|
|A coluna {col_index_or_name} tem todos os valores ausentes.|


## <a name="error-0081"></a>Erro 0081  
 Ocorrerá uma exceção no módulo PCA se o número de dimensões a serem reduzidas for igual ao número de colunas de recurso no conjunto de dados de entrada, que contém pelo menos uma coluna de recurso esparsa.  

 No Azure Machine Learning, esse erro será produzido se as seguintes condições forem atendidas: (a) o conjunto de dados de entrada tem pelo menos uma coluna esparsa e (b) o número final de dimensões solicitadas é o mesmo que o número de dimensões de entrada.  

**Resolução:** considere reduzir o número de dimensões na saída para ser menor que o número de dimensões na entrada. Isso é típico em aplicativos do PCA.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Mensagens de Exceção|
|------------------------|
|Para um conjunto de dados que contenha colunas de recursos esparsas, o número de dimensões para o qual reduzir deve ser menor que o número de colunas de recurso.|


## <a name="error-0082"></a>Erro 0082  
 Ocorrerá uma exceção quando um modelo não puder ser desserializado com êxito.  

 No Azure Machine Learning, esse erro ocorrerá quando um modelo de machine learning salvo ou uma transformação não puder ser carregado por uma versão mais recente do runtime do Azure Machine Learning como resultado de uma alteração da falha.  

**Resolução:** o pipeline de treinamento que produziu o modelo ou a transformação deve ser executado novamente e o modelo ou a transformação deve ser salvo novamente.  

|Mensagens de Exceção|
|------------------------|
|Não foi possível desserializar o modelo porque ele provavelmente foi serializado com um formato de serialização mais antigo. Treine novamente e salve o modelo outra vez.|


## <a name="error-0083"></a>Erro 0083  
 Ocorrerá uma exceção se o conjunto de dados usado para treinamento não puder ser usado para o tipo concreto de aprendiz.  

 No Azure Machine Learning, esse erro é produzido quando o conjunto de dados é incompatível com o aprendiz que está sendo treinado. Por exemplo, o conjunto de dados pode conter pelo menos um valor ausente em cada linha e, como resultado, todo o conjunto de dados seria ignorado durante o treinamento. Em outros casos, alguns algoritmos de machine learning, como a detecção de anomalias, não esperam que os rótulos estejam presentes e possam gerar essa exceção se os rótulos estão presentes no conjunto de dados.  

**Resolução:** consulte a documentação do aprendiz que está sendo usada para verificar os requisitos para o conjunto de dados de entrada. Examine as colunas para ver se todas as colunas necessárias estão presentes.  

|Mensagens de Exceção|
|------------------------|
|O conjunto de dados usado para treinamento é inválido.|
|{data_name} contém dados inválidos para treinamento.|
|{data_name} contém dados inválidos para treinamento. Tipo de aprendiz: {learner_type}.|
|{data_name} contém dados inválidos para treinamento. Tipo de aprendiz: {learner_type}. Motivo: {reason}.|
|Falha ao aplicar a ação "{action_name}" nos dados de treinamento {data_name}. Motivo: {reason}.|


## <a name="error-0084"></a>Erro 0084  
 Ocorrerá uma exceção quando as pontuações produzidas com base em um script R forem avaliadas. No momento, não há suporte para isso.  

 No Azure Machine Learning, esse erro ocorrerá se você tentar usar um dos módulos para avaliar um modelo sem saída de um script R que contém pontuações.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|No momento, não há suporte para a avaliação de pontuações produzidas pelo Modelo Personalizado.|


## <a name="error-0085"></a>Erro 0085  
 Ocorrerá uma exceção quando a avaliação de script falhar com um erro.  

 No Azure Machine Learning, esse erro ocorrerá quando você estiver executando um script personalizado que conte erros de sintaxe.  

**Resolução:** examine seu código em um editor externo e verifique se há erros.  

|Mensagens de Exceção|
|------------------------|
|Erro durante a avaliação do script.|
|Ocorreu o seguinte erro durante a avaliação do script. Veja o log de saída para obter mais informações:<br />---------- Início da mensagem de erro do interpretador {script_language} ----------<br />{message}<br />---------- Fim da mensagem de erro do interpretador {script_language} ----------|


## <a name="error-0090"></a>Erro 0090  
 Ocorrerá uma exceção quando a criação da tabela do Hive falhar.  

 No Azure Machine Learning, esse erro ocorrerá quando você estiver usando [Exportar Dados](export-data.md) ou outra opção para salvar dados em um cluster HDInsight e a tabela do Hive especificada não puder ser criada.  

**Resolução:** verifique o nome da conta de armazenamento do Azure associado ao cluster e verifique se você está usando a mesma conta nas propriedades do módulo.  

|Mensagens de Exceção|
|------------------------|
|Não foi possível criar a tabela do Hive. Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que é passado pelo parâmetro do módulo.|
|Não foi possível criar a tabela do Hive "{table_name}". Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é o mesmo que é passado pelo parâmetro do módulo.|
|Não foi possível criar a tabela do Hive "{table_name}". Para um cluster HDInsight, verifique se o nome da conta de armazenamento do Azure associado ao cluster é "{cluster_name}".|


## <a name="error-0102"></a>Erro 0102  
 Gerado quando um arquivo ZIP não puder ser extraído.  

 No Azure Machine Learning, esse erro ocorrerá quando você estiver importando um pacote compactado com a extensão .zip, mas o pacote não for um arquivo zip ou o arquivo não usar um formato zip com suporte.  

**Resolução:** verifique se o arquivo selecionado é um arquivo .zip válido e se ele foi compactado usando um dos algoritmos de compactação com suporte.  

 Se você receber esse erro ao importar conjuntos de dados em formato compactado, verifique se todos os arquivos contidos usam um dos formatos de arquivo com suporte e se estão no formato Unicode.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Tente ler os arquivos desejados para uma nova pasta compactada e tente adicionar o módulo personalizado novamente.  

|Mensagens de Exceção|
|------------------------|
|O arquivo ZIP fornecido não está no formato correto.|


## <a name="error-0105"></a>Erro 0105  
 Esse erro é exibido quando um arquivo de definição de módulo contém um tipo de parâmetro sem suporte  
  
 No Azure Machine Learning, esse erro é produzido quando você cria uma definição xml de módulo personalizado e o tipo de parâmetro ou argumento na definição não corresponde a um tipo com suporte.  
  
**Resolução:** verifique se a propriedade de tipo de qualquer elemento **Arg** no arquivo de definição xml do módulo personalizado é um tipo com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de parâmetro sem suporte.|  
|Tipo de parâmetro '{0}' especificado sem suporte.|  


## <a name="error-0107"></a>Erro 0107  
 Gerado quando um arquivo de definição de módulo define um tipo de saída sem suporte  
  
 No Azure Machine Learning, esse erro é produzido quando o tipo de uma porta de saída em uma definição xml do módulo personalizado não corresponde a um tipo com suporte.  
  
**Resolução:** verifique se a propriedade de tipo de um elemento Saída no arquivo de definição xml do módulo personalizado é um tipo com suporte.  
  
|Mensagens de Exceção|  
|------------------------|  
|Tipo de saída sem suporte.|  
|Tipo de saída '{output_type}' especificado sem suporte.|  


## <a name="error-0125"></a>Erro 0125  
 Gerado quando o esquema de vários conjuntos de dados não corresponde.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O esquema do conjunto de dados não corresponde.|


## <a name="error-0127"></a>Erro 0127  
 O tamanho de pixel da imagem excede o limite permitido  

 Esse erro ocorrerá se você estiver lendo imagens de um conjunto de dados de uma imagem para classificação e as imagens forem maiores do que o modelo possa manipular.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Mensagens de Exceção|
|------------------------|
|O tamanho de pixel da imagem excede o limite permitido.|
|O tamanho do pixel da imagem no arquivo '{file_path}' excede o limite permitido: '{size_limit}'.|


## <a name="error-0128"></a>Erro 0128  
 O número de probabilidades condicionais para colunas categóricas excede o limite.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O número de probabilidades condicionais para colunas categóricas excede o limite.|
|O número de probabilidades condicionais para colunas categóricas excede o limite. As colunas '{column_name_or_index_1}' e '{column_name_or_index_2}' são o par problemático.|


## <a name="error-0129"></a>Erro 0129  
 O número de colunas no conjunto de dados excede o limite permitido.  

**Resolução:**

|Mensagens de Exceção|
|------------------------|
|O número de colunas no conjunto de dados excede o limite permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o permitido.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite permitido de '{component_name}'.|
|O número de colunas no conjunto de dados em '{dataset_name}' excede o limite de '{limit_columns_count}' de '{component_name}' permitido.|


## <a name="error-0134"></a>Erro 0134
A exceção ocorre quando a coluna de rótulo está ausente ou tem um número insuficiente de linhas rotuladas.  

Esse erro ocorre quando o módulo requer uma coluna de rótulo, mas você não incluiu nenhuma na seleção de coluna ou a coluna de rótulo está sem muitos valores.

Esse erro também pode ocorrer quando uma operação anterior altera o conjunto de dados de modo que linhas insuficientes estão disponíveis para uma operação downstream. Por exemplo, suponha que você use uma expressão no módulo **Partição e Exemplo** para dividir um conjunto de dados por valores. Se nenhuma correspondência for encontrada para sua expressão, um dos conjuntos de dados resultante da partição estará vazio.

Resolução: 

 se você incluir uma coluna de rótulo na seleção de coluna, mas ela não for reconhecida, use o módulo [Editar Metadados](edit-metadata.md) para marcá-la como uma coluna de rótulo.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Em seguida, você pode usar o módulo [Limpar Dados Ausentes](clean-missing-data.md) para remover linhas com valores ausentes na coluna rótulo. 

 Verifique seus conjuntos de dados de entrada para verificar se eles contêm dados válidos e linhas suficientes para atender aos requisitos da operação. Muitos algoritmos gerarão uma mensagem de erro se exigirem algum número mínimo de linhas de dados, mas os dados contiverem apenas algumas linhas ou apenas um cabeçalho.

|Mensagens de Exceção|
|------------------------|
|A exceção ocorre quando a coluna de rótulo está ausente ou tem um número insuficiente de linhas rotuladas.|
|Ocorrerá uma exceção quando a coluna de rótulo estiver ausente ou tiver menos de {required_rows_count} linhas rotuladas.|
|Ocorrerá uma exceção quando a coluna de rótulo no conjunto de dados {dataset_name} estiver ausente ou tiver menos de {required_rows_count} linhas rotuladas.|


## <a name="error-0138"></a>Erro 0138  
 A memória foi esgotada; não é possível concluir a execução do módulo. A redução do conjunto de dados pode ajudar a minimizar o problema.  

 Esse erro ocorre quando o módulo que está sendo executado requer mais memória do que o disponível no contêiner do Azure. Isso poderá acontecer se você estiver trabalhando com um grande conjunto de dados e a operação atual não couber na memória.  

**Resolução:** se você estiver tentando ler um grande conjunto de dados e a operação não puder ser concluída, a redução da resolução do conjunto de dados poderá ajudar.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Mensagens de Exceção|
|------------------------|
|A memória foi esgotada; não é possível concluir a execução do módulo.|
|A memória foi esgotada; não é possível concluir a execução do módulo. Detalhes: {details}|


## <a name="error-0141"></a>Erro 0141  
 Ocorrerá uma exceção se o número das colunas numéricas selecionadas e os valores exclusivos nas colunas categóricas e de cadeia de caracteres forem muito pequenos.  

 No Azure Machine Learning, esse erro ocorrerá quando não houver valores exclusivos suficientes na coluna selecionada para executar a operação.  

**Resolução:** algumas operações executam operações estatísticas em colunas de recursos e categóricas e, se não houver valores suficientes, a operação poderá falhar ou retornar um resultado inválido. Verifique o conjunto de dados para ver quantos valores existem nas colunas de recurso e rótulo e determine se a operação que você está tentando executar é estatisticamente válida.  

 Se o conjunto de dados de origem for válido, você também poderá verificar se alguma operação de metadados ou manipulação de dados upstream tiver alterado os dados e removido alguns valores.  

 Se as operações upstream incluírem a divisão, amostragem ou reamostragem, verifique se as saídas contêm o número esperado de linhas e valores.  

|Mensagens de Exceção|
|------------------------|
|O número das colunas numéricas e de valores exclusivos selecionados nas colunas categóricas e de cadeia de caracteres é pequeno demais.|
|O número total das colunas numéricas e valores exclusivos selecionados nas colunas categóricas e de cadeia de caracteres (no momento, {actual_num}) deve ser pelo menos {lower_boundary}.|


## <a name="error-0154"></a>Erro 0154  
 Ocorrerá uma exceção quando um usuário tentar unir dados em colunas de chave com tipo de coluna incompatível.

|Mensagens de Exceção|
|------------------------|
|Os tipos de elementos de coluna de chave não são compatíveis.|
|Os tipos de elementos de coluna de chave não são compatíveis (esquerda: {keys_left}; direita: {keys_right})|


## <a name="error-0155"></a>Erro 0155  
 Ocorrerá uma exceção quando os nomes de coluna de conjunto de dados não forem cadeias de caracteres.

|Mensagens de Exceção|
|------------------------|
|O nome da coluna dataframe deve ser do tipo cadeia de caracteres. Nomes de coluna não são cadeia de caracteres.|
|O nome da coluna dataframe deve ser do tipo cadeia de caracteres. Os nomes de coluna {column_names} não são cadeias de caracteres.|


## <a name="error-0156"></a>Erro 0156  
 Ocorrerá uma exceção quando houver falha na leitura de dados do Banco de Dados SQL do Azure.

|Mensagens de Exceção|
|------------------------|
|Falha ao ler dados do Banco de Dados SQL do Azure.|
|Falha ao ler dados do Banco de Dados SQL do Azure: {detailed_message} BD: {database_server_name}:{database_name} Consulta: {sql_statement}|


## <a name="error-0157"></a>Erro 0157  
 Armazenamento de dados não encontrado.

|Mensagens de Exceção|
|------------------------|
|As informações do armazenamento de dados são inválidas.|
|As informações do armazenamento de dados são inválidas. Falha ao obter o armazenamento de dados AzureML '{datastore_name}' no workspace '{workspace_name}'.|


## <a name="error-0158"></a>Erro 0158
 Gerado quando um diretório de transformação é inválido.

|Mensagens de Exceção|
|------------------------------------------------------------|
|O TransformationDirectory fornecido é inválido.|
|TransformationDirectory "{arg_name}" é inválido. Motivo: {reason}. Execute o teste de treinamento novamente que gera o arquivo de transformação. Se o teste de treinamento tiver sido excluído, crie novamente e salve o arquivo de transformação.|
|TransformationDirectory "{arg_name}" é inválido. Motivo: {reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>Erro 0159
 Ocorrerá uma exceção se o diretório de modelo de módulo for inválido. 

|Mensagens de Exceção|
|------------------------------------------------------------|
|O ModelDirectory fornecido é inválido.|
|ModelDirectory "{arg_name}" é inválido.|
|ModelDirectory "{arg_name}" é inválido. Motivo: {reason}.|
|ModelDirectory "{arg_name}" é inválido. Motivo: {reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>Erro 1000  
Exceção de biblioteca interna.  

Esse erro é fornecido para capturar erros de mecanismo interno sem tratamento de outra forma. Portanto, a causa desse erro poderá ser diferente dependendo do módulo que gerou o erro.  

Para obter mais ajuda, recomendamos que você poste a mensagem detalhada que acompanha o erro no fórum de [Azure Machine Learning](/answers/topics/azure-machine-learning.html), junto com uma descrição do cenário, incluindo os dados usados como entradas. Esses comentários nos ajudarão a priorizar erros e identificar os problemas mais importantes para um trabalho adicional.  

|Mensagens de Exceção|
|------------------------|
|Exceção de biblioteca.|
|Exceção de biblioteca: {exception}.|
|Exceção de biblioteca desconhecida: {exception}. {customer_support_guidance}.|


## <a name="execute-python-script-module"></a>Executar módulo de script Python

Pesquise **em azureml_main** em **70_Driver_logs** do **módulo executar script python** e você poderá encontrar o erro de linha que ocorreu. Por exemplo, "File"/tmp/tmp01_ID/user_script. py ", line 17, in azureml_main" indica que o erro ocorreu na linha 17 do seu script Python.
