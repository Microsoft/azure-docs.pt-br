---
title: Novidades na versão
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as atualizações mais recentes para o Serviço do Azure Machine Learning e o aprendizado de máquina e SDKs de Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: f39c914bce3fbc47775a76f1c3a1fb64de560505
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498328"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notas de versão do serviço de aprendizado de máquina do Azure

Neste artigo, conheça os lançamentos de serviços do Aprendizado de Máquina do Azure.  Para obter o conteúdo completo de referência do SDK, visite a página de referência do [**SDK principal do Azure Machine Learning para Python**](https://aka.ms/aml-sdk) .

Veja [a lista de problemas conhecidos](resource-known-issues.md) para aprender sobre erros e soluções conhecidas.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v 1.0.53

+ **Novos recursos**
    + O Machine Learning automatizado agora dá suporte a modelos de ONNX de treinamento no destino de computação remota
  + Azure Machine Learning agora fornece a capacidade de retomar o treinamento de uma execução anterior, um ponto de verificação ou arquivos de modelo.
    + Saiba como [usar os estimadores para retomar o treinamento de uma execução anterior](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correções de bugs e melhorias**
  + **automl-Client-Core-nativeClient**
    + Corrigir o bug sobre os tipos de colunas perder após a transformação (bug vinculado); 
    + Permitir que y_query seja um tipo de objeto que contém nenhum (s) no início (#459519).
  + **azure-cli-ml**
    + Os comandos da CLI "implantação de modelo" e "atualização de serviço" agora aceitam parâmetros, arquivos de configuração ou uma combinação dos dois. Parâmetros têm precedência sobre atributos em arquivos.
    + A descrição do modelo agora pode ser atualizada após o registro
  + **azureml-automl-Core**
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar suporte para avaliadores Nimbus ML & pipelines a serem usados em estimadores AutoML.
    + Corrigindo um bug no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Habilite a reutilização de algumas featurizations em divisões de CV para tarefas de previsão. Isso acelera o tempo de execução da configuração executada por aproximadamente um fator de n_cross_validations para featurizations dispendiosas, como atrasos e janelas sem interrupção.
    + Resolver um problema se o tempo estiver fora do intervalo de tempo com suporte do pandas. Agora, geramos uma DataException se o tempo for menor que PD. Timestamp. min ou maior que PD. Timestamp. Max
    + A previsão agora permite diferentes frequências em conjuntos de treinamento e teste se eles puderem ser alinhados. Por exemplo, "começando trimestralmente em Janeiro" e em "a partir de outubro", a cada trimestre, pode ser alinhado.
    + A propriedade "Parameters" foi adicionada ao TimeSeriesTransformer.
    + Remova as classes de exceção antigas.
    + Em tarefas de previsão, o `target_lags` parâmetro agora aceita um único valor inteiro ou uma lista de inteiros. Se o inteiro tiver sido fornecido, apenas uma latência será criada. Se uma lista for fornecida, os valores exclusivos de retardo serão feitos. target_lags = [1, 2, 2, 4] será criado com atraso de um, 2 e 4 períodos.
    + Corrigir o bug sobre a perda de tipos de colunas após a transformação (bug vinculado);
    + No `model.forecast(X, y_query)`, permitir que y_query seja um tipo de objeto que contém nenhum (s) no início (#459519).
    + Adicionar valores esperados à saída de automl
  + **azureml-contrib-descompasso**
    +  Melhorias no notebook de exemplo, incluindo alternar para o azureml-opendatasets em vez de azureml-contrib-opendatasets e melhorias de desempenho ao aprimorar dados
  + **azureml-contrib-explicação-modelo**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Segmentações adicionadas a explicações de imagem no explicador de imagem para o AzureML-contrib-explique-Model pacote
    + Adicionar suporte esparso scipy para LimeExplainer
    + Adicione batch_size ao explicador de imitação quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correção para chamar set_featurizer_timeseries_params (): tipo de valor de dicto alterar e verificação nula-Adicionar bloco de anotações para timeseries featurizer
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML 
    + Corrigido o bug com o carregamento do repositório de armazenamento em que uma pasta `target_path` vazia é criada, se iniciada com`/`
    + Correção do problema de DeepCopy em ServicePrincipalAuthentication.
    + Adicionados os comandos "AZ ml Environment show" e "AZ ml Environment List" à CLI.
    + Os ambientes agora dão suporte à especificação de um base_dockerfile como uma alternativa a um base_image já criado.
    + A configuração RunConfiguration não utilizada auto_prepare_environment foi marcada como preterida.
    + A descrição do modelo agora pode ser atualizada após o registro
    + Bugfix: O modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Correção do bug que imprimiu a duração em branco para implantações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + Melhores exceções de falha na criação do espaço de trabalho. De modo que os usuários não vejam "não é possível criar o espaço de trabalho. Não é possível localizar... " como a mensagem e, em vez disso, veja a falha de criação real.
    + Adicione suporte para autenticação de token em WebServices AKS. 
    + Adicionar `get_token()` método a `Webservice` objetos.
    + Adicionado suporte à CLI para gerenciar os conjuntos de computadores de aprendizado de máquina.
    + `Datastore.register_azure_blob_container`Agora, opcionalmente, `blob_cache_timeout` usa um valor (em segundos) que configura os parâmetros de montagem do blobfuse para habilitar a expiração do cache para esse repositório de armazenamento. O padrão é sem tempo limite, ou seja, quando um blob é lido, ele permanecerá no cache local até que o trabalho seja concluído. A maioria dos trabalhos preferirá essa configuração, mas alguns trabalhos precisarão ler mais dados de um grande DataSet do que caberá em seus nós. Para esses trabalhos, o ajuste desse parâmetro irá ajudá-lo a ter sucesso. Tome cuidado ao ajustar esse parâmetro: definir o valor muito baixo pode resultar em baixo desempenho, pois os dados usados em uma época podem expirar antes de serem usados novamente. Isso significa que todas as leituras serão feitas do armazenamento de BLOBs (ou seja, a rede) em vez do cache local, o que afeta negativamente os tempos de treinamento.
    + A descrição do modelo agora pode ser atualizada corretamente após o registro
    + A exclusão de modelo e imagem agora fornece mais informações sobre objetos upstream que dependem deles, o que causa a falha da exclusão
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
  + **azureml-dataprep**
    + Os objetos Dataflow agora podem ser iterados, produzindo uma sequência de registros.
    + Adicione `_summarize_each` como um recurso experimental `azureml.dataprep.Dataflow`ao.
  + **azureml-explain-model**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Adicionar suporte esparso scipy para LimeExplainer
    + adicionado o wrapper de explicador linear shap, bem como outro nível ao explicador tabular para explicar modelos lineares
    + para o explicador de imitação em explicar a biblioteca de modelos, erro fixo quando include_local = false para entrada de dados esparsas
    + Adicionar valores esperados à saída de automl
    + correção do recurso de permutação fixa quando o argumento de transformações foi fornecido para obter a importância do recurso bruto
    + Adicione batch_size ao explicador de imitação quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
    + para a biblioteca de explicabilidade de modelo, os explicadores Blackbox fixos em que a entrada do dataframe do pandas é necessária para previsão
    + Correção de um bug `explanation.expected_values` em que às vezes retornaria um float em vez de uma lista com um float.
  + **azureml-mlflow**
    + Melhorar o desempenho de mlflow. Set _experiment (experiment_name)
    + Corrigir o bug em uso de InteractiveLoginAuthentication para mlflow tracking_uri
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
    + Melhorar a documentação do pacote azureml-mlflow
    + Bug de patch em que mlflow. log _artifacts ("my_dir") salvaria artefatos em "my_dir/< artefato-Paths >" em vez de "< artefato-Paths >"
  + **azureml-opendatasets**
    + Fixe pyarrow de opendatasets em versões antigas (< 0.14.0) devido a um problema de memória introduzido recentemente.
    +  Mova o azureml-contrib-opendatasets para o azureml-opendatasets. -Permitir que as classes de conjunto de recursos abertas sejam registradas no espaço de trabalho AML e aproveitar as funcionalidades do conjunto de recursos AML diretamente. -Melhorar significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-pipeline-steps**
    + O DBFS datastore agora tem suporte para entradas e saídas em DatabricksStep.
    + Documentação atualizada para a etapa do lote do Azure com relação a entradas/saídas.
    + Em AzureBatchStep, alterou o valor padrão de *delete_batch_job_after_finish* para *true*.
  + **azureml – telemetria**
    +  Mova o azureml-contrib-opendatasets para o azureml-opendatasets. -Permitir que as classes de conjunto de recursos abertas sejam registradas no espaço de trabalho AML e aproveitar as funcionalidades do conjunto de recursos AML diretamente. -Melhorar significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-train-automl**
    + Documentação atualizada em get_output para refletir o tipo de retorno real e fornecer observações adicionais sobre a recuperação de propriedades de chave.
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar valores esperados à saída de automl
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado
    + A configuração RunConfiguration não utilizada auto_prepare_environment foi marcada como preterida.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface visual
+ **Recursos de visualização**
  + Módulo "executar script R" adicionado na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v 1.0.48

+ **Novos recursos**
  + **azureml-opendatasets**
    + o **azureml-contrib-opendatasets** agora está disponível como **azureml-opendatasets**. O pacote antigo ainda pode funcionar, mas é recomendável usar o **azureml-opendatasets** avançando para obter recursos e aprimoramentos mais avançados.
    + Esse novo pacote permite que você registre conjuntos de registros abertos como DataSet no espaço de trabalho AML e aproveite quaisquer funcionalidades oferecidas pelo conjunto de recursos.
    + Ele também inclui recursos existentes, como o consumo de conjuntos de valores abertos como o pandas/SPARK dataframes e junções de local para alguns conjuntos de um conjunto de recursos, como clima.

+ **Recursos de visualização**
    + HyperDriveConfig agora pode aceitar o objeto de pipeline como um parâmetro para dar suporte ao ajuste de hiperparâmetro usando um pipeline.

+ **Correções de bugs e melhorias**
  + **azureml-train-automl**
    + Corrigido o bug sobre a perda de tipos de colunas após a transformação.
    + Foi corrigido o bug para permitir que y_query seja um tipo de objeto que contém nenhum (s) no início. 
    + Corrigido o problema no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Corrigido o problema com as configurações whitelist_models e blacklist_models no AutoMLStep.
    + Correção do problema que impedia o uso de pré-processamento quando AutoML teria sido usado no contexto de pipelines do Azure ML.
  + **azureml-opendatasets**
    + Você moveu o azureml-contrib-opendatasets para o azureml-opendatasets.
    + As classes Open DataSet permitidas foram registradas no espaço de trabalho AML e aproveitam as funcionalidades do conjunto de recursos AML diretamente.
    + Desempenho aprimorado do NoaaIsdWeather enriquecer com uma versão não SPARK significativamente.
  + **azureml-explain-model**
    + Documentação online atualizada para objetos de interpretação.
    + Adição de batch_size ao explicador de imitação quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel.
    + Correção do problema em `explanation.expected_values` que às vezes retornaria um float em vez de uma lista com um float.
    + Adicionados valores esperados à saída automl para o explicador de imitação em explicar a biblioteca de modelos.
    + Correção do recurso de permutação corrigida quando o argumento de transformações foi fornecido para obter a importância do recurso bruto.
    + Adição de batch_size para simular explicador quando include_local = false para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel para a biblioteca de explicabilidade de modelo.
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML.
    + Corrigido o problema com o carregamento do repositório de armazenamento em que uma pasta `target_path` vazia é `/`criada, se iniciada com.
    + Comparação habilitada de dois conjuntos de valores.
    + O modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Preterida a configuração RunConfiguration não utilizada em auto_prepare_environment.
  + **azureml-mlflow**
    + Melhor utilização de recursos de execuções remotas que usam o azureml. mlflow.
    + A documentação do pacote azureml-mlflow foi aprimorada.
    + Correção do problema em que mlflow. log _artifacts ("my_dir") salvaria artefatos em "my_dir/artefato-Paths" em vez de "artefato-Paths".
  + **azureml-pipeline-core**
    + O parâmetro hash_paths para todas as etapas de pipeline foi preterido e será removido no futuro. Por padrão, o conteúdo de pasta_de_origem tem hash (exceto os arquivos listados em. amlignore ou. gitignore)
    + Continuando melhorando o módulo e o ModuleStep para dar suporte a módulos específicos do tipo de computação, em preparação para a integração do RunConfiguration e outras alterações para desbloquear seu uso em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Documentação aprimorada com relação a entradas/saídas.
    + AzureBatchStep: Valor padrão delete_batch_job_after_finish alterado para true.
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado.
    + Preterida a configuração RunConfiguration não utilizada em auto_prepare_environment.
    + Parâmetros `conda_dependencies_file_path` preteridos `pip_requirements_file_path` e em favor `conda_dependencies_file` de `pip_requirements_file` e, respectivamente.
  + **azureml-opendatasets**
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>SDK v 1.1.8 do Azure Machine Learning data Prep

+ **Novos recursos**
 + Os objetos Dataflow agora podem ser iterados, produzindo uma sequência de registros. Consulte a documentação `Dataflow.to_record_iterator`do.

+ **Correções de bugs e melhorias**
 + Aumentou a robustez do dataprep SDK.
 + Tratamento aprimorado de quadros de bits pandas com índices de coluna que não são de cadeia de caracteres.
 + Melhorou o desempenho `to_pandas_dataframe` do em conjuntos de os.
 + Correção de um bug em que a execução do Spark de conjuntos de valores falhou quando executada em um ambiente com vários nós.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>SDK v 1.1.7 do Azure Machine Learning data Prep

Revertemos uma alteração que melhorou o desempenho, pois ela estava causando problemas para alguns clientes usando Azure Databricks. Se você recebeu um problema em Azure Databricks, você pode atualizar para a versão 1.1.7 usando um dos métodos abaixo:
1. Execute este script para atualizar:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recrie o cluster, que instalará a versão mais recente do SDK de preparação de dados.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK para Python v 1.0.45

+ **Novos recursos**
  + Adicionar modelo substituto de árvore de decisão ao explicador imitador em azureml-explique-pacote de modelo
  + Capacidade de especificar uma versão do CUDA a ser instalada em imagens do inferência. Suporte para CUDA 9,0, 9,1 e 10,0.
  + As informações sobre as imagens de base de treinamento do Azure ML agora estão disponíveis no [repositório do GitHub de contêineres do Azure ml](https://github.com/Azure/AzureML-Containers) e [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Adicionado suporte à CLI para o agendamento do pipeline. Execute "AZ ml pipeline-h" para saber mais
  + Adicionado o parâmetro namespace kubernetes personalizado à CLI e à configuração de implantação do AKS WebService.
  + Parâmetro hash_paths preterido para todas as etapas de pipeline
  + Model. Register agora dá suporte ao registro de vários arquivos individuais como um único modelo com o `child_paths` uso do parâmetro.
  
+ **Recursos de visualização**
    + Os explicadores de Pontuação agora podem, opcionalmente, salvar informações de Conda e Pip para serialização e desserialização mais confiáveis.
    + Correção de bug do seletor de recurso automático.
    + Atualizado mlflow. azureml. build_image para a nova API, bugs com patches expostos pela nova implementação.

+ **Correções de bugs e melhorias**
  + A dependência paramiko foi removida do azureml-Core. Foram adicionados avisos de substituição para métodos de anexação de destino de computação herdados.
  + Melhorar o desempenho de Run. create_children
  + Em explicador de imitação com classificador binário, corrija a ordem das probabilidades quando a probabilidade de professor for usada para dimensionar valores de shap
  + Tratamento de erros e mensagem aprimorados para o aprendizado de máquina automatizado. 
  + Correção do problema de tempo limite de iteração para o aprendizado de máquina automatizado.
  + Melhorou o desempenho de transformação da série temporal para o aprendizado de máquina automatizado.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>SDK v 1.1.6 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Funções de resumo adicionadas para valores`SummaryFunction.TOPVALUES`principais () e valores`SummaryFunction.BOTTOMVALUES`inferiores ().

+ **Correções de bugs e melhorias**
  + Melhorou significativamente o desempenho `read_pandas_dataframe`do.
  + Foi corrigido um bug que causava `get_profile()` uma falha em um fluxo de arquivos que apontasse para arquivo binário.
  + Exposto `set_diagnostics_collection()` para permitir a habilitação/desabilitação programática da coleção de telemetria.
  + Alterou o comportamento `get_profile()`de. Os valores NaN agora são ignorados por min, mean, STD e Sum, que se alinha com o comportamento de pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK para Python v 1.0.43

+ **Novos recursos**
  + O Azure Machine Learning agora fornece suporte de primeira classe para aprendizado de máquina e estrutura de análise de dados populares Scikit. Usando o estimador, os usuários podem facilmente treinar e implantar modelos Scikit-learn. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + Saiba como [executar o ajuste de hiperparâmetro com Scikit-Learn usando o hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Adição de suporte para a criação de ModuleStep em pipelines junto com classes de módulo e ModuleVersion para gerenciar unidades de computação reutilizáveis.
  + Os WebServices ACI agora dão suporte a scoring_uri persistentes por meio de atualizações. O scoring_uri será alterado de IP para FQDN. O rótulo de nome DNS para FQDN pode ser configurado definindo o dns_name_label em deploy_configuration. 
  + Novos recursos do Machine Learning automatizados:
    + Featurizer STL para previsão
    + O clustering do KMeans está habilitado para a limpeza de recursos
  + As aprovações de cota AmlCompute se tornaram mais rápidas! Agora, automatizamos o processo para aprovar suas solicitações de cota dentro de um limite. Para obter mais informações sobre como as cotas funcionam, saiba [como gerenciar cotas](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Recursos de visualização**
    + Integração com o [MLflow](https://mlflow.org) 1.0.0 Tracking por meio do pacote azureml-MLflow (notebooks de[exemplo](https://aka.ms/azureml-mlflow-examples)).
    + Envie o bloco de anotações Jupyter como uma execução. [Documentação de referência de API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Visualização pública do [detector](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) de descompasso de dados por meio do pacote azureml-contrib-descompasso (notebooks de[exemplo](https://aka.ms/azureml-datadrift-example)). A descompasso de dados é uma das principais razões em que a precisão do modelo diminui com o passar do tempo. Acontece quando os dados servidos para o modelo na produção são diferentes dos dados em que o modelo foi treinado. O detector de descompasso de dados AML ajuda o cliente a monitorar descompasso de dados e envia alerta sempre que o descompasso é detectado. 

+ **Alterações da falha**

+ **Correções de bugs e melhorias**
  + O RunConfiguration Load e Save dá suporte à especificação de um caminho de arquivo completo com compatibilidade total para o comportamento anterior.
  + Cache adicionado em ServicePrincipalAuthentication, desativado por padrão.
  + Habilite o registro em log de várias plotagens com o mesmo nome de métrica.
  + A classe de modelo agora é adequadamente importável de azureml`from azureml.core import Model`. Core ().
  + Nas etapas do pipeline `hash_path` , o parâmetro agora é preterido. O novo comportamento é o hash completo de pasta_de_origem, exceto os arquivos listados em. amlignore ou. gitignore.
  + Em pacotes de pipeline, `get_all` vários `get_all_*` métodos e foram preteridos em favor `list` de `list_*`e, respectivamente.
  + o azureml. Core. Get _run não requer mais classes a serem importadas antes de retornar o tipo de execução original.
  + Corrigido um problema em que algumas chamadas para a atualização do WebService não dispararam uma atualização.
  + O tempo limite de pontuação em WebServices AKS deve estar entre 5 ms e 300000ms. O scoring_timeout_ms máximo permitido para solicitações de pontuação foi aumentado de 1 min a 5 min.
  + Os objetos LocalWebservice agora `scoring_uri` têm `swagger_uri` Propriedades e.
  + A criação do diretório de saídas movidos e o carregamento do diretório de saídas do processo do usuário. O SDK do histórico de execução habilitado deve ser executado em cada processo do usuário. Isso deve resolver alguns problemas de sincronização experientes por execuções de treinamento distribuídas.
  + O nome do log do azureml gravado no nome do processo do usuário agora incluirá o nome do processo (somente para treinamento distribuído) e PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>SDK v 1.1.5 do Azure Machine Learning data Prep

+ **Correções de bugs e melhorias**
  + Para valores de data e hora interpretados que têm um formato de ano de 2 dígitos, o intervalo de anos válidos foi atualizado para corresponder ao Windows pode ser liberado. O intervalo foi alterado de 1930-2029 para 1950-2049.
  + Ao ler um arquivo e configurar `handleQuotedLineBreaks=True`, `\r` será tratado como uma nova linha.
  + Corrigido um bug que causou `read_pandas_dataframe` a falha em alguns casos.
  + Desempenho aprimorado `get_profile`do.
  + Mensagens de erro aprimoradas.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>SDK v 1.1.4 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora você pode usar as seguintes funções de linguagem de expressão para extrair e analisar valores DateTime em novas colunas.
    + `RegEx.extract_record()`extrai elementos DateTime em uma nova coluna.
    + `create_datetime()`cria objetos DateTime a partir de elementos DateTime separados.
  + Ao chamar `get_profile()`, agora você pode ver que as colunas Quantil são rotuladas como (est.) para indicar claramente que os valores são aproximações.
  + Agora você pode usar o * * mascaramento ao ler do armazenamento de BLOBs do Azure.
    + Por exemplo: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correções de bug**
  + Correção de um bug relacionado à leitura de um arquivo parquet de uma origem remota (blob do Azure).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK para Python v 1.0.39
+ **Alterações**
  + A opção executar auto_prepare_environment de configuração está sendo preterida, com a preparação automática se tornando o padrão.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>SDK v 1.1.3 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Adicionado suporte para leitura de um banco de dados PostgresSQL, seja chamando read_postgresql ou usando um armazenamento.
    + Consulte os exemplos nos guias de instruções:
      + [Bloco de anotações de ingestão de dados](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Bloco de anotações do repositório de armazenamento](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correções de bugs e melhorias**
  + Problemas corrigidos com a conversão de tipo de coluna:
  + Agora, converte corretamente uma coluna Booleana ou numérica em uma coluna Booleana.
  + Agora não falha ao tentar definir uma coluna de data como tipo de data.
  + Os tipos de JoinType aprimorados e a documentação de referência acompanhada. Ao unir dois fluxos de entrada, agora você pode especificar um destes tipos de junção:
    + NENHUM, CORRESPONDER, INTERNO, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Tipo de dados aprimorado inferência para reconhecer mais formatos de data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal do Azure

No portal do Azure, agora você pode:
+ Criar e executar experimentos de ML automatizados 
+ Crie uma VM do notebook para experimentar os notebooks Jupyter de exemplo ou seus próprios.
+ Seção de criação de nova marca (versão prévia) no espaço de trabalho Machine Learning serviço, que inclui Machine Learning automatizado, interface visual e VMs de notebooks hospedados
    + Criar automaticamente um modelo usando o Machine Learning automatizado 
    + Usar uma interface visual de arrastar e soltar para executar experimentos
    + Crie uma VM de notebook para explorar dados, criar modelos e implantar serviços.
+ Gráfico dinâmico e atualização de métrica em executar relatórios e páginas de detalhes de execução
+ Atualizado o Visualizador de arquivos para logs, saídas e instantâneos em páginas de detalhes de execução.
+ Nova e aprimorada experiência de criação de relatórios na guia experimentos. 
+ Foi adicionada a capacidade de baixar o arquivo config. JSON da página Visão geral do espaço de trabalho do serviço de Azure Machine Learning.
+ Suporte à criação de Machine Learning espaço de trabalho de serviço do espaço de trabalho Azure Databricks 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK para Python v 1.0.33
+ **Novos recursos**
  + O método _Workspace. Create_ agora aceita configurações de cluster padrão para clusters de CPU e GPU.
  + Se a criação do espaço de trabalho falhar, os recursos dependentes serão limpos.
  + O SKU do registro de contêiner do Azure padrão foi alternado para básico.
  + O registro de contêiner do Azure é criado lentamente, quando necessário para a execução ou criação de imagem.
  + Suporte para ambientes para execuções de treinamento.

### <a name="notebook-virtual-machine"></a>Máquina virtual do notebook 

Use uma VM de notebook como um ambiente de hospedagem seguro e pronto para a empresa para notebooks Jupyter nos quais você pode programar experimentos de aprendizado de máquina, implantar modelos como pontos de extremidade da Web e executar todas as outras operações com suporte pelo SDK Azure Machine Learning usando Python. Ele fornece vários recursos:
+ [Crie rapidamente uma VM](quickstart-run-cloud-notebook.md) de notebook pré-configurada que tenha a versão mais recente do SDK do Azure Machine Learning e os pacotes relacionados.
+ O acesso é protegido por meio de tecnologias comprovadas, como HTTPS, autenticação e autorização de Azure Active Directory.
+ Armazenamento em nuvem confiável de blocos de anotações e código em sua conta de armazenamento de BLOBs Workspace do Azure Machine Learning. Você pode excluir com segurança sua VM do bloco de anotações sem perder seu trabalho.
+ Blocos de anotações de exemplo pré-instalados para explorar e experimentar Azure Machine Learning recursos de serviço.
+ Recursos de personalização completa de VMs do Azure, qualquer tipo de VM, qualquer pacote, qualquer driver. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v 1.0.33 lançado.

+ O Modelos de Aceleração de Hardware do Azure ML no [FPGAs](concept-accelerate-with-fpgas.md) está disponível para o público em geral.
  + Agora você pode [usar o pacote azureml-da aceleração extra-Models](how-to-deploy-fpga-web-service.md) para:
    + Treinar os pesos de uma rede neural profunda com suporte (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usar o aprendizado de transferência com o DNN com suporte
    + Registrar o modelo com o Serviço Gerenciamento de Modelos e colocar o modelo em contêiner
    + Implantar o modelo em uma VM do Azure com um FPGA em um cluster do AKS (serviço kubernetes do Azure)
  + Implantar o contêiner em um dispositivo de servidor [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Pontuar seus dados com o ponto de extremidade gRPC com este [exemplo](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizado de Machine Learning

+ Limpeza de recursos para habilitar a adição dinâmica de featurizers para otimização de desempenho. Novos featurizers: incorporações de trabalho, peso de evidência, codificações de destino, codificação de destino de texto, distância do cluster
+ Smart currículo para lidar com as divisões de treinamento/válidas dentro do ML automatizado
+ Poucas alterações de otimização de memória e melhoria no desempenho de tempo de execução
+ Melhoria de desempenho na explicação do modelo
+ Conversão de modelo ONNX para execução local
+ Suporte adicionado à subamostragem
+ Parando inteligente quando não há critérios de saída definidos
+ Conjuntos empilhados

+ Previsão de série temporal
  + Nova função prever previsão   
  + Agora você pode usar a validação cruzada de origem sem interrupção em dados de série temporal
  + Nova funcionalidade adicionada para configurar o tempo de retardo da série temporal 
  + Nova funcionalidade adicionada para dar suporte a recursos agregados de janela sem interrupção
  + Nova detecção de feriados e featurizer quando o código do país é definido nas configurações do experimento

+ Azure Databricks
  + Funcionalidade de previsão de série temporal e modelo de explainabilty/interpretação habilitada
  + Agora você pode cancelar e retomar (continuar) experimentos de ML automatizados
  + Suporte adicionado para processamento de vários núcleos

### <a name="mlops"></a>MLOps
+ **Implantação local & depuração para contêineres de Pontuação**<br/> Agora você pode implantar um modelo de ML localmente e iterar rapidamente em seu arquivo de Pontuação e dependências para garantir que eles se comportem conforme o esperado.

+ **Introduziu o modelo de & InferenceConfig. Deploy ()**<br/> A implantação de modelo agora dá suporte à especificação de uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implantação do modelo foi simplificada para um único comando.

+ **Acompanhamento de referência do git**<br/> Os clientes estão solicitando recursos básicos de integração do git por algum tempo, pois ele ajuda a manter uma trilha de auditoria de ponta a ponta. Implementamos o rastreamento em entidades principais no Azure ML para metadados relacionados ao git (repositório, confirmação, estado de limpeza). Essas informações serão coletadas automaticamente pelo SDK e pela CLI.

+ **Criação de perfil do modelo & serviço de validação**<br/> Os clientes freqüentemente reclamam da dificuldade de dimensionar corretamente a computação associada ao seu serviço de inferência. Com nosso serviço de criação de perfil de modelo, o cliente pode fornecer entradas de exemplo e criarei um perfil em 16 diferentes configurações de CPU/memória para determinar o dimensionamento ideal para a implantação.

+ **Traga sua própria imagem base para inferência**<br/> Outra reclamação comum foi a dificuldade de migrar da experimentação para a inferência recompartilhamento de dependências. Com nosso novo recurso de compartilhamento de imagem base, agora você pode reutilizar suas imagens base de experimentação, dependências e todas, para inferência. Isso deve acelerar as implantações e reduzir a lacuna do loop interno para o exterior.

+ **Experiência de geração de esquema do Swagger aprimorada**<br/> Nosso método de geração Swagger anterior era propenso a erros e impossível de ser automatizado. Temos uma nova maneira embutida de gerar esquemas do Swagger de qualquer função do Python por meio de decoradores. Nós abrimos esse código e nosso protocolo de geração de esquema não está acoplado à plataforma ML do Azure.

+ **O CLI do Azure ML está geralmente disponível (GA)**<br/> Os modelos agora podem ser implantados com um único comando da CLI. Temos comentários comuns do cliente que ninguém implanta um modelo de ML de um notebook Jupyter. A [**documentação de referência da CLI**](https://aka.ms/azmlcli) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v 1.0.30 lançado.

O [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) foi introduzido para adicionar uma nova versão de um pipeline publicado enquanto mantém o mesmo ponto de extremidade.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning o SDK da preparação de dados v 1.1.2

Observação: O SDK do Python de preparação de dados `numpy` não `pandas` será mais instalado e empacotado. Consulte [as instruções de instalação atualizadas](https://aka.ms/aml-data-prep-installation).

+ **Novos recursos**
  + Agora você pode usar a transformação dinâmica.
    + Guia de instruções: [Bloco de anotações dinâmico](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora você pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora você pode usar `to_upper`  o `to_lower` e o Functions na linguagem de expressão.
  + Agora você pode ver o número de valores exclusivos de cada coluna em um perfil de dados.
  + Para algumas das etapas mais usadas do leitor, agora você pode passar o `infer_column_types` argumento. Se estiver definido como, `True`a preparação de dados tentará detectar e converter automaticamente os tipos de coluna.
    + `inference_arguments`Agora está preterido.
  + Agora você pode chamar `Dataflow.shape`.

+ **Correções de bugs e melhorias**
  + `keep_columns` Agora aceita um argumento `validate_column_exists`opcional adicional, que verifica se o resultado de `keep_columns` conterá qualquer coluna.
  + Todas as etapas do leitor (que lêem de um arquivo) agora aceitam um `verify_exists`argumento opcional adicional.
  + Desempenho aprimorado de leitura do data frame do pandas e obtenção de perfis de dados.
  + Correção de um bug em que houve falha na divisão de uma única etapa de um Dataflow com um único índice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora você pode reenviar um script existente executado em um cluster de computação remota existente. 
  + Agora você pode executar um pipeline publicado com novos parâmetros na guia pipelines. 
  + Os detalhes da execução agora dão suporte a um novo Visualizador de arquivos de instantâneo. Você pode exibir um instantâneo do diretório ao enviar uma execução específica. Você também pode baixar o bloco de anotações que foi enviado para iniciar a execução.
  + Agora você pode cancelar as execuções pai do portal do Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK para Python v 1.0.23

+ **Novos recursos**
  + O SDK do Azure Machine Learning agora dá suporte ao Python 3,7.
  + Azure Machine Learning os estimadores DNN agora fornecem suporte interno a várias versões. Por exemplo, `TensorFlow`  o estimador agora `framework_version` aceita um parâmetro e os usuários podem especificar a versão ' 1,10 ' ou ' 1,12 '. Para obter uma lista das versões com suporte na versão atual do SDK, `get_supported_versions()` chame na classe da estrutura desejada (por exemplo `TensorFlow.get_supported_versions()`,).
  Para obter uma lista das versões com suporte da versão mais recente do SDK, consulte a documentação do estimador do [DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>SDK v 1.1.1 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Você pode ler várias fontes de datastore/caminho de email/datareferente usando transformações de read_ *.
  + Você pode executar as seguintes operações em colunas para criar uma nova coluna: divisão, piso, módulo, potência, comprimento.
  + A preparação de dados agora faz parte do pacote de diagnóstico do Azure ML e registrará em log as informações de diagnóstico por padrão.
    + Para desativar essa opção, defina essa variável de ambiente como true: DISABLE_DPREP_LOGGER

+ **Correções de bugs e melhorias**
  + Documentação de código aprimorada para classes e funções usadas com frequência.
  + Correção de um bug em auto_read_file que falhou ao ler arquivos do Excel.
  + Opção adicionada para substituir a pasta em read_pandas_dataframe.
  + Desempenho aprimorado da instalação de dependência do dotnetcore2 e adição de suporte para Fedora 27/28 e Ubuntu 1804.
  + Melhorou o desempenho da leitura de BLOBs do Azure.
  + A detecção de tipo de coluna agora dá suporte a colunas do tipo Long.
  + Correção de um bug em que alguns valores de data estavam sendo exibidos como carimbos de hora em vez de objetos DateTime de Python.
  + Corrigido um bug em que algumas contagens de tipo estavam sendo exibidas como duplos em vez de inteiros.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v 1.0.21

+ **Novos recursos**
  + O método *azureml. Core. Run. create_children* permite a criação de baixa latência de várias execuções filhas com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>SDK da preparação de dados do Azure Machine Learning v 1.1.0

+ **Alterações da falha**
  + O conceito do pacote de preparação de dados foi preterido e não tem mais suporte. Em vez de persistir vários fluxos de entrada em um pacote, você pode persistir os fluxos de entrada individualmente.
    + Guia de instruções: [Abrindo e salvando o notebook de fluxos de anotações](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Novos recursos**
  + A preparação de dados agora pode reconhecer colunas que correspondem a um tipo semântico específico e divididas de acordo. Os STypes atualmente suportados incluem: endereço de email, coordenadas geográficas (Latitude & longitude), endereços IPv4 e IPv6, número de telefone dos EUA e CEP dos EUA.
    + Guia de instruções: [Bloco de anotações de tipos semânticos](https://aka.ms/aml-data-prep-semantic-types-nb)
  + A preparação de dados agora dá suporte às seguintes operações para gerar uma coluna resultante de duas colunas numéricas: subtrair, multiplicar, dividir e módulo.
  + Você pode chamar `verify_has_data()` em um Dataflow para verificar se o Dataflow produziria registros, se executado.

+ **Correções de bugs e melhorias**
  + Agora você pode especificar o número de compartimentos a serem usados em um histograma para perfis de coluna numérica.
  + Agora `read_pandas_dataframe` , a transformação requer que o dataframe tenha nomes de coluna com tipo de cadeia de caracteres ou de byte.
  + Corrigido um bug na `fill_nulls` transformação, em que os valores não foram preenchidos corretamente se a coluna estava ausente.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v 1.0.18

 + **Alterações**
   + O pacote azureml-tensorboard substitui o azureml-contrib-tensorboard.
   + Com esta versão, você pode configurar uma conta de usuário em seu amlcompute (cluster de computação gerenciada), ao criá-la. Isso pode ser feito passando essas propriedades na configuração de provisionamento. Você pode encontrar mais detalhes na [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>SDK v 1.0.17 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora dá suporte à adição de duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Correções de bugs e melhorias**
  + Melhorou a documentação e a verificação de parâmetros para random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>SDK v 1.0.16 do Azure Machine Learning data Prep

+ **Correção de bug**
  + Correção de um problema de autenticação de entidade de serviço que foi causado por uma alteração de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK para Python v 1.0.17

+ **Novos recursos**

  + Azure Machine Learning agora fornece suporte de primeira classe para o DNN Framework popular. Usar [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) usuários de classe pode facilmente treinar e implantar modelos de encadeamento.
    + Saiba como [executar o treinamento distribuído com o ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Saiba como [executar o ajuste de hiperparâmetro com o encadeamento usando hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines adicionou a capacidade disparam uma execução de pipeline com base em modificações no armazenamento de Datastore. O [notebook de agendamento](https://aka.ms/pl-schedule) de pipeline é atualizado para demonstrar esse recurso.

+ **Correções de bugs e melhorias**
  + Adicionamos suporte Azure Machine Learning pipelines para definir a propriedade source_directory_data_store para um repositório de armazenamento desejado (como um armazenamento de BLOBs) no [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) que são fornecidos para o [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por padrão, as etapas usam o repositório de arquivos do Azure como o repositório de armazenamento de backup, que pode ter problemas de limitação quando um grande número de etapas é executado simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Nova experiência de editor de tabela de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna do bem para a área de tabela em que uma visualização da tabela será exibida. As colunas podem ser reorganizadas.
  + Novo Visualizador de arquivos de logs
  + Links para execuções de teste, computação, modelos, imagens e implantações na guia atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>SDK v 1.0.15 do Azure Machine Learning data Prep

+ **Novos recursos**
  + A preparação de dados agora dá suporte à gravação de fluxos de arquivo de um fluxo de arquivos. Também fornece a capacidade de manipular os nomes de fluxo de arquivo para criar novos nomes de arquivo.
    + Guia de instruções: [Trabalhando com o bloco de anotações de fluxos de arquivos](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correções de bugs e melhorias**
  + Desempenho aprimorado de t-Digest em conjuntos de dados grandes.
  + A preparação de dados agora dá suporte à leitura de dados de um caminho de data.
  + Uma codificação ativa agora funciona em colunas booleanas e numéricas.
  + Outras correções de bugs diversas.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>SDK do Azure Machine Learning para Python v1.0.15

+ **Novos recursos**
  + Azure Machine Learning pipelines adicionou AzureBatchStep ([Notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (Notebook) e a funcionalidade de agendamento baseada em tempo ([Notebook](https://aka.ms/pl-schedule)).
  +  DataTranferStep atualizado para funcionar com o Azure SQL Server e banco de dados do Azure para PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Alterações**
  + `PublishedPipeline.get_published_pipeline` preterido em favor de `PublishedPipeline.get`.
  + `Schedule.get_schedule` preterido em favor de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.12

+ **Novos recursos**
  + A preparação de dados agora oferece suporte à leitura de um banco de dados SQL do Azure usando o armazenamento de dados.
 
+ **Alterações**
  + Melhorou o desempenho da memória de determinadas operações em dados grandes.
  + `read_pandas_dataframe()` agora exige que `temp_folder` seja especificado.
  + A propriedade `name` em `ColumnProfile` foi preterida - usar `column_name` em vez disso.

## <a name="2019-01-28"></a>28/01/2019

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>SDK do Azure Machine Learning para Python v1.0.10

+ **Alterações**: 
  + O SDK do Azure Machine Learning não tem mais pacotes azure-cli como dependência. Especificamente, as dependências azure-cli-core e azure-cli-profile foram removidas de azureml-core. Estas são alterações que afetam o usuário:
    + Se você estiver executando "AZ login" e usando o azureml-SDK, o SDK fará o navegador ou o log de código do dispositivo em mais uma vez. Ele não usará nenhum estado de credencial criado por "az login".
    + Para autenticação de CLI do Azure, assim como usando "az login", use a classe _azureml.core.authentication.AzureCliAuthentication_. Para a autenticação de CLI do Azure, execute _pip install azure-cli_ no ambiente do Python em que você instalou o azureml-sdk.
    + Se você estiver usando "az login" e usando uma entidade de serviço para a automação, é recomendável usar a classe _azureml.core.authentication.ServicePrincipalAuthentication_, pois o azureml-sdk não usará o estado de credenciais criado pela CLI do Azure. 

+ **Correções de bug**: Esta versão contém principalmente correções de bugs secundários

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.8

+ **Correções de bug**
  + Melhorou o desempenho da obtenção de perfis de dados.
  + Bugs secundários relacionados ao relatório de erros foram corrigidos.
  
### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Nova experiência de criação de gráficos de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna ou um atributo do poço à área do gráfico, na qual o sistema selecionará automaticamente um tipo de gráfico apropriado para o usuário com base no tipo de dados. Os usuários podem alterar o tipo de gráfico para outros tipos aplicáveis ou adicionar outros atributos.

    Tipos de gráfico compatíveis:
    - Gráfico de linhas
    - Histograma
    - Gráfico de barras empilhadas
    - Gráfico de caixa
    - Gráfico de Dispersão
    - Gráfico de bolhas
+ O portal agora gera relatórios dinamicamente para experimentos. Quando um usuário envia uma execução a um experimento, um relatório será gerado automaticamente com métricas registradas em log e gráficos para permitir a comparação entre diferentes execuções. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>SDK do Azure Machine Learning para Python v1.0.8

+ **Correções de bug**: Esta versão contém principalmente correções de bugs secundários

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.7

+ **Novos recursos**
  + Aprimoramentos do repositório de dados (documentado em [Guia de instruções do repositório de dados](https://aka.ms/aml-data-prep-datastore-nb))
    + Capacidade adicional de ler e gravar no compartilhamento de Arquivo do Azure e em Repositórios de Dados do ADLS em expansão.
    + Ao usar repositórios de dados, a preparação de dados agora dá suporte ao uso à autenticação de entidade de serviço em vez da autenticação interativa.
    + Adicionado suporte para URLs de wasb e wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.6

+ **Correções de bug**
  + Corrigido o bug com a leitura de contêineres de Blob do Azure legíveis públicos no Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>SDK do Azure Machine Learning para Python v1.0.6
+ **Correções de bug**: Esta versão contém principalmente correções de bugs secundários

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>SDK de preparação de dados do Azure Machine Learning v1.0.4

+ **Novos recursos**
  + A função `to_bool` agora permite que os valores incompatíveis sejam convertidos em valores de erro. Esse é o novo comportamento de incompatibilidade de padrão para `to_bool` e `set_column_types`, enquanto o comportamento padrão anterior era para converter valores incompatíveis como False.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores ausente/nulos em colunas numéricas como NaN.
  + Capacidade adicional para verificar o tipo de retorno de algumas expressões para garantir a consistência de tipo e falha antecipada.
  + Agora você pode chamar `parse_json` para analisar valores em uma coluna como objetos JSON e expandi-las em várias colunas.

+ **Correções de bug**
  + Corrigido um bug que travou `set_column_types` no Python 3.5.2.
  + Corrigido um bug que falhou ao se conectar ao armazenamento de dados usando uma imagem do AML.

+ **Atualizações**
  * [Notebooks de exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de Introdução, estudos de caso e guias de instruções.

## <a name="2018-12-04-general-availability"></a>04/12/2018: Disponibilidade geral

O Serviço do Azure Machine Learning já está disponível ao público em geral.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com esta versão, estamos anunciando uma nova experiência de computação gerenciada por meio da [Computação do Machine Learning](how-to-set-up-training-targets.md#amlcompute). Esse destino de computação substitui a computação do IA do Lote do Azure para o Azure Machine Learning. 

O destino de computação:
+ É usado para treinamento de modelo e inferência/Pontuação de lote
+ É único - para computação de vários nós
+ Realiza o gerenciamento de cluster e o plano de trabalho para o usuário
+ Dimensionado automaticamente por padrão
+ Suporte para recursos de CPU e GPU 
+ Permite o uso de VMs de baixa prioridade para redução de custos

A Computação do Azure Machine Learning pode ser criada no Python, pelo portal do Azure ou pela CLI. Precisa ser criada na região do seu workspace e não pode ser anexada a outro workspace. Esse destino de computação usa um contêiner do Docker para execução e empacota as dependências para replicar o mesmo ambiente em todos os nós.

> [!Warning]
> É recomendável criar um novo workspace para usar a Computação do Azure Machine Learning. Há uma possibilidade remota de que os usuários que tentarem criar a Computação do Azure Machine Learning em um workspace existente recebam um erro. A computação existente no workspace deve continuar a funcionar sem problemas.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>SDK do Azure Machine Learning para Python v1.0.2
+ **Alterações da falha**
  + Nesta versão, removemos o suporte para a criação de uma VM do Azure Machine Learning. Ainda é possível anexar uma VM em nuvem existente ou um servidor local remoto. 
  + Também removemos o suporte para o IA do Lote, agora, o suporte para todos ocorrerá por meio da Computação do Azure Machine Learning.

+ **Novo**
  + Para pipelines do aprendizado de máquina:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Updated**
  + Para pipelines do aprendizado de máquina:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) aceita runconfig agora
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) agora copia de e para uma fonte de dados SQL
    + Agendar a funcionalidade no SDK para criar e atualizar agendas e executar pipelines publicados

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>SDK de preparação de dados do Azure Machine Learning v0.5.2
+ **Alterações da falha** 
  * `SummaryFunction.N` foi renomeado para `SummaryFunction.Count`.
  
+ **Correções de bug**
  * Use o Token de Execução do AML mais recente ao ler e gravar em armazenamentos de dados ou execuções remotas. Anteriormente, se o Token de Execução do AML fosse atualizado no Python, o tempo de execução de preparação de dados não seria atualizado com o novo Token de Execução do AML.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() não falhará mais quando o Spark usar a serialização `Kryo`
  * Agora, o Inspetor de Contagem de Valor pode mostrar mais de mil valores únicos
  * Não haverá mais falha na Divisão Aleatória se o Fluxo de Dados original não tiver um nome  

+ **Mais informações**
  * [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e notebooks
+ Pipelines de ML
  + Notebooks novos e atualizados para começar a ver exemplos de pipelines, escopo de lote e transferência de estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [executar previsões em lotes usando pipelines](how-to-run-batch-predictions.md)
+ Computação de destino do Azure Machine Learning
  + [Notebooks de exemplo](https://aka.ms/aml-notebooks) agora são atualizados para usar essa nova computação gerenciada.
  + [Saiba mais sobre essa computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Crie e gerencie tipos de [Computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) no portal.
+ Monitore o uso de cota e a [cota de solicitações](how-to-manage-quotas.md) da Computação do Azure Machine Learning.
+ Veja o status do cluster da Computação do Machine Learning em tempo real.
+ Foi adicionado suporte à rede virtual para a criação de Computação do Azure Machine Learning e do Serviço de Kubernetes do Azure.
+ Execute novamente os pipelines publicados com parâmetros existentes.
+ Novos [gráficos de aprendizado de máquina automatizado](how-to-understand-automated-ml.md) para modelos de classificação (gráfico de importância de recursos de comparação de precisão e calibragem com explicabilidade de modelo) e modelos de regressão (resíduos e gráfico de importância de recursos com explicabilidade de modelo). 
+ Os pipelines podem ser exibidos no portal do Azure




## <a name="2018-11-20"></a>20-11-2018

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>SDK do Azure Machine Learning para Python v0.1.80

+ **Alterações da falha** 
  * Namespace *azureml.Train.widgets* foi movido para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* substitui as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A última classe será removida nas versões subsequentes. A classe AmlCompute tem agora uma definição mais fácil e simplesmente precisa de um vm_size e do max_nodes e dimensionará automaticamente a seu cluster de 0 para o max_nodes quando um trabalho for enviado. Nossos [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) foram atualizados com essas informações e devem fornecer exemplos de uso. Esperamos que você goste dessa simplificação e dos muitos dos recursos mais interessantes para ficar em uma versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK de preparação de dados do AML v0.5.1 

Saiba mais sobre o SDK de preparação de dados lendo [docs de referência](https://aka.ms/data-prep-sdk).
+ **Novos recursos**
   * Criada uma nova CLI DataPrep para executar pacotes DataPrep e exibir o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reprojetado para melhorar a usabilidade
   * Smart_read_file renomeado para auto_read_file
   * Agora inclui distorção e curtose no Perfil de Dados
   * Pode criar amostra com amostragem estratificada
   * Pode ler de arquivos zip que contenham arquivos CSV
   * Pode dividir conjuntos de dados por linha com a divisão aleatória (por exemplo, nos conjuntos de teste-treinamento)
   * Pode obter os tipos de dados de coluna de um fluxo de dados ou um perfil de dados chamando `.dtypes`
   * Pode obter a contagem de linhas de um fluxo de dados ou um perfil de dados chamando `.row_count`

+ **Correções de bug**
   * Corrigidos para conversão dupla 
   * Ativo corrigido após qualquer adição de coluna 
   * Corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de classificação corrigida para respeitar a ordem de classificação de várias colunas
   * Fixos e/ou expressões para ser semelhante a como `pandas` lida com isso
   * Leitura corrigida do caminho dbfs
   * Mensagens de erro tornadas mais compreensíveis 
   * Agora não há mais falhar durante a leitura no destino de computação remota usando o token do AML
   * Agora não falhará na DSVM do Linux
   * Agora não falha quando os valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção ao fluxo de dados deve falhar corretamente
   * Agora dá suporte a locais de armazenamento dbutils montados no Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portal do Azure 
O portal do Azure para o serviço de AML tem as seguintes atualizações:
  * Uma nova **Pipelines** guia para pipelines publicados.
  * Adicionado suporte para anexar a um cluster HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>SDK de aprendizado de máquina do AML para Python v0.1.74

+ **Alterações da falha** 
  * Workspace.compute_targets, datastores, experimentos, imagens, modelos e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
  * *Run.get_context* pretere *Run.get_submitted_run*. O último método será removido em versões subsequentes.
  * A classe *PipelineData* agora espera um objeto de armazenamento de dados como um parâmetro em vez de datastore_name. Da mesma forma, o *Pipeline* aceita default_datastore em vez de default_datastore_name.

+ **Novos recursos**
  * O caderno de amostras de [amostra do AML](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) agora usa etapas de MPI.
  * O widget RunDetails para notebooks Jupyter é atualizado para mostrar uma visualização do pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK de preparação de dados do AML v0.4.0 
 
+ **Novos recursos**
  * Tipo de contagem adicionada ao perfil de dados 
  * Contagem de valores e histograma estão agora disponíveis
  * Mais percentis no perfil de dados
  * Mediana está disponível em Summarize
  * Agora há suporte para Python 3.7
  * Quando você salva um fluxo de dados que contém datastores em um pacote DataPrep, as informações do armazenamento de dados serão mantidas como parte do pacote DataPrep
  * A gravação no armazenamento de dados agora é suportada 
        
+ **Erro corrigido**
  * Extensões de inteiro não assinado de 64 bits agora são tratadas corretamente no Linux
  * Etiqueta de texto incorreta corrigida para arquivos de texto simples em smart_read
  * O tipo de coluna de cadeia de caracteres agora aparece na visualização de métricas
  * A contagem de tipos agora é fixada para mostrar ValueKinds mapeados para um único FieldType em vez de um individual
  * Write_to_csv não falha quando o caminho é fornecido como uma cadeia de caracteres
  * Ao usar Replace, deixar "localizar" em branco não falhará mais 

## <a name="2018-10-12"></a>12-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>SDK de aprendizado de máquina do Azure para Python v0.1.68

+ **Novos recursos**
  * Suporte a vários locatários ao criar novo espaço de trabalho.

+ **Bugs corrigidos**
  * Você não precisa fixar a versão da biblioteca pynacl ao implantar o serviço web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.3.0

+ **Novos recursos**
  * Adicionado método transform_partition_with_file (script_path), que permite aos usuários passar no caminho de um arquivo Python para executar

## <a name="2018-10-01"></a>01-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>SDK de aprendizado de máquina do Azure para Python v0.1.65
A [versão 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclui novos recursos, mais documentação, correções de bugs e mais [exemplos de blocos de notas](https://aka.ms/aml-notebooks).

Veja [a lista de problemas conhecidos](resource-known-issues.md) para aprender sobre erros e soluções conhecidas.

+ **Alterações da falha**
  * Workspace.Experiments, Workspace.models, Workspace.compute_targets, Workspace.images, dicionário de retorno Workspace.web_services, retornado anteriormente de lista. Veja a documentação da API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Automatizados de aprendizado de máquina removido erro normalizado quadrada da média das métricas principais.

+ **HyperDrive**
  * Várias correções de bugs do HyperDrive para Bayesian, melhorias de desempenho para obter chamadas de métricas. 
  * Atualização de Tensorflow 1.10 do 1.9 
  * Otimização de imagem do Docker para inicialização a frio. 
  * O trabalho agora reporta o status correto, mesmo se ele sair com um código de erro diferente de 0. 
  * Validação de atributo RunConfig no SDK. 
  * O objeto de execução HyperDrive suporta cancelamentos semelhantes a uma execução normal: não é necessário passar nenhum parâmetro. 
  * Melhorias de widget para manter o estado dos valores suspensos para execuções distribuídas e execuções do HyperDrive. 
  * TensorBoard e outros arquivos de log suportam fixos para o servidor Parameter. 
  * Suporte ao Intel (R) MPI no lado do serviço. 
  * Bugfix ao ajuste de parâmetro para correção de execução distribuída durante a validação no BatchAI. 
  * O Context Manager agora identifica a instância primária. 

+ **Experiência do portal do Azure**
  * log_table () e log_row () são suportados em detalhes da execução. 
  * Crie automaticamente gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna categórica opcional.

+ **Automatizado de Machine Learning**
  * Melhor tratamento e documentação de erros 
  * Corrigidos problemas de desempenho de recuperação de propriedades de execução. 
  * Fixo continuar a execução do problema. 
  * Correção de ensembling problemas de iteração.
  * Bug deslocada de treinamento fixo no MAC OS.
  * Curva média de solicitação de pull/ROC no cenário de validação personalizada da macro da resolução.
  * Removida a lógica extra de índice.
  * Removemos o filtro de get_output API.

+ **Pipelines**
  * Adicionado um método Pipeline.publish () para publicar um pipeline diretamente, sem precisar executar uma execução primeiro.   
  * Adicionado um método PipelineRun.get_pipeline_runs () para buscar as execuções de pipeline que foram geradas a partir de um pipeline publicado.

+ **Project Brainwave**
  * Suporte atualizado para novos modelos de IA disponíveis em FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.2.0
[Versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui recursos e correções de bug a seguir:

+ **Novos recursos**
  * Suporte para codificação one-hot
  * Suporte para a transformação de quantil
   
+ **Bug corrigido:**
  * Funciona com qualquer versão do Tornado, sem necessidade de rebaixar sua versão do Tornado
  * O valor conta para todos os valores, não apenas os três primeiros

## <a name="2018-09-public-preview-refresh"></a>09-2018 (atualização da visualização pública)

Uma nova versão atualizada do Azure Machine Learning: Leia mais sobre essa versão: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Serviço do Azure Machine Learning](../service/overview-what-is-azure-ml.md).
