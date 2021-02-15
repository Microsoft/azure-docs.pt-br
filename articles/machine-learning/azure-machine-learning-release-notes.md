---
title: Novidades na versão
titleSuffix: Azure Machine Learning
description: Saiba mais sobre as atualizações mais recentes para Azure Machine Learning e o aprendizado de máquina e SDKs do Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 09/10/2020
ms.openlocfilehash: c54034ef927bb49a955ef6121f5a8d56b57f0bd3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375554"
---
# <a name="azure-machine-learning-release-notes"></a>Notas de versão do Azure Machine Learning

Neste artigo, saiba mais sobre as versões de Azure Machine Learning.  Para obter o conteúdo completo de referência do SDK, visite a página de referência do [**SDK principal do Azure Machine Learning para Python**](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) .


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK para Python v 1.22.0
+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Corrigido o bug em que uma dependência Pip extra foi adicionada ao arquivo Conda yml para modelos de visão.
  + **azureml-automl-runtime**
    + Corrigido um bug em que modelos de previsão clássicas (por exemplo, AutoArima) poderiam receber dados de treinamento em que as linhas com valores de destino imputados não estavam presentes. Isso violou o contrato de dados desses modelos. * Corrigido vários bugs com o comportamento de atraso por ocorrência no operador de retardo da série temporal. Anteriormente, a operação de latência por ocorrência não marcou todas as linhas imputados corretamente e, portanto, nem sempre geraria os valores de retardo de ocorrência corretos. Também corrigidos alguns problemas de compatibilidade entre o operador de latência e o operador de janela sem interrupção com comportamento de latência por ocorrência. Isso resultou anteriormente no operador de janela sem interrupção, descartando algumas linhas dos dados de treinamento que deveria usar de outra forma.
  + **azureml-core**
    + Adicionando suporte para autenticação de token por público.
    + Adicione `process_count` ao [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) para dar suporte a trabalhos de PyTorch de vários nós de vários processos.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep?preserve-view=true&view=azure-ml-py) agora GA e não mais experimental.
    + [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?preserve-view=true&view=azure-ml-py): Adicione o argumento allowed_failed_count e allowed_failed_percent para verificar o limite de erros no mini nível do lote. O limite de erros tem 3 tipos agora:
       + error_threshold-o número de itens de mini-lote permitidos com falha; 
       + allowed_failed_count-o número de mini lotes permitidos com falha; 
       + allowed_failed_percent-o percentual de mini lotes permitidos com falha. 
       
       Um trabalho será interrompido se o exceder qualquer um deles. error_threshold é necessário para manter a compatibilidade com versões anteriores. Defina o valor como-1 para ignorá-lo.
    + Tratamento de espaço em branco fixo no nome do AutoMLStep.
    + O ScriptRunConfig agora tem suporte do HyperDriveStep
  + **azureml-train-core**
    + HyperDrive execuções invocadas de um ScriptRun agora serão consideradas uma execução filho.
    + Adicione `process_count` ao [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) para dar suporte a trabalhos de PyTorch de vários nós de vários processos.
  + **azureml-widgets**
    + Adicione o widget ParallelRunStepDetails para visualizar o status de um ParallelRunStep.
    + Permite que os usuários do hyperdrive vejam um eixo adicional no gráfico de coordenadas paralelas que mostra o valor da métrica correspondente a cada conjunto de hiperparâmetros para cada execução filho.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Experiência do Azure Machine Learning Studio notebooks (atualização de Janeiro)
+ **Novos recursos**
  + Editor de redução nativa no AzureML. Agora, os usuários podem renderizar e editar arquivos de redução nativamente no AzureML Studio.
  + [Botão Executar para scripts (. py,. R e. sh)](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#run-a-notebook-or-python-script). Agora, os usuários podem executar o script Python, R e bash no AzureML
  + [Gerenciador de variáveis](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#explore-variables-in-the-notebook). Explore o conteúdo de variáveis e quadros de dados em um painel pop-up. Os usuários podem verificar facilmente o tipo de dados, o tamanho e o conteúdo.
  + [Tabela de conteúdo](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#navigate-with-a-toc). Navegue até as seções do seu bloco de anotações, indicadas por cabeçalhos de redução.
  + Exporte seu bloco de anotações como LaTeX/HTML/py. Crie arquivos de bloco de anotações fáceis de compartilhar exportando para LaTex, HTML ou. py
  + Intellicode. Os resultados com base no ML fornecem uma [experiência de autocompletação inteligente](https://docs.microsoft.com/visualstudio/intellicode/overview)aprimorada.

+ **Correções de bugs e melhorias**
  + Tempos de carregamento de página aprimorados
  + desempenho aprimorado 
  + Velocidade aprimorada e confiabilidade do kernel
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK para Python v 1.21.0
+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Corrigido o texto de ajuda da CLI ao usar o AmlCompute com a identidade do userassigned
  + **azureml-contrib-automl-DNN-Vision**
    + Os botões implantar e baixar ficarão visíveis para as execuções da visão AutoML, e os modelos poderão ser implantados ou baixados de forma semelhante a outras execuções do AutoML. Há dois novos arquivos (scoring_file_v_1_0_0. py e conda_env_v_1_0_0. yml) que contêm um script para executar inferência e um arquivo yml para recriar o ambiente Conda. O arquivo ' Model. PTH ' também foi renomeado para usar a extensão '. pt '.
  + **azureml-core**
    + Suporte a MSI para Azure-CLI-ml
    + Suporte à identidade gerenciada atribuída pelo usuário.
    + Com essa alteração, os clientes devem ser capazes de fornecer uma identidade atribuída pelo usuário que possa ser usada para buscar a chave do cofre de chaves do cliente para criptografia em repouso.
    +  corrigir row_count = 0 para o perfil de arquivos muito grandes – correção de erro na conversão dupla para valores delimitados com preenchimento de espaço em branco
    + Remover sinalizador experimental da GA do conjunto de resultados de saída
    + Atualizar a documentação sobre como buscar uma versão específica de um modelo
    + Permitir atualização de espaço de trabalho para acesso de modo misto no caso de link privado
    + Correção para remover o registro adicional no repositório de armazenamento para o recurso de retomada de execução
    + Adicionado suporte de CLI/SDK para atualizar a identidade de espaço de trabalho atribuída ao usuário principal
  + **azureml-interpret**
    + atualizado o azureml-interpretar para o 0.16.0 da comunidade de interpretação
    + otimizações de memória para o cliente de explicação no azureml-interpret
  + **azureml-train-automl-runtime**
    + Streaming habilitado para execuções de ADB
  + **azureml-train-core**
    + Correção para remover o registro adicional no repositório de armazenamento para o recurso de retomada de execução
  + **azureml-widgets**
    + Os clientes não devem ver alterações na visualização de dados de execução existente usando o widget e agora terão suporte se usarem opcionalmente hiperparâmetros condicionais.
    + O widget executar usuário agora inclui uma explicação detalhada de por que uma execução está no estado enfileirado.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK para Python v 1.20.0
+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + framework_version adicionado em OptimizationConfig. Ele será usado quando o modelo for registrado com o Framework MULTI.
  + **azureml-contrib-otimização**
    + framework_version adicionado em OptimizationConfig. Ele será usado quando o modelo for registrado com o Framework MULTI.
  + **azureml-pipeline-steps**
    + Apresentando o CommandStep que usaria o comando para processar. O comando pode incluir executáveis, comandos do Shell, scripts, etc.
  + **azureml-core**
    + Agora a criação do espaço de trabalho dá suporte à identidade atribuída pelo usuário. Adicionando o suporte a UAI do SDK/CLI
    + Corrigido o problema no serviço. recarregue () para obter alterações em score.py na implantação local.
    + `run.get_details()` tem um campo extra chamado "submittedBy", que exibe o nome do autor para esta execução.
    + Documentação do método edited Model. Register para mencionar como registrar o modelo de executar diretamente
    + Corrigido IOT-Server problema de tratamento de alteração de status de conexão.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Experiência do Azure Machine Learning Studio notebooks (atualização de dezembro)
+ **Novos recursos**
  + Pesquisa de nome de arquivo de usuário. Agora, os usuários podem pesquisar todos os arquivos salvos em um espaço de trabalho.
  + Redução do suporte lado a lado por célula do notebook. Em uma célula de notebook, os usuários agora podem ter a opção de exibir a redução renderizada e a sintaxe de redução lado a lado.
  + Barra de status da célula. A barra de status indica em qual estado uma célula de código se encontra, se uma execução de célula foi bem-sucedida e quanto tempo levou para ser executada. 
   
+ **Correções de bugs e melhorias**
  + Tempos de carregamento de página aprimorados
  + desempenho aprimorado 
  + Velocidade aprimorada e confiabilidade do kernel

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK para Python v 1.19.0
+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Adicionado suporte experimental para dados de teste para AutoMLStep.
    + Adicionada a implementação principal inicial do recurso de ingestão de conjunto de teste.
    + Referências movidas para sklearn. externals. joblib para depender diretamente de joblib.
    + Introduza um novo tipo de tarefa AutoML de "Image-Instance-segmentation".
  + **azureml-automl-runtime**
    + Adicionada a implementação principal inicial do recurso de ingestão de conjunto de teste.
    + Quando todas as cadeias de caracteres em uma coluna de texto têm um comprimento de exatamente 1 caractere, o TfIdf Word-Gram featurizer não funciona porque seu criador ignora as cadeias de caracteres com menos de 2. A alteração de código atual permitirá que o AutoML manipule esse caso de uso.
    + Introduza um novo tipo de tarefa AutoML de "Image-Instance-segmentation".
  + **azureml-contrib-automl-DNN-NLP**
    + PR inicial para o novo pacote DNN-NLP
  + **azureml-contrib-automl-DNN-Vision**
    + Introduza um novo tipo de tarefa AutoML de "Image-Instance-segmentation".
  + **azureml-contrib-automl-pipeline-steps**
    + Esse novo pacote é responsável pela criação de etapas necessárias para vários cenários de treinamento/inferência de modelos. -Ele também move o código de treinamento/inferência no pacote azureml. Train. automl. Runtime para que as correções futuras sejam disponibilizadas automaticamente por meio de versões de ambiente organizadas.
  + **azureml-contrib-dataset**
    + Introduza um novo tipo de tarefa AutoML de "Image-Instance-segmentation".
  + **azureml-core**
    + Adicionada a implementação principal inicial do recurso de ingestão de conjunto de teste.
    + Corrigindo os avisos de xref para a documentação no pacote azureml-Core
    + Correção de cadeia de caracteres do documento para o recurso de suporte de comando no SDK
    + Adicionando a propriedade Command a RunConfiguration. O recurso permite que os usuários executem um comando ou executáveis reais na computação por meio do SDK do AzureML.
    + Os usuários podem excluir um experimento vazio, considerando a ID desse experimento.
  + **azureml-dataprep**
    + Adicionado suporte a DataSet para Spark criado com escala de 2,12. Isso adiciona ao suporte 2,11 existente.
  + **azureml-mlflow**
    + AzureML-MLflow adiciona proteções seguras em scripts remotos para evitar o encerramento antecipado de execuções enviadas.
  + **azureml-pipeline-core**
    + Correção de um bug na definição de um pipeline padrão para o ponto de extremidade de pipeline criado via interface do usuário
  + **azureml-pipeline-steps**
    + Adicionado suporte experimental para dados de teste para AutoMLStep.
  + **azureml-tensorboard**
    + Corrigindo os avisos de xref para a documentação no pacote azureml-Core
  + **azureml-train-automl-client**
    + Adicionado suporte experimental para dados de teste para AutoMLStep.
    + Adicionada a implementação principal inicial do recurso de ingestão de conjunto de teste.
    + Introduza um novo tipo de tarefa AutoML de "Image-Instance-segmentation".
  + **azureml-train-automl-runtime**
    + Adicionada a implementação principal inicial do recurso de ingestão de conjunto de teste.
    + Corrija a computação das explicações brutas para o melhor modelo de AutoML se os modelos de AutoML forem treinados usando validation_size configuração.
    + Referências movidas para sklearn. externals. joblib para depender diretamente de joblib.
  + **azureml-train-core**
    + O HyperDriveRun.get_children_sorted_by_primary_metric () deve ser concluído mais rapidamente agora
    + Melhor tratamento de erros no SDK do HyperDrive.
    +  Todas as classes estimadas em favor do uso de ScriptRunConfig para configurar execuções de experimento. As classes preteridas incluem:
        + MMLBase
        + Avaliador
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Preterido o uso de Nccl e gloo como tipos de entrada válidos para classes de estimação em favor de usar PyTorchConfiguration com ScriptRunConfig.
    + Preterido o uso de MPI como um tipo de entrada válido para classes de estimação em favor de usar MpiConfiguration com ScriptRunConfig.
    + Adicionando a propriedade Command a RunConfiguration. O recurso permite que os usuários executem um comando ou executáveis reais na computação por meio do SDK do AzureML.

    +  Todas as classes estimadas em favor do uso de ScriptRunConfig para configurar execuções de experimento. As classes preteridas incluem: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Preterido o uso de Nccl e gloo como um tipo válido de entrada para classes estimadas em favor de usar PyTorchConfiguration com ScriptRunConfig. 
    + Foi preterido o uso de MPI como um tipo válido de entrada para classes estimadas em favor de usar MpiConfiguration com ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Experiência do Azure Machine Learning Studio notebooks (atualização de novembro)
+ **Novos recursos**
   + Terminal nativo. Agora, os usuários terão acesso a um terminal integrado, bem como a uma operação git por meio do [terminal integrado.](./how-to-access-terminal.md)
  + Duplicar pasta 
  + Lista suspensa de custos para computação 
  + Pylance de computação offline 

+ **Correções de bugs e melhorias**
  + Tempos de carregamento de página aprimorados
  + desempenho aprimorado 
  + Velocidade aprimorada e confiabilidade do kernel
  + Carregamento de arquivo grande. Agora você pode carregar o arquivo >95MB

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK para Python v 1.18.0
+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    +  Manipulação aprimorada da série temporal curta, permitindo que elas sejam preenchedas com ruído gaussiano.
  + **azureml-automl-runtime**
    + Lançar Configexception se uma coluna DateTime tiver um valor OutOfBoundsDatetime
    + Manipulação aprimorada da série temporal curta, permitindo que elas sejam preenchedas com ruído gaussiano.
    + Verificando se cada coluna de texto pode aproveitar a transformação Char-Gram com o intervalo de n-Gram com base no comprimento das cadeias de caracteres nessa coluna de texto
    + Fornecendo explicações de recursos brutos para o melhor modo para experimentos AutoML em execução na computação local do usuário
  + **azureml-core**
    + Fixe o pacote: pyjwt para evitar o pull de versões mais recentes em versões futuras.
    + A criação de um experimento retornará o teste ativo ou último arquivado com o mesmo nome fornecido, se esse experimento existir ou um novo experimento.
    + Chamar get_experiment por nome retornará o experimento ativo ou último arquivado com o nome fornecido.
    + Os usuários não podem renomear um experimento ao reativá-lo.
    + Mensagem de erro aprimorada para incluir correções em potencial quando um conjunto de um DataSet é passado incorretamente para um experimento (por exemplo, ScriptRunConfig). 
    + Documentação aprimorada do `OutputDatasetConfig.register_on_complete` para incluir o comportamento do que acontecerá quando o nome já existir.
    + Especificar os nomes de entrada e saída do conjunto de dados que têm o potencial de colidir com variáveis de ambiente comuns resultará em um aviso
    + Parâmetro de realocação `grant_workspace_access` ao registrar os repositórios de armazenamento. Defina-o como `True` para acessar dados por trás da rede virtual do Machine Learning Studio.
      [Saiba mais](./how-to-enable-studio-virtual-network.md)
    + A API de serviço vinculado está refinada. Em vez de fornecer a ID do recurso, temos 3 parâmetros separados sub_id, RG e Name definidos na configuração.
    + Para permitir que os clientes resolvam automaticamente os problemas de corrupção de token, habilite a sincronização de token de espaço de trabalho para ser um método público.
    + Essa alteração permite que uma cadeia de caracteres vazia seja usada como um valor para um script_param
  + **azureml-train-automl-client**
    +  Manipulação aprimorada da série temporal curta, permitindo que elas sejam preenchedas com ruído gaussiano.
  + **azureml-train-automl-runtime**
    + Lançar Configexception se uma coluna DateTime tiver um valor OutOfBoundsDatetime
    + Suporte adicionado para fornecer explicações de recursos brutos para o melhor modelo para experimentos AutoML em execução na computação local do usuário
    + Manipulação aprimorada da série temporal curta, permitindo que elas sejam preenchedas com ruído gaussiano.
  + **azureml-train-core**
    + Essa alteração permite que uma cadeia de caracteres vazia seja usada como um valor para um script_param
  + **azureml-Train-restclients-hyperdrive**
    + O LEIAme foi alterado para oferecer mais contexto
  + **azureml-widgets**
    + Adicione suporte de cadeia de caracteres à biblioteca de coordenadas de gráficos/paralelas para widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Rotulação de dados para segmentação de instância de imagem (anotação de polígono) (visualização)

O tipo de projeto de segmentação de instância de imagem (anotações de polígono) no rótulo de dados está disponível agora, para que os usuários possam desenhar e anotar com polígonos em torno da delimitação dos objetos nas imagens. Os usuários poderão atribuir uma classe e um polígono a cada objeto de interesse em uma imagem.

Saiba mais sobre [rotulagem de segmentação de instância de imagem](how-to-label-images.md).



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK para Python v 1.17.0
+ **novos exemplos**
  + Um novo repositório controlado pela comunidade de exemplos está disponível em https://github.com/Azure/azureml-examples
+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Corrigido um problema em que get_output pode gerar um XGBoostError.
  + **azureml-automl-runtime**
    + Os recursos baseados em tempo/calendário criados pelo AutoML agora terão o prefixo.
    + Correção de um IndexError ocorrido durante o treinamento de StackEnsemble para conjuntos de volumes de conjunto de valores com um grande número de classes e a subamostra habilitada.
    + Corrigido um problema em que as previsões de VotingRegressor podem ser imprecisas depois de reajustar o modelo.
  + **azureml-core**
    + Detalhes adicionais adicionados sobre a relação entre a configuração de implantação do AKS e os conceitos do serviço kubernetes do Azure.
    + Suporte a rótulos de cliente de ambiente. O usuário pode rotular os ambientes e referenciá-los por rótulo.
  + **azureml-dataprep**
    + Melhor mensagem de erro ao usar o Spark sem suporte no momento com escala de 2,12.
  + **azureml-explain-model**
    + O pacote azureml-explique-Model foi oficialmente preterido
  + **azureml-mlflow**
    + Foi resolvido um bug em mlflow. Projects. Execute no back-end do azureml em que o estado final não foi manipulado corretamente.
  + **azureml-pipeline-core**
    + Adicione suporte para criar, listar e obter agenda de pipeline com base em um ponto de extremidade de pipeline.
    +  Melhorou a documentação de PipelineData.as_dataset com um exemplo de uso inválido-usar PipelineData.as_dataset de forma inadequada agora resultará em um Valorexception sendo lançado
    + O bloco de anotações de pipelines HyperDriveStep foi alterado para registrar o melhor modelo em um PipelineStep diretamente após a execução do HyperDriveStep.
  + **azureml-pipeline-steps**
    + O bloco de anotações de pipelines HyperDriveStep foi alterado para registrar o melhor modelo em um PipelineStep diretamente após a execução do HyperDriveStep.
  + **azureml-train-automl-client**
    + Corrigido um problema em que get_output pode gerar um XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Experiência do Azure Machine Learning Studio notebooks (atualização de outubro)
+ **Novos recursos**
  + [Suporte completo à rede virtual](./how-to-enable-studio-virtual-network.md)
  + [Modo de foco](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Salvar blocos de anotações Ctrl-S
  + Números de Linha

+ **Correções de bugs e melhorias**
  + Melhoria em velocidade e confiabilidade do kernel
  + Atualizações da interface do usuário do widget Jupyter

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK para Python v 1.16.0
+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + AKSWebservice e AKSEndpoints agora dão suporte a limites de recursos de CPU e memória no nível de Pod. Esses limites opcionais podem ser usados por configuração `--cpu-cores-limit` e `--memory-gb-limit` sinalizadores nas chamadas da CLI aplicáveis
  + **azureml-core**
    + Fixe as principais versões de dependências diretas do azureml-Core
    + AKSWebservice e AKSEndpoints agora dão suporte a limites de recursos de CPU e memória no nível de Pod. Mais informações sobre [os recursos e limites do kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Atualizado run.log_table para permitir que linhas individuais sejam registradas.
    + Método estático adicionado `Run.get(workspace, run_id)` para recuperar uma execução somente usando um espaço de trabalho 
    + Método de instância adicionado `Workspace.get_run(run_id)` para recuperar uma execução no espaço de trabalho
    + Introdução à Propriedade Command na configuração de execução que permitirá que os usuários enviem comando em vez de argumentos de & de script.
  + **azureml-interpret**
    + correção da explicação do comportamento do sinalizador de is_raw do cliente no azureml-interpret
  + **azureml-sdk**
    + `azureml-sdk` suporte oficialmente Python 3,8.
  + **azureml-train-core**
    + Adicionando o ambiente organizado do TensorFlow 2,3
    + Introdução à Propriedade Command na configuração de execução que permitirá que os usuários enviem comando em vez de argumentos de & de script.
  + **azureml-widgets**
    + Interface reprojetada para widget execução de script.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK para Python v 1.15.0
+ **Correções de bugs e melhorias**
  + **azureml-contrib-interpret**
    + Explicador de verde-limão movido de azureml-contrib-interpret para interpretação-pacote da Comunidade e explicador de imagem removido do pacote azureml-contrib-interpret
    + painel de visualização removido do pacote azureml-contrib-interpret, explicação cliente movido para o pacote azureml-interpret e preterido no pacote azureml-contrib-interpret e notebooks atualizados para refletir a API aprimorada
    + Corrija as descrições do pacote PyPI para azureml-interpret, azureml-explique-Model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-contrib-notebook**
    + Fixe a dependência nbcovert para < 6 para que o Papermill 1. x continue a funcionar.
  + **azureml-core**
    + Parâmetros adicionados ao Construtor TensorflowConfiguration e MpiConfiguration para permitir uma inicialização mais simplificada dos atributos de classe sem exigir que o usuário defina cada atributo individual. Adicionada uma classe PyTorchConfiguration para configurar trabalhos PyTorch distribuídos no ScriptRunConfig.
    + Fixe a versão do Azure-MGMT-Resource para corrigir o erro de autenticação.
    + Triton de suporte sem implantação de código
    + os diretórios de saída especificados em Run.start_logging () agora serão controlados ao usar a execução em cenários interativos. Os arquivos rastreados serão visíveis em ML Studio após a chamada de Run. Complete ()
    + Agora, a codificação de arquivo pode ser especificada durante `Dataset.Tabular.from_delimited_files` a criação do conjunto de um com e `Dataset.Tabular.from_json_lines_files` passando o `encoding` argumento. As codificações com suporte são ' utf8 ', ' iso88591 ', ' latino1 ', ' ASCII ', UTF16 ', ' UTF32 ', ' utf8bom ' e ' windows1252 '.
    + Correção de bug quando o objeto de ambiente não é passado para o Construtor ScriptRunConfig.
    + Execução atualizada. Cancel () para permitir o cancelamento de uma execução local de outro computador.
  + **azureml-dataprep**
    +  Problemas fixos de tempo limite de montagem do conjunto de
  + **azureml-explain-model**
    + Corrija as descrições do pacote PyPI para azureml-interpret, azureml-explique-Model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-interpret**
    + painel de visualização removido do pacote azureml-contrib-interpret, explicação cliente movido para o pacote azureml-interpret e preterido no pacote azureml-contrib-interpret e notebooks atualizados para refletir a API aprimorada
    + o pacote azureml-interpretar foi atualizado para depender da 0.15.0 da comunidade de interpretação
    + Corrija as descrições do pacote PyPI para azureml-interpret, azureml-explique-Model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-pipeline-core**
    +  Correção do problema de pipeline com `OutputFileDatasetConfig` o local em que o sistema pode parar de responder quando `register_on_complete` é chamado com o `name` parâmetro definido como um nome de conjunto de um DataSet pré-existente.
  + **azureml-pipeline-steps**
    + Blocos de anotações do databricks obsoletos removidos.
  + **azureml-tensorboard**
    + Corrija as descrições do pacote PyPI para azureml-interpret, azureml-explique-Model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-train-automl-runtime**
    + painel de visualização removido do pacote azureml-contrib-interpret, explicação cliente movido para o pacote azureml-interpret e preterido no pacote azureml-contrib-interpret e notebooks atualizados para refletir a API aprimorada
  + **azureml-widgets**
    + painel de visualização removido do pacote azureml-contrib-interpret, explicação cliente movido para o pacote azureml-interpret e preterido no pacote azureml-contrib-interpret e notebooks atualizados para refletir a API aprimorada

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK para Python v 1.14.0
+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + A criação de perfil de grade foi removida do SDK e não tem mais suporte.
  + **azureml-accel-models**
    + o pacote azureml-da aceleração extra-Models agora dá suporte a Tensorflow 2. x
  + **azureml-automl-core**
    + O tratamento de erros foi adicionado em get_output para casos em que as versões locais do pandas/sklearn não correspondam às usadas durante o treinamento
  + **azureml-automl-runtime**
    + Corrigido um bug em que as iterações AutoArima falhariam com um PredictionException e a mensagem: "falha silenciosa durante a previsão".
  + **azureml-CLI-comum**
    + A criação de perfil de grade foi removida do SDK e não tem mais suporte.
  + **azureml-contrib-servidor**
    + Descrição da atualização do pacote para a página de visão geral do PyPI.
  + **azureml-core**
    + A criação de perfil de grade foi removida do SDK e não é mais suportada.
    + Reduza o número de mensagens de erro quando a recuperação do espaço de trabalho falhar.
    + Não mostrar aviso quando a busca de metadados falhar
    + Nova etapa Kusto e destino de computação Kusto.
    + Atualize o documento para o parâmetro SKU. Remova a SKU na funcionalidade de atualização do espaço de trabalho na CLI e no SDK.
    + Descrição da atualização do pacote para a página de visão geral do PyPI.
    + Documentação atualizada para ambientes do AzureML.
    + Expor configurações de recursos gerenciados de serviço para o espaço de trabalho AML no SDK.
  + **azureml-dataprep**
    + Habilitar a permissão EXECUTE em arquivos para montagem de conjunto de
  + **azureml-mlflow**
    + Documentação atualizada do AzureML MLflow e exemplos de notebook 
    + Novo suporte para projetos MLflow com back-end do AzureML
    + Suporte ao registro de modelo MLflow
    + Adicionado suporte do RBAC do Azure para operações de AzureML-MLflow 
    
  + **azureml-pipeline-core**
    + A documentação dos métodos PipelineOutputFileDataset.parse_ * foi aprimorada.
    + Nova etapa Kusto e destino de computação Kusto.
    + A propriedade Swaggerurl fornecida para a entidade de ponto de extremidade de pipeline por meio desse usuário pode ver a definição de esquema para o ponto de extremidade de pipeline publicado.
  + **azureml-pipeline-steps**
    + Nova etapa Kusto e destino de computação Kusto.
  + **azureml-telemetry**
    + Descrição da atualização do pacote para a página de visão geral do PyPI.
  + **azureml-treinar**
    + Descrição da atualização do pacote para a página de visão geral do PyPI.
  + **azureml-train-automl-client**
    + O tratamento de erros foi adicionado em get_output para casos em que as versões locais do pandas/sklearn não correspondam às usadas durante o treinamento
  + **azureml-train-core**
    + Descrição da atualização do pacote para a página de visão geral do PyPI.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK para Python v 1.13.0
+ **Versão prévia dos recursos**
  + **azureml-núcleo** Com a nova funcionalidade de conjuntos de novos de saída, você pode fazer write-back para o armazenamento em nuvem, incluindo BLOB, ADLS Gen 1, ADLS Gen 2 e FileShare. Você pode configurar o local de saída de dados, como gerar dados de saída (via montagem ou carregamento), se deseja registrar os dados de saída para futura reutilização e compartilhamento e passar dados intermediários entre as etapas do pipeline diretamente. Isso permite que o reprodução, o compartilhamento, impeça a duplicação de dados e resulte em eficiência de custo e ganhos de produtividade. [Saiba como usá-lo](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py)
    
+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Foi adicionado validated_ arquivo {Platform} _requirements.txt para fixar todas as dependências de Pip para AutoML.
    + Esta versão dá suporte a modelos maiores que 4 GB.
    + Dependências de AutoML atualizadas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-automl-runtime**
    + Defina horovod para Text DNN para sempre usar a compactação FP16.
    + Esta versão dá suporte a modelos maiores que 4 GB.
    + Corrigido o problema em que AutoML falha com ImportError: não é possível importar o nome `RollingOriginValidator` .
    + Dependências de AutoML atualizadas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-contrib-automl-DNN-previsão**
    + Dependências de AutoML atualizadas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-contrib-fairness**
    + Forneça uma breve descrição para o azureml-contrib-imparciality.
  + **azureml-contrib-pipeline-steps**
    + Mensagem adicionada indicando que este pacote foi preterido e o usuário deve usar o azureml-pipeline-Steps em vez disso.
  + **azureml-core**
    + Adicionado comando de chave de lista para o espaço de trabalho.
    + Adicionar parâmetro de marcas no SDK do espaço de trabalho e na CLI.
    + Corrigido o bug em que o envio de uma execução filho com o conjunto de um falhará devido a `TypeError: can't pickle _thread.RLock objects` .
    + Adicionando page_count padrão/documentação da lista de modelos ().
    + Modifique o SDK do&da CLI para obter o parâmetro adbworkspace e adicione o executor de ADB de espaço de trabalho/DESlink Runner.
    + Corrija o bug no DataSet. Update que causou a atualização da versão do conjunto de mais recente, não que a versão da atualização do conjunto de atualizações foi chamada em. 
    + Corrija o bug em Dataset.get_by_name que mostraria as marcas para a versão mais recente do conjunto de informações mesmo quando uma versão específica anterior foi recuperada.
  + **azureml-interpret**
    + Saídas de probabilidade adicionadas aos explicadores de Pontuação shap no azureml-interpretam com base no parâmetro shap_values_output do explicador original.
  + **azureml-pipeline-core**
    + Documentação aprimorada `PipelineOutputAbstractDataset.register` .
  + **azureml-train-automl-client**
    + Dependências de AutoML atualizadas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-train-automl-runtime**
    + Dependências de AutoML atualizadas: `scikit-learn` (agora 0.22.1), `pandas` (agora 0.25.1), `numpy` (agora 1.18.2).
  + **azureml-train-core**
    + Agora, os usuários devem fornecer um ARG hyperparameter_sampling válido ao criar um HyperDriveConfig. Além disso, a documentação do HyperDriveRunConfig foi editada para informar os usuários sobre a reprovação do HyperDriveRunConfig.
    + Revertendo a versão padrão do PyTorch para 1,4.
    + Adicionando PyTorch 1,6 & Tensorflow 2,2 imagens e ambiente organizado.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Experiência do Azure Machine Learning Studio notebooks (atualização de agosto)
+ **Novos recursos**
  + Nova página de aterrissagem introdução 
  
+ **Versão prévia dos recursos**
    + Reúna recursos em blocos de anotações. Com o recurso de [coleta](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) , os usuários agora podem facilmente limpar blocos de anotações com o, coletar usa uma análise de dependência automatizada do seu notebook, garantindo que o código essencial seja mantido, mas removendo quaisquer partes irrelevantes.

+ **Correções de bugs e melhorias**
  + Melhoria em velocidade e confiabilidade
  + Bugs de modo escuro corrigidos
  + Erros de rolagem de saída corrigidos
  + A pesquisa de exemplo agora pesquisa todo o conteúdo de todos os arquivos na Azure Machine Learning repositório de blocos de anotações de exemplo
  + As células do R de várias linhas agora podem ser executadas
  + "Eu confio no conteúdo deste arquivo" agora é automaticamente verificado após a primeira vez
  + Melhor caixa de diálogo resolução de conflitos, com a nova opção "fazer uma cópia"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK para Python v 1.12.0

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Adicione image_name e image_label parâmetros a Model. Package () para habilitar a renomeação da imagem do pacote compilado.
  + **azureml-automl-core**
    + O AutoML gera um novo código de erro do dataprep quando o conteúdo é modificado durante a leitura.
  + **azureml-automl-runtime**
    + Foram adicionados alertas para o usuário quando os dados contêm valores ausentes, mas personalização está desativado.
    + Correção de falhas de execução de filhos quando os dados contêm Nan e personalização são desativados.
    + O AutoML gera um novo código de erro do dataprep quando o conteúdo é modificado durante a leitura.
    + Normalização atualizada para que as métricas de previsão ocorram por granulação.
    + Cálculo aprimorado de previsão quantis quando os recursos do lookback estão desabilitados.
    + Corrigido o tratamento de matriz esparsa de bool ao calcular explicações após AutoML.
  + **azureml-core**
    + Um novo método `run.get_detailed_status()` agora mostra a explicação detalhada do status de execução atual. No momento, ele só mostra a explicação do `Queued` status.
    + Adicione image_name e image_label parâmetros a Model. Package () para habilitar a renomeação da imagem do pacote compilado.
    + Novo método `set_pip_requirements()` para definir a seção Pip inteira [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) ao mesmo tempo.
    + Habilitar o registro de repositório de armazenamento de ADLS Gen2 com menos credenciais.
    + Mensagem de erro aprimorada ao tentar baixar ou montar um tipo de conjunto de texto incorreto.
    + Atualize o bloco de anotações de exemplo de filtro de conjunto de teste de série temporal com mais exemplos de partition_timestamp que fornece otimização de filtro.
    + Altere o SDK e a CLI para aceitar SubscriptionId, resourcegroup, WorkspaceName, peConnectionName como parâmetros em vez de ArmResourceId ao excluir uma conexão de ponto de extremidade privada.
    + Decorador experimental mostra o nome da classe para facilitar a identificação.
    + As descrições dos ativos dentro de modelos não são mais geradas automaticamente com base em uma execução.
  + **azureml-datadrift**
    + Marque create_from_model API no DataDriftDetector como sendo preterida.
  + **azureml-dataprep**
    + Mensagem de erro aprimorada ao tentar baixar ou montar um tipo de conjunto de texto incorreto.
  + **azureml-pipeline-core**
    + Corrigido o bug ao desserializar o grafo de pipeline que contém conjuntos de tabelas registrados.
  + **azureml-pipeline-steps**
    + O RScriptStep dá suporte a RSection do azureml. Core. Environment.
    + Removeu o parâmetro passthru_automl_config da `AutoMLStep` API pública e o converteu em um parâmetro somente interno.
  + **azureml-train-automl-client**
    + O ambiente gerenciado assíncrono local removido é executado em AutoML. Todas as execuções locais serão executadas no ambiente em que a execução foi iniciada.
    + Correção de problemas de instantâneo ao enviar execuções do AutoML sem scripts fornecidos pelo usuário.
    + Correção de falhas de execução de filhos quando os dados contêm Nan e personalização são desativados.
  + **azureml-train-automl-runtime**
    + O AutoML gera um novo código de erro do dataprep quando o conteúdo é modificado durante a leitura.
    + Correção de problemas de instantâneo ao enviar execuções do AutoML sem scripts fornecidos pelo usuário.
    + Correção de falhas de execução de filhos quando os dados contêm Nan e personalização são desativados.
  + **azureml-train-core**
    + Suporte adicionado para especificar opções de PIP (por exemplo,--extra-index-URL) no arquivo de requisitos Pip passado para um [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator?preserve-view=true&view=azure-ml-py) parâmetro por meio de `pip_requirements_file` .


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK para Python v 1.11.0

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Corrigir estrutura de modelo e estrutura de modelo não passada no objeto de execução no caminho de registro do modelo da CLI
    + Corrigir o comando CLI amlcompute Identity show para mostrar a ID do locatário e a ID da entidade 
  + **azureml-train-automl-client**
    + Adição de get_best_child () a AutoMLRun para buscar a melhor execução de filho para uma execução de AutoML sem baixar o modelo associado.
    + Adicionado o objeto ModelProxy que permite que a previsão ou previsão seja executada em um ambiente de treinamento remoto sem baixar o modelo localmente.
    + As exceções sem tratamento no AutoML agora apontam para uma página HTTP de problemas conhecidos, onde mais informações sobre os erros podem ser encontradas.
  + **azureml-core**
    + Os nomes de modelo podem ter de 255 a caracteres de comprimento.
    + Environment.get_image_details () tipo de objeto de retorno alterado. `DockerImageDetails` classe substituída `dict` , os detalhes da imagem estão disponíveis nas propriedades da nova classe. As alterações são compatíveis com versões anteriores.
    + Corrigir o bug de Environment.from_pip_requirements () para preservar a estrutura de dependências
    + Corrigido um bug em que o log_list falharia se um int e duplo fossem incluídos na mesma lista.
    + Ao habilitar o link privado em um espaço de trabalho existente, observe que, se houver destinos de computação associados ao espaço de trabalho, esses destinos não funcionarão se não estiverem atrás da mesma rede virtual que o ponto de extremidade privado do espaço de trabalho.
    + Tornar `as_named_input` opcional ao usar conjuntos de valores em experimentos e adicionado `as_mount` e `as_download` ao `FileDataset` . O nome de entrada será gerado automaticamente se `as_mount` ou `as_download` for chamado.
  + **azureml-automl-core**
    + As exceções sem tratamento no AutoML agora apontam para uma página HTTP de problemas conhecidos, onde mais informações sobre os erros podem ser encontradas.
    + Adição de get_best_child () a AutoMLRun para buscar a melhor execução de filho para uma execução de AutoML sem baixar o modelo associado.
    + Adicionado o objeto ModelProxy que permite que previsão ou previsão seja executada em um ambiente de treinamento remoto sem baixar o modelo localmente.
  + **azureml-pipeline-steps**
    + Adicionados `enable_default_model_output` e `enable_default_metrics_output` sinalizadores a `AutoMLStep` . Esses sinalizadores podem ser usados para habilitar/desabilitar as saídas padrão.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK para Python v 1.10.0

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Ao usar AutoML, se um caminho for passado para o objeto AutoMLConfig e ele ainda não existir, ele será criado automaticamente.
    + Os usuários agora podem especificar uma frequência de série temporal para tarefas de previsão usando o `freq` parâmetro.
  + **azureml-automl-runtime**
    + Ao usar AutoML, se um caminho for passado para o objeto AutoMLConfig e ele ainda não existir, ele será criado automaticamente.
    + Os usuários agora podem especificar uma frequência de série temporal para tarefas de previsão usando o `freq` parâmetro.
    + A previsão do AutoML agora dá suporte à avaliação sem interrupção, que se aplica ao caso de uso de que o comprimento de um teste ou de validação seja maior que o horizonte de entrada, e o valor de y_pred conhecido é usado como contexto de previsão.
  + **azureml-core**
    + Mensagens de aviso serão impressas se nenhum arquivo tiver sido baixado do repositório de armazenamento em uma execução.
    + Adicionada a documentação do `skip_validation` ao `Datastore.register_azure_sql_database method` .
    + Os usuários precisam atualizar para o SDK v 1.10.0 ou superior para criar um ponto de extremidade particular aprovado automaticamente. Isso inclui o recurso de bloco de anotações que pode ser usado por trás da VNet.
    + Expor NotebookInfo na resposta de Get Workspace.
    + Alterações para ter chamadas para listar destinos de computação e obter o destino de computação com sucesso em uma execução remota. As funções do SDK para obter destino de computação e listar destinos de computação do espaço de trabalho agora funcionarão em execuções remotas.
    + Adicione mensagens de substituição às descrições de classe para as classes azureml. Core. Image.
    + Gerar exceção e limpar espaço de trabalho e recursos dependentes se a criação de ponto de extremidade particular do espaço de trabalho falhar.
    + Suporte para atualização de SKU do espaço de trabalho no método de atualização do espaço de
  + **azureml-datadrift**
    + Atualize a versão do matplotlib da 3.0.2 para 3.2.1 para dar suporte ao Python 3,8.
  + **azureml-dataprep**
    + Suporte adicionado de fontes de dados de URL da Web com a `Range` `Head` solicitação ou. 
    + Estabilidade aprimorada para montagem e download do conjunto de arquivos de arquivo.
  + **azureml-train-automl-client**
    + Correção de problemas relacionados à remoção de `RequirementParseError` de setuptools.
    + Use o Docker em vez de Conda para execuções locais enviadas usando "compute_target = ' local '"
    + A duração da iteração impressa no console foi corrigida. Anteriormente, a duração da iteração foi, às vezes, impressa como tempo de término da execução menos o tempo de criação de execução. Ele foi corrigido para a hora de término da execução igual menos a hora de início da execução.
    + Ao usar AutoML, se um caminho for passado para o objeto AutoMLConfig e ele ainda não existir, ele será criado automaticamente.
    + Os usuários agora podem especificar uma frequência de série temporal para tarefas de previsão usando o `freq` parâmetro.
  + **azureml-train-automl-runtime**
    + Saída de console aprimorada quando melhores explicações de modelo falham.
    + Parâmetro de entrada renomeado como "blocked_models" para remover um termo confidencial.
      + Parâmetro de entrada renomeado como "allowed_models" para remover um termo confidencial.
    + Os usuários agora podem especificar uma frequência de série temporal para tarefas de previsão usando o `freq` parâmetro.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK para Python v 1.9.0

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Substituído get_model_path () por AZUREML_MODEL_DIR variável de ambiente no script de Pontuação gerado automaticamente AutoML. Também foi adicionada telemetria para rastrear falhas durante init ().
    + A capacidade de especificar `enable_cache` como parte do AutoMLConfig foi removida
    + Correção de um bug em que execuções podem falhar com erros de serviço durante execuções específicas de previsão
    + Melhor manipulação de erros em relação a modelos específicos durante `get_output`
    + Chamada fixa para fitted_model. Fit (X, y) para classificação com o transformador y
    + Habilitado o preenchimento de encaminhamento personalizado imputer para tarefas de previsão
    + Uma nova classe ForecastingParameters será usada em vez de parâmetros de previsão em um formato de dicto
    + Detecção automática de retardo de destino aprimorada
    + Adição de disponibilidade limitada de vários nós personalização distribuídos de várias GPU com BERT
  + **azureml-automl-runtime**
    + Prophet agora faz a modelagem sazonalidade aditiva em vez de multiplicativa.
    + Corrigido o problema quando os pequenos detalhamentos, a existência de frequências diferentes das granularidades longas resultará em execuções com falha.
  + **azureml-contrib-automl-DNN-Vision**
    + Coletar estatísticas de sistema/GPU e médias de log para treinamento e Pontuação
  + **azureml-contrib-Mir**
    + Suporte adicionado para o sinalizador Enable-app-insights no ManagedInferencing
  + **azureml-core**
    + Um parâmetro Validate para essas APIs, permitindo que a validação seja ignorada quando a fonte de dados não estiver acessível da computação atual.
      + TabularDataset.time_before (end_time, include_boundary = true, Validate = true)
      + TabularDataset.time_after (start_time, include_boundary = true, Validate = true)
      + TabularDataset.time_recent (time_delta, include_boundary = true, Validate = true)
      + TabularDataset.time_between (start_time, end_time, include_boundary = true, Validate = true)
    + Adicionado suporte à filtragem de estrutura para a lista de modelos e adicionado o exemplo NCD AutoML no notebook de volta
    + Para Datastore.register_azure_blob_container e Datastore.register_azure_file_share (somente opções que dão suporte ao token SAS), atualizamos as cadeias de caracteres de documento para o `sas_token` campo para incluir requisitos mínimos de permissões para cenários de leitura e gravação típicos.
    + Preterindo _with_auth param no ws.get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Adicionar suporte para a implantação de modelos de file://locais com AzureML-MLflow
    + Preterindo _with_auth param no ws.get_mlflow_tracking_uri ()
  + **azureml-opendatasets**
    + Os conjuntos de Covid de controle de acompanhamento recentemente publicados agora estão disponíveis com o SDK
  + **azureml-pipeline-core**
    + Aviso de logoff quando "azureml-defaults" não está incluído como parte da dependência de Pip
    + Melhorar a renderização de nota.
    + Suporte adicionado para quebras de linha entre aspas ao analisar arquivos delimitados para PipelineOutputFileDataset.
    + A classe PipelineDataset foi preterida. Para obter mais informações, consulte https://aka.ms/dataset-deprecation. Saiba como usar o conjunto de informações com o pipeline, consulte https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Atualizações do documento para o azureml-pipeline-Steps.
    +  Suporte adicionado no ParallelRunConfig `load_yaml()` para que os usuários definam ambientes embutidos com o restante da configuração ou em um arquivo separado
  + **azureml-Train-automl-Client**.
    + A capacidade de especificar `enable_cache` como parte do AutoMLConfig foi removida
  + **azureml-train-automl-runtime**
    + Adição de disponibilidade limitada de vários nós personalização distribuídos de várias GPU com BERT.
    + O tratamento de erros foi adicionado para pacotes incompatíveis em execuções de Machine Learning automatizadas baseadas em ADB.
  + **azureml-widgets**
    + Atualizações do documento para o azureml-widgets.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK para Python v 1.8.0
  
  + **Versão prévia dos recursos**
    + **azureml-contrib-imparcial** O `azureml-contrib-fairness` pacote fornece integração entre a avaliação de integridade de software livre e o pacote de mitigação de infração [Fairlearn](https://fairlearn.github.io) e o Azure Machine Learning Studio. Em particular, o pacote permite que os painéis de avaliação de integridade do modelo sejam carregados como parte de uma execução do AzureML e apareçam no Azure Machine Learning Studio

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Suporte ao obter logs do contêiner de inicialização.
    + Adicionados novos comandos da CLI para gerenciar o ComputeInstance
  + **azureml-automl-core**
    + Agora, os usuários podem habilitar a iteração de Ensemble de pilha para tarefas de série temporal com um aviso de que ele poderia sobreajustar potencialmente.
    + Adicionado um novo tipo de exceção de usuário que é gerado se o conteúdo do armazenamento de cache tiver sido adulterado
  + **azureml-automl-runtime**
    + A limpeza de balanceamento de classe não será mais habilitada se o usuário desabilitar personalização.  
  + **azureml-contrib-ITP**
    + Há suporte para o tipo de computação CmAks. Você pode anexar seu próprio cluster AKS ao espaço de trabalho para o trabalho de treinamento.
  + **azureml-contrib-notebook**
    + Melhorias de documentos no pacote azureml-contrib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Aprimoramentos de documentos para o azureml-contrib--pacote de etapas de pipeline.
  + **azureml-core**
    + Adicionar set_connection, get_connection, list_connections delete_connection funções do cliente para operar no recurso de conexão do espaço de trabalho
    + Atualizações de documentação para o pacote azureml-coore/azureml. Exceptions.
    + Atualizações de documentação para o pacote azureml-Core.
    + Atualizações do documento para a classe ComputeInstance.
    + Melhorias de documentos no pacote azureml-Core/azureml. Core. COMPUTE.
    + Aprimoramentos do doc para classes relacionadas ao WebService no azureml-Core.
    + Dar suporte ao armazenamento de dados selecionado pelo usuário para armazenar o perfil
    + Adicionada a propriedade Expand e page_count para a API de lista de modelos
    + Corrigido o bug em que a remoção da propriedade de substituição fará com que a execução enviada falhe com o erro de desserialização.
    + Correção da estrutura de pastas inconsistentes ao baixar ou montar um filedataset referenciando um único arquivo.
    + Carregar um conjunto de to_spark_dataframe de arquivos parquet agora é mais rápido e dá suporte a todos os tipos de dataparquet e Spark SQL.
    + Suporte ao obter logs do contêiner de inicialização.
    + As execuções de AutoML agora estão marcadas como execução filho da etapa de execução paralela.
  + **azureml-datadrift**
    + Melhorias de documentos no pacote azureml-contrib-notebook.
  + **azureml-dataprep**
    + Carregar um conjunto de to_spark_dataframe de arquivos parquet agora é mais rápido e dá suporte a todos os tipos de dataparquet e Spark SQL.
    + Melhor tratamento de memória para o problema de OutOfMemory para to_pandas_dataframe.
  + **azureml-interpret**
    + Atualização do azureml-interprete para usar a interpretação-Community versão 0,12. *
  + **azureml-mlflow**
    + Aprimoramentos do doc para o azureml-mlflow.
    + Adiciona suporte para o registro de modelo AML com MLFlow.
  + **azureml-opendatasets**
    + Suporte adicionado para Python 3.8
  + **azureml-pipeline-core**
    + `PipelineDataset`A documentação da atualização para torná-la clara é uma classe interna.
    + ParallelRunStep atualizações para aceitar vários valores para um argumento, por exemplo: "--group_column_names", "Col1", "Col2", "Col3"
    + Removida a passthru_automl_config requisito para uso de dados intermediários com AutoMLStep em pipelines.
  + **azureml-pipeline-steps**
    + Melhorias de documentos no pacote azureml-pipeline-Steps.
    + Removida a passthru_automl_config requisito para uso de dados intermediários com AutoMLStep em pipelines.
  + **azureml-telemetry**
    + Aprimoramentos do doc para o azureml – telemetria.
  + **azureml-train-automl-client**
    + Corrigido um bug onde `experiment.submit()` chamado duas vezes em um `AutoMLConfig` objeto resultou em um comportamento diferente.
    + Agora, os usuários podem habilitar a iteração de Ensemble de pilha para tarefas de série temporal com um aviso de que ele poderia sobreajustar potencialmente.
    + O comportamento de execução do AutoML foi alterado para gerar usererroexception se o serviço lançar o erro do usuário
    + Corrige um bug que fez com que azureml_automl. log não seja gerado ou não tenha logs ao executar um experimento AutoML em um destino de computação remota.
    + Para conjuntos de dados de classificação com classes desbalanceadas, aplicaremos o balanceamento de peso, se a varredura de recurso determinar que, para dados de subamostrados, o balanceamento de peso melhorará o desempenho da tarefa de classificação por um determinado limite.
    + As execuções de AutoML agora estão marcadas como execução filho da etapa de execução paralela.
  + **azureml-train-automl-runtime**
    + O comportamento de execução do AutoML foi alterado para gerar usererroexception se o serviço lançar o erro do usuário
    + As execuções de AutoML agora estão marcadas como execução filho da etapa de execução paralela.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK para Python v 1.7.0

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Concluída a remoção da criação de perfil de modelo de Mir contrib limpando os comandos da CLI e as dependências do pacote, a criação de perfil do modelo está disponível no núcleo.
    + Atualiza a versão mínima do CLI do Azure para 2.3.0
  + **azureml-automl-core**
    + Melhor mensagem de exceção na etapa personalização fit_transform () devido a parâmetros de transformador personalizados.
    + Adicione suporte para vários idiomas para modelos de transformadores de aprendizado profundo, como BERT em ML automatizado.
    + Remova o parâmetro de lag_length preterido da documentação.
    + A documentação dos parâmetros de previsão foi aprimorada. O parâmetro lag_length foi preterido.
  + **azureml-automl-runtime**
    + Corrigido o erro gerado quando uma das colunas categóricas está vazia no tempo de previsão/teste.
    + Corrija as falhas de execução que ocorrem quando os recursos de lookback estão habilitados e os dados contêm pequenas granularidades.
    + Corrigido o problema com a mensagem de erro de índice de tempo duplicado quando as janelas atrasadas ou revertidas foram definidas como ' auto '.
    + Corrigido o problema com os modelos Prophet e ARIMA em conjuntos de dados, contendo os recursos do lookback.
    + Adicionado suporte de datas antes de 1677-09-21 ou depois de 2262-04-11 em colunas, em vez de data e hora nas tarefas de previsão. Mensagens de erro aprimoradas.
    + A documentação dos parâmetros de previsão foi aprimorada. O parâmetro lag_length foi preterido.
    + Melhor mensagem de exceção na etapa personalização fit_transform () devido a parâmetros de transformador personalizados.
    + Adicione suporte para vários idiomas para modelos de transformadores de aprendizado profundo, como BERT em ML automatizado.
    + As operações de cache que resultam em algumas OSErrors gerarão erro do usuário.
    + Verificações adicionadas para garantir que os dados de treinamento e validação tenham o mesmo número e conjunto de colunas
    + Corrigido o problema com o script de Pontuação AutoML gerado automaticamente quando os dados contêm aspas
    + Habilitando explicações para modelos AutoML Prophet e ensembled que contêm o modelo Prophet.
    + Um problema recente do cliente revelou um bug de site ativo no qual registramos as mensagens ao longo do balanceamento de classe-varredura, mesmo quando a lógica de balanceamento de classe não está habilitada corretamente. Removendo os logs/mensagens desta PR.
  + **azureml-CLI-comum**
    + Concluída a remoção da criação de perfil de modelo de Mir contrib limpando os comandos da CLI e as dependências do pacote, a criação de perfil do modelo está disponível no núcleo.
  + **azureml-contrib-reinforcementlearning**
    + Ferramenta de teste de carga
  + **azureml-core**
    + Alterações de documentação em Script_run_config. py
    + Corrige um bug com a impressão da saída da CLI executar envio-pipeline
    + Aprimoramentos de documentação para azureml-Core/azureml. Data
    + Correção do problema ao recuperar a conta de armazenamento usando o comando HDFS getconf
    + Documentação de register_azure_blob_container e register_azure_file_share aprimorada
  + **azureml-datadrift**
    + Implementação aprimorada para desabilitar e habilitar monitores de descompasso de conjuntos de conjunto
  + **azureml-interpret**
    + Em explicação cliente, remova NaNs ou INFs antes da serialização JSON ao carregar de artefatos
    + Atualize para a versão mais recente da interpretação da Comunidade para melhorar erros de memória para explicações globais com muitos recursos e classes
    + Adicionar true_ys parâmetro opcional ao upload de explicação para habilitar recursos adicionais na interface do usuário do estúdio
    + Melhorar o desempenho de download_model_explanations () e list_model_explanations ()
    + Pequenos ajustes em blocos de anotações para auxiliar na depuração
  + **azureml-opendatasets**
    + o azureml-opendatasets precisa do azureml-dataprep versão 1.4.0 ou superior. Aviso adicionado se uma versão inferior for detectada
  + **azureml-pipeline-core**
    + Essa alteração permite que o usuário forneça um runconfig opcional para o moduleVersion ao chamar o módulo. Publish_python_script.
    + Habilitar a conta de nó pode ser um parâmetro de pipeline em ParallelRunStep no azureml. Pipeline. Steps
  + **azureml-pipeline-steps**
    + Essa alteração permite que o usuário forneça um runconfig opcional para o moduleVersion ao chamar o módulo. Publish_python_script.
  + **azureml-train-automl-client**
    + Adicione suporte para vários idiomas para modelos de transformadores de aprendizado profundo, como BERT em ML automatizado.
    + Remova o parâmetro de lag_length preterido da documentação.
    + A documentação dos parâmetros de previsão foi aprimorada. O parâmetro lag_length foi preterido.
  + **azureml-train-automl-runtime**
    + Habilitando explicações para modelos AutoML Prophet e ensembled que contêm o modelo Prophet.
    + Atualizações de documentação para os pacotes azureml-Train-automl-*.
  + **azureml-train-core**
    + Dando suporte ao TensorFlow versão 2,1 no estimador de PyTorch
    + Melhorias no pacote azureml-Train-Core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK para Python v 1.6.0

+ **Novos recursos**
  + **azureml-automl-runtime**
    + A previsão de AutoML agora dá suporte a previsões de clientes além do máximo de horizonte predefinido sem treinar novamente o modelo. Quando o destino da previsão estiver mais distante no futuro do que o horizonte máximo especificado, a função Forecast () ainda fará previsões de ponto para a data posterior usando um modo de operação recursivo. Para obter a ilustração do novo recurso, consulte a seção "previsão mais distante do que o horizonte máximo" do bloco de anotações "previsão-previsão-função" na [pasta](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)".
  
  + **azureml-pipeline-steps**
    + O ParallelRunStep agora é liberado e faz parte do pacote **azureml-pipeline-Steps** . Os ParallelRunStep existentes no pacote **azureml-contrib-pipeline-Steps** foram preteridos. Alterações da versão de visualização pública:
      + `run_max_try`Parâmetro opcional configurável adicionado para controlar a chamada máxima para o método Run para qualquer lote especificado, o valor padrão é 3.
      + Nenhum Pipelineparameters é mais gerado automaticamente. Os seguintes valores configuráveis podem ser definidos como PipelineParameter explicitamente.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + O valor padrão para process_count_per_node é alterado para 1. O usuário deve ajustar esse valor para melhorar o desempenho. A prática recomendada é definir como o número de GPU ou o nó de CPU tem.
      + O ParallelRunStep não injeta nenhum pacote, o usuário precisa incluir pacotes **azureml-Core** e **azureml-dataprep [pandas, fusível]** na definição de ambiente. Se a imagem personalizada do Docker for usada com user_managed_dependencies, o usuário precisará instalar o Conda na imagem.
      
+ **Alterações interruptivas**
  + **azureml-pipeline-steps**
    + Preterido o uso de azureml. dprep. Dataflow como um tipo válido de entrada para AutoMLConfig
  + **azureml-train-automl-client**
    + Preterido o uso de azureml. dprep. Dataflow como um tipo válido de entrada para AutoMLConfig

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Corrigido o bug em que um aviso pode ser impresso durante o `get_output` usuário solicitado a fazer downgrade do cliente.
    + Mac atualizado para contar com cudatoolkit = 9.0, pois ele não está disponível na versão 10 ainda.
    + Remoção de restrições em modelos Prophet e xgboost quando treinado em computação remota.
    + Log aprimorado no AutoML
    + O tratamento de erros para personalização personalizado em tarefas de previsão foi aprimorado.
    + Funcionalidade adicionada para permitir que os usuários incluam recursos esdefasados para gerar previsões.
    + Atualizações da mensagem de erro para exibir corretamente o erro do usuário.
    + Suporte para cv_split_column_names ser usado com training_data
    + Atualize o log da mensagem de exceção e controle.
  + **azureml-automl-runtime**
    + Habilite guardrails para prever o valor ausente imputations.
    + Log aprimorado no AutoML
    + Adicionado tratamento de erro refinado para exceções de preparação de dados
    + Remoção de restrições em modelos phrophet e xgboost quando treinado em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` ter dependências atualizadas para `pytorch` , `scipy` e `cudatoolkit` . Agora, damos suporte a `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` e `cudatoolkit==10.1.243` .
    + O tratamento de erros para personalização personalizado em tarefas de previsão foi aprimorado.
    + O mecanismo de detecção de frequência do conjunto de dados de previsão foi aprimorado.
    + Correção do problema com o treinamento do modelo Prophet em alguns conjuntos de dados.
    + A detecção automática de horizonte máximo durante a previsão foi aprimorada.
    + Funcionalidade adicionada para permitir que os usuários incluam recursos esdefasados para gerar previsões.
    +  Adiciona a funcionalidade na função de previsão para habilitar o fornecimento de previsões além do horizonte treinado sem treinar novamente o modelo de previsão.
    + Suporte para cv_split_column_names ser usado com training_data
  + **azureml-contrib-automl-DNN-previsão**
    + Log aprimorado no AutoML
  + **azureml-contrib-Mir**
    + Suporte adicionado para serviços do Windows no ManagedInferencing
    + Remova os fluxos de trabalho MIR antigos, como anexar MIR Compute, SingleModelMirWebservice classe-limpar criação de perfil de modelo colocado no pacote contrib-Mir
  + **azureml-contrib-pipeline-steps**
    + Correção secundária para o suporte do YAML
    + O ParallelRunStep é liberado para disponibilidade geral-o azureml. contrib. Pipeline. Steps tem um aviso de reprovação e é movido para o azureml. Pipeline. Steps
  + **azureml-contrib-reinforcementlearning**
    + Ferramenta de teste de carga de RL
    + O estimador RL tem padrões inteligentes
  + **azureml-core**
    + Remova os fluxos de trabalho MIR antigos, como anexar MIR Compute, SingleModelMirWebservice classe-limpar criação de perfil de modelo colocado no pacote contrib-Mir
    + Foram corrigidas as informações fornecidas ao usuário em caso de falha de criação de perfil: ID de solicitação incluída e retexto da mensagem para que seja mais significativo. Adição de novo fluxo de trabalho de criação de perfil para criação de perfil de executores
    + Texto de erro aprimorado no caso de falhas de execução de conjunto de entrada.
    + Suporte adicionado à CLI do link privado do espaço de trabalho.
    + Um parâmetro opcional foi adicionado `invalid_lines` ao `Dataset.Tabular.from_json_lines_files` que permite especificar como lidar com linhas que contêm JSON inválido.
    + Iremos substituir a criação de computação baseada em execução na próxima versão. É recomendável criar um cluster Amlcompute real como um destino de computação persistente e usar o nome do cluster como o destino de computação em sua configuração de execução. Consulte o exemplo de bloco de anotações aqui: aka.ms/amlcomputenb
    + Mensagens de erro aprimoradas em caso de falhas de execução do conjunto de de.
  + **azureml-dataprep**
    + Foi feito um aviso para atualizar a versão pyarrow mais explícita.
    + Tratamento de erro e mensagem aprimorados retornados em caso de falha ao executar Dataflow.
  + **azureml-interpret**
    + Atualizações de documentação para o pacote azureml-interpret.
    + Correção de pacotes de interpretação e notebooks para serem compatíveis com a atualização mais recente do sklearn
  + **azureml-opendatasets**
    + retorne nenhum quando nenhum dado for retornado.
    + Melhorar o desempenho de to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Correção rápida para ParallelRunStep em que o carregamento de YAML foi rompido
    + O ParallelRunStep é liberado para disponibilidade geral-o azureml. contrib. Pipeline. Steps tem um aviso de reprovação e é movido para o azureml. Pipeline. Steps-os novos recursos incluem: 1. Conjuntos de valores como PipelineParameter 2. Novo parâmetro run_max_retry 3. Nome de arquivo de saída append_row configurável
  + **azureml-pipeline-steps**
    + O azureml. dprep. Dataflow foi preterido como um tipo válido para dados de entrada.
    + Correção rápida para ParallelRunStep em que o carregamento de YAML foi rompido
    + O ParallelRunStep é liberado para disponibilidade geral-o azureml. contrib. Pipeline. Steps tem um aviso de reprovação e é movido para o azureml. Pipeline. Steps-os novos recursos incluem:
      + Conjuntos de valores como PipelineParameter
      + Novo run_max_retry de parâmetros
      + Nome de arquivo de saída append_row configurável
  + **azureml-telemetry**
    + Atualize o log da mensagem de exceção e controle.
  + **azureml-train-automl-client**
    + Log aprimorado no AutoML
    + Atualizações da mensagem de erro para exibir corretamente o erro do usuário.
    + Suporte para cv_split_column_names ser usado com training_data
    + O azureml. dprep. Dataflow foi preterido como um tipo válido para dados de entrada.
    + Mac atualizado para contar com cudatoolkit = 9.0, pois ele não está disponível na versão 10 ainda.
    + Remoção de restrições em modelos phrophet e xgboost quando treinado em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` ter dependências atualizadas para `pytorch` , `scipy` e `cudatoolkit` . Agora, damos suporte a `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` e `cudatoolkit==10.1.243` .
    + Funcionalidade adicionada para permitir que os usuários incluam recursos esdefasados para gerar previsões.
  + **azureml-train-automl-runtime**
    + Log aprimorado no AutoML
    + Adicionado tratamento de erro refinado para exceções de preparação de dados
    + Remoção de restrições em modelos phrophet e xgboost quando treinado em computação remota.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` ter dependências atualizadas para `pytorch` , `scipy` e `cudatoolkit` . Agora, damos suporte a `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` e `cudatoolkit==10.1.243` .
    + Atualizações da mensagem de erro para exibir corretamente o erro do usuário.
    + Suporte para cv_split_column_names ser usado com training_data
  + **azureml-train-core**
    + Adicionado um novo conjunto de exceções específicas do HyperDrive. o azureml. Train. hyperdrive agora vai lançar exceções detalhadas.
  + **azureml-widgets**
    + Os widgets do AzureML não são exibidos no JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>SDK do Azure Machine Learning para Python v 1.5.0

+ **Novos recursos**
  + **Versão prévia dos recursos**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning está liberando o suporte de visualização para aprendizado de reforço usando o [Ray](https://ray.io) Framework. O `ReinforcementLearningEstimator` habilita o treinamento de agentes do reforço Learning em destinos de computação de CPU e de GPU em Azure Machine Learning.

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Corrige um log de aviso acidentalmente deixado para trás na minha PR anterior. O log foi usado para depuração e, acidentalmente, foi deixado para trás.
    + Correção de bug: informar aos clientes sobre falha parcial durante a criação de perfil
  + **azureml-automl-core**
    + Acelere o modelo Prophet/AutoArima na previsão de AutoML habilitando a ajuste paralelo para a série temporal quando os conjuntos de dados tiverem várias séries temporais. Para se beneficiar desse novo recurso, é recomendável definir "max_cores_per_iteration =-1" (ou seja, usando todos os núcleos de CPU disponíveis) em AutoMLConfig.
    + Corrigir keyerro ao imprimir guardrails na interface do console
    + Mensagem de erro fixa para experimentation_timeout_hours
    + Modelos Tensorflow preteridos para AutoML.
  + **azureml-automl-runtime**
    + Mensagem de erro fixa para experimentation_timeout_hours
    + Correção de exceção não classificada ao tentar desserializar do armazenamento de cache
    + Acelere o modelo Prophet/AutoArima na previsão de AutoML habilitando a ajuste paralelo para a série temporal quando os conjuntos de dados tiverem várias séries temporais.
    + Correção da previsão com a janela sem interrupção habilitada nos conjuntos de dados em que o conjunto de teste/previsão não contém uma das granulações do conjunto de treinamento.
    + Tratamento aprimorado de dados ausentes
    + Corrigido o problema com intervalos de previsão durante a previsão de conjuntos de dados, contendo séries temporais, que não estão alinhadas no tempo.
    + Adicionada melhor validação da forma de dados para as tarefas de previsão.
    + Melhoria da detecção de frequência.
    + Foi criada uma mensagem de erro melhor se as dobras de validação cruzada para tarefas de previsão não puderem ser geradas.
    + Corrija a interface do console para imprimir o valor ausente Guardrail corretamente.
    + Impor verificações de DataType em cv_split_indices entrada em AutoMLConfig.
  + **azureml-CLI-comum**
    + Correção de bug: informar aos clientes sobre falha parcial durante a criação de perfil
  + **azureml-contrib-Mir**
    + Adiciona uma classe azureml. contrib. Mir. RevisionStatus que retransmite informações sobre a revisão do MIR implantada no momento e a versão mais recente especificada pelo usuário. Essa classe está incluída no objeto MirWebservice no atributo ' deployment_status '.
    + Habilita a atualização em WebServices do tipo MirWebservice e sua classe filho SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Adicionado suporte para Ray 0.8.3
    + O AmlWindowsCompute só dá suporte a arquivos do Azure como armazenamento montado
    + Health_check_timeout renomeado para health_check_timeout_seconds
    + Correção de algumas descrições de classe/método.
  + **azureml-core**
    + As conversões de blob > WASB habilitadas no Azure governamental e nas nuvens da China.
    + Corrige o bug para permitir que as funções de leitor usem os comandos AZ ml Run CLI para obter informações de execução
    + Log desnecessário removido durante execuções remotas do Azure ML com conjuntos de dados de entrada.
    + RCranPackage agora dá suporte ao parâmetro "Version" para a versão do pacote CRAN.
    + Correção de bug: informar aos clientes sobre falha parcial durante a criação de perfil
    + Adicionada manipulação de float de estilo europeu para o azureml-Core.
    + Recursos de link privado do espaço de trabalho habilitado no SDK do Azure ml.
    + Ao criar um TabularDataset usando `from_delimited_files` , você pode especificar se os valores vazios devem ser carregados como None ou como uma cadeia de caracteres vazia, definindo o argumento booliano `empty_as_string` .
    + Adicionada manipulação de float de estilo europeu para conjuntos de valores.
    + Mensagens de erro aprimoradas em falhas de montagem de conjunto de de.
  + **azureml-datadrift**
    + A consulta de resultados de descompasso de dados do SDK teve um bug que não diferenciava as métricas de recurso mínima, máxima e média, resultando em valores duplicados. Corrigimos esse bug ao prefixar o destino ou a linha de base para os nomes de métrica. Antes: mín. máx. de duplicação, média. Após: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Melhore o tratamento de ambientes Python restritos de gravação ao garantir as dependências do .NET necessárias para a entrega de dados.
    + Correção da criação de fluxo de arquivos no arquivo com registros vazios à esquerda.
    + Adicionadas opções de tratamento de erros para `to_partition_iterator` semelhantes a `to_pandas_dataframe` .
  + **azureml-interpret**
    + Redução dos limites de tamanho de caminho de explicação para reduzir a probabilidade de passar pelo limite do Windows
    + Bugfix explicações esparsas criadas com o explicador de imitação usando um modelo substituto linear.
  + **azureml-opendatasets**
    + Corrigir o problema das colunas de MNIST é analisado como cadeia de caracteres, que deve ser int.
  + **azureml-pipeline-core**
    + Permitir a opção de regenerate_outputs ao usar um módulo inserido em um ModuleStep.
  + **azureml-train-automl-client**
    + Modelos Tensorflow preteridos para AutoML.
    + Corrigir usuários permitir a listagem de algoritmos sem suporte no modo local
    + O Doc corrige o AutoMLConfig.
    + Impor verificações de DataType em cv_split_indices entrada em AutoMLConfig.
    + Problema corrigido com AutoML execuções com falha em show_output
  + **azureml-train-automl-runtime**
    + Corrigindo um bug em iterações Ensemble que impedia o tempo limite de download do modelo de iniciar com êxito.
  + **azureml-train-core**
    + Corrija a digitação na classe azureml. Train. DNN. Nccl.
    + Dando suporte ao PyTorch versão 1,5 no estimador de PyTorch
    + Corrigir o problema que a imagem de estrutura não pode ser buscada na região do Azure governamental ao usar estimadores de estrutura de treinamento

  
## <a name="2020-05-04"></a>2020-05-04
**Nova experiência de notebook**

Agora você pode criar, editar e compartilhar arquivos e blocos de anotações do Machine Learning diretamente dentro da experiência da Web do estúdio do Azure Machine Learning. Você pode usar todas as classes e os métodos disponíveis em [Azure Machine Learning SDK do Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) de dentro desses notebooks comece [aqui](./how-to-run-jupyter-notebooks.md)

**Novos recursos introduzidos:**

+ Editor aprimorado (editor Mônaco) usado pelo VS Code 
+ Aprimoramentos da interface do usuário/UX
+ Barra de ferramentas da célula
+ Nova barra de ferramentas e controles de computação do notebook
+ Barra de status do bloco de anotações 
+ Alternância de kernel embutido
+ Suporte a R
+ Aprimoramentos de acessibilidade e localização
+ Paleta de Comandos
+ Atalhos de teclado adicionais
+ Salvamento automático
+ Desempenho e confiabilidade aprimorados

Acesse as seguintes ferramentas de criação baseadas na Web no estúdio:
    
| Ferramenta baseada na Web  |     Descrição  |
|---|---|
| Blocos de anotações do Azure ML Studio   |     Primeira criação na classe para arquivos de bloco de anotações e suporte a todas as operações disponíveis no SDK do Python do Azure ML. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK para Python v 1.4.0

+ **Novos recursos**
  + Os clusters AmlCompute agora dão suporte à configuração de uma identidade gerenciada no cluster no momento do provisionamento. Basta especificar se você deseja usar uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário e passar uma identityid para a última. Em seguida, você pode configurar permissões para acessar vários recursos, como armazenamento ou ACR, de uma forma que a identidade da computação seja usada para acessar os dados com segurança, em vez de uma abordagem baseada em token que o AmlCompute emprega hoje. Confira nossa referência de SDK para obter mais informações sobre os parâmetros.
  

+ **Alterações interruptivas**
  + Os clusters AmlCompute suportavam um recurso de visualização em relação à criação baseada em execução, que estamos planejando para substituir em duas semanas. Você pode continuar criando destinos de computação persistentes como sempre usando a classe Amlcompute, mas a abordagem específica de especificar o identificador "Amlcompute" como o destino de computação na configuração de execução não terá suporte em um futuro próximo. 

+ **Correções de bugs e melhorias**
  + **azureml-automl-runtime**
    + Habilite o suporte para tipo sem hash ao calcular o número de valores exclusivos em uma coluna.
  + **azureml-core**
    + Estabilidade aprimorada ao ler do armazenamento de BLOBs do Azure usando um TabularDataset.
    + Documentação aprimorada para o `grant_workspace_msi` parâmetro para `Datastore.register_azure_blob_store` .
    + Corrigido o bug com `datastore.upload` para dar suporte ao `src_dir` argumento que termina com um `/` ou `\` .
    + Mensagem de erro acionável adicionada ao tentar carregar em um armazenamento de blob do Azure que não tem uma chave de acesso ou token SAS.
  + **azureml-interpret**
    + Adição do limite superior ao tamanho do arquivo para os dados de visualização nas explicações carregadas.
  + **azureml-train-automl-client**
    + Verificando explicitamente label_column_name & parâmetros weight_column_name para que AutoMLConfig seja do tipo cadeia de caracteres.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep agora dá suporte a DataSet como parâmetro de pipeline. O usuário pode construir o pipeline com o conjunto de dados de exemplo e pode alterar o conjunto de dados de entrada do mesmo tipo (arquivo ou tabular) para a nova execução do pipeline.

  
## <a name="2020-04-13"></a>13/04/2020

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK para Python v 1.3.0

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Adição de telemetria adicional em operações de pós-treinamento.
    + Acelera o treinamento de ARIMA automático usando o treinamento de soma de quadrados (CSS) condicional para uma série de comprimento maior que 100. O comprimento usado é armazenado como a constante ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/na classe TimeSeriesInternal em/src/azureml-automl-Core/azureml/automl/Core/Shared/Constants.py
    + O registro em log de execuções de previsão foi aprimorado. agora, mais informações sobre qual fase está em execução no momento serão mostradas no log
    + Target_rolling_window_size não permitido ser definido como valores menores que 2
  + **azureml-automl-runtime**
    + Mensagem de erro mostrada quando são encontrados carimbos de data/hora duplicados.
    + Target_rolling_window_size não permitido ser definido como valores menores que 2.
    + Correção da falha de imputação de latência. O problema foi causado pelo número insuficiente de observações necessárias para decompor uma série em um época. Os dados "de-sazonal" são usados para calcular uma função parcial de correlação (PACF) para determinar o comprimento de latência.
    + Habilitada a personalização de personalização de finalidade de coluna para prever tarefas por personalização config. Agora há suporte para numérico e categórico como finalidade de coluna para tarefas de previsão.
    + Habilite a personalização de personalização de coluna para previsão de tarefas por personalização config.
    + Habilitada a personalização de imputação para tarefas de previsão pela configuração do personalização. O valor constante imputação para a coluna de destino e a média, mediana, most_frequent e o valor constante imputação para dados de treinamento agora têm suporte.
  + **azureml-contrib-pipeline-steps**
    + Aceite os nomes de computação da cadeia de caracteres a serem passados para ParallelRunConfig
  + **azureml-core**
    +  API de Environment. Clone (new_name) adicionada para criar uma cópia do objeto de ambiente
    +  Environment.docker.base_dockerfile aceita FilePath. Se for capaz de resolver um arquivo, o conteúdo será lido na propriedade de ambiente base_dockerfile
    + Redefine automaticamente valores mutuamente exclusivos para base_image e base_dockerfile quando o usuário define manualmente um valor em Environment.docKer
    + Foi adicionado user_managed sinalizador em RSection que indica se o ambiente é gerenciado pelo usuário ou pelo AzureML.
    + DataSet: falha no download do conjunto de dados fixo se o caminho de dado que contém caracteres Unicode.
    + Conjunto de valores: mecanismo de cache de montagem de conjunto de valores aprimorado para respeitar o requisito mínimo de espaço em disco no Azure Machine Learning computação, o que evita tornar o nó inutilizável e fazer com que o trabalho seja cancelado.
    + Conjunto de dados: adicionamos um índice para a coluna de série temporal quando você acessa um conjunto de dados de série temporal como um data frame do pandas, que é usado para acelerar o acesso ao acesso de dado baseado em séries temporais.  Anteriormente, o índice recebeu o mesmo nome que a coluna timestamp, os usuários confusos sobre qual é a coluna de carimbo de data/hora real e qual é o índice. Agora, não damos nenhum nome específico ao índice, pois ele não deve ser usado como uma coluna. 
    + DataSet: problema de autenticação de conjunto de banco de uma correção no soberanas Cloud.
    + Conjunto de `Dataset.to_spark_dataframe` valores: falha fixa para conjuntos de itens criados a partir de repositórios de armazenamento do PostgreSQL do Azure.
  + **azureml-interpret**
    + Foram adicionadas pontuações globais à visualização se os valores de importância local forem esparsos
    + Atualizado o azureml-interpretar para usar a interpretação-Community 0,9. *
    + Correção do problema com a explicação de download que tinha dados de avaliação esparsas
    + Adicionado suporte do formato esparso do objeto de explicação em AutoML
  + **azureml-pipeline-core**
    + Suporte a ComputeInstance como destino de computação em pipelines
  + **azureml-train-automl-client**
    + Adição de telemetria adicional em operações de pós-treinamento.
    + Correção da regressão na parada inicial
    + O azureml. dprep. Dataflow foi preterido como um tipo válido para dados de entrada.
    +  A alteração do tempo de teste do AutoML padrão é de seis dias.
  + **azureml-train-automl-runtime**
    + Adição de telemetria adicional em operações de pós-treinamento.
    + adição de AutoML esparsa de suporte de ponta a ponta
  + **azureml-opendatasets**
    + Telemetria adicional adicionada para o monitor de serviço.
    + Habilitar a porta frontal para o blob aumentar a estabilidade 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK para Python v 1.2.0

+ **Alterações interruptivas**
  + Descartar suporte para Python 2,7

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Adiciona "--Subscription-ID" aos `az ml model/computetarget/service` comandos na CLI
    + Adição de suporte para passar a CMK (chave gerenciada pelo cliente) vault_url, key_name e key_version para implantação do ACI
  + **azureml-automl-core** 
    + Habilitada a imputação personalizada com valor constante para tarefas de previsão de dados X e y.
    + Correção do problema no com a exibição de mensagens de erro para o usuário.    
  + **azureml-automl-runtime**
    + Correção do problema no com a previsão nos conjuntos de dados, contendo refinamentos com apenas uma linha
    + Diminuiu a quantidade de memória exigida pelas tarefas de previsão.
    + Adicionadas melhores mensagens de erro se a coluna de tempo tiver um formato incorreto.
    + Habilitada a imputação personalizada com valor constante para tarefas de previsão de dados X e y.
  + **azureml-core**
    + Adicionado suporte para carregar o servicePrincipalName de variáveis de ambiente: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID e AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introduziu um novo parâmetro `support_multi_line` para `Dataset.Tabular.from_delimited_files` : por padrão ( `support_multi_line=False` ), todas as quebras de linha, incluindo aquelas em valores de campo entre aspas, serão interpretadas como uma quebra de registro. A leitura de dados dessa maneira é mais rápida e mais otimizada para execução paralela em vários núcleos de CPU. No entanto, isso pode resultar na produção silenciosa de mais registros com valores de campo desalinhados. Isso deve ser definido como `True` quando os arquivos delimitados são conhecidos por conter quebras de linha entre aspas.
    + Adicionada a capacidade de registrar ADLS Gen2 na CLI do Azure Machine Learning
    + Parâmetro renomeado ' fine_grain_timestamp ' para ' timestamp ' e parâmetro ' coarse_grain_timestamp ' como ' partition_timestamp ' para o método with_timestamp_columns () em TabularDataset para refletir melhor o uso dos parâmetros.
    + Tamanho máximo do nome de experimento aumentado para 255.
  + **azureml-interpret**
    + Atualizado o azureml-interpretar para interpretar-Community 0,7. *
  + **azureml-sdk**
    + Mudando para dependências com til de versão compatível para o suporte de aplicação de patch em versões de pré-lançamento e estáveis.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK para Python v 1.1.5

+ **Substituição de recursos**
  + **Python 2,7**
    + Última versão para dar suporte ao Python 2,7

+ **Alterações interruptivas**
  + **Controle de versão semântico 2.0.0**
    + A partir da versão 1,1, o SDK do Python do Azure ML adota o controle de versão semântico 2.0.0. [Leia mais aqui](https://semver.org/). Todas as versões subsequentes seguirão o novo esquema de numeração e o contrato de controle de versão semântico. 

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Altere o nome de comando da CLI do ponto de extremidade de ' AZ ml Endpoint AKs ' para ' AZ ml Endpoint real time ' para fins de consistência.
    + atualizar instruções de instalação da CLI para a CLI do Branch estável e experimental
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
  + **azureml-automl-core**
    + Habilitada a inferência de modo de lote (levando várias linhas uma vez) para modelos AutoML ONNX
    + Melhoria da detecção de frequência nos conjuntos de dados, falta de dados ou contém pontos de dados irregulares
    + Adicionada a capacidade de remover pontos de dados que não estão em conformidade com a frequência dominante.
    + Alteração da entrada do construtor para obter uma lista de opções para aplicar as opções imputação para as colunas correspondentes.
    + O log de erros foi aprimorado.
  + **azureml-automl-runtime**
    + Corrigido o problema com o erro gerado se a granulação não estava presente no conjunto de treinamento exibido no conjunto de teste
    + Removida a y_query requisito durante a pontuação no serviço de previsão
    + Corrigido o problema com a previsão quando o conjunto de dados contém granulações curtas com intervalos de tempo longos.
    + Corrigido o problema quando o horizonte máximo automático é ativado e a coluna data contém datas na forma de cadeias de caracteres. A conversão adequada e as mensagens de erro foram adicionadas para quando a conversão em data não é possível
    + Usando NumPy e SciPy nativos para serialização e desserialização de dados intermediários para FileCacheStore (usado para execuções locais de AutoML)
    + Corrigido um bug em que as execuções de filho com falha poderiam ficar paralisadas no estado de execução.
    + Maior velocidade de personalização.
    + Correção da verificação de frequência durante a pontuação, agora as tarefas de previsão não exigem uma equivalência de frequência estrita entre o treinamento e o conjunto de teste.
    + Alteração da entrada do construtor para obter uma lista de opções para aplicar as opções imputação para as colunas correspondentes.
    + Correção de erros relacionados à seleção do tipo de latência.
    + Corrigido o erro não classificado gerado nos conjuntos de dados, com refinamentos com a única linha
    + Corrigido o problema com a detecção de frequência lentidão.
    + Corrige um bug no tratamento de exceção do AutoML que causou o verdadeiro motivo para a falha do treinamento ser substituída por um AttributeError.
  + **azureml-CLI-comum**
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
  + **azureml-contrib-Mir**
    + Adiciona a funcionalidade na classe MirWebservice para recuperar o token de acesso
    + Usar autenticação de token para MirWebservice por padrão durante MirWebservice. Run () atualizar somente chamada se a chamada falhar
    + A implantação do Mir WebService agora requer SKUs apropriados [Standard_DS2_v2, Standard_F16, Standard_A2_v2] em vez de [Ds2v2, A2v2 e F16] respectivamente.
  + **azureml-contrib-pipeline-steps**
    + Parâmetro opcional side_inputs adicionado a ParallelRunStep. Esse parâmetro pode ser usado para montar a pasta no contêiner. Atualmente, os tipos com suporte são DataReference e PipelineData.
    + Os parâmetros passados em ParallelRunConfig podem ser substituídos passando parâmetros de pipeline agora. Novos parâmetros de pipeline com suporte aml_mini_batch_size, aml_error_threshold, aml_logging_level aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte da versão anterior).
  + **azureml-core**
    + Os WebServices do AzureML implantados agora serão padronizados para `INFO` registro em log. Isso pode ser controlado pela definição da `AZUREML_LOG_LEVEL` variável de ambiente no serviço implantado.
    + O SDK do Python usa o serviço de descoberta para usar o ponto de extremidade ' API ' em vez de ' pipelines '.
    + Troque para as novas rotas em todas as chamadas do SDK.
    + O roteamento de chamadas para o ModelManagementService foi alterado para uma nova estrutura unificada.
      + Método de atualização do espaço de trabalho disponibilizado publicamente.
      + Parâmetro image_build_compute adicionado no método de atualização do espaço de trabalho para permitir que o usuário atualize a computação para a compilação da imagem.
    + Foram adicionadas mensagens de substituição ao antigo fluxo de trabalho de criação de perfil. Correção dos limites de CPU e memória de criação de perfil.
    + Adicionado RSection como parte do ambiente para executar trabalhos do R.
    + Validação adicionada a `Dataset.mount` para gerar erro quando a origem do conjunto de dados não está acessível ou não contém dados.
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para a CLI do armazenamento de datastore para registrar o contêiner de blob do Azure que permitirá que você registre o contêiner de BLOB que está atrás de uma VNet.
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
    + Correção do problema no aks.py _deploy.
    + Valida a integridade dos modelos que estão sendo carregados para evitar falhas de armazenamento silencioso.
    + Agora, o usuário pode especificar um valor para a chave de autenticação ao regenerar chaves para WebServices.
    + Corrigido o bug em que letras maiúsculas não podem ser usadas como nome de entrada do conjunto de dados.
  + **azureml-padrões**
    + `azureml-dataprep` Agora será instalado como parte do `azureml-defaults` . Não é mais necessário instalar o data Prep [fusível] manualmente em destinos de computação para montar conjuntos de dados.
  + **azureml-interpret**
    + Atualizado o azureml-interpretar para interpretar-Community 0,6. *
    + Atualizado o azureml-interpretar para depender do 0.5.0 da comunidade de interpretação
    + Exceções do estilo do azureml adicionadas ao azureml-interpret
    + Correção de serialização de DeepScoringExplainer para modelos de Keras
  + **azureml-mlflow**
    + Adicionar suporte para nuvens soberanas a azureml. mlflow
  + **azureml-pipeline-core**
    + O notebook de Pontuação do lote de pipeline agora usa ParallelRunStep
    + Correção de um bug em que os resultados de PythonScriptStep podem ser reutilizados incorretamente apesar de alterar a lista de argumentos
    + Adicionada a capacidade de definir o tipo de colunas ao chamar os métodos parse_ * em `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Moveu o `AutoMLStep` para o `azureml-pipeline-steps` pacote. Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
    + Exemplo de documentação adicionada para o conjunto de dados como entrada PythonScriptStep
  + **azureml-tensorboard**
    + atualizado o azureml-tensorboard para dar suporte ao tensorflow 2,0
    + Mostrar o número de porta correto ao usar uma porta Tensorboard personalizada em uma instância de computação
  + **azureml-train-automl-client**
    + Corrigido um problema em que determinados pacotes podem ser instalados em versões incorretas em execuções remotas.
    + correção do problema de substituição de FeaturizationConfig que filtra a configuração personalização personalizada.
  + **azureml-train-automl-runtime**
    + Correção do problema com a detecção de frequência nas execuções remotas
    + Moveu o `AutoMLStep` no `azureml-pipeline-steps` pacote. Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Dando suporte ao PyTorch versão 1,4 no estimador de PyTorch
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>SDK do Azure Machine Learning para Python v 1.1.2 RC0 (pré-lançamento)

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Habilitada a inferência de modo de lote (levando várias linhas uma vez) para modelos AutoML ONNX
    + Melhoria da detecção de frequência nos conjuntos de dados, falta de dados ou contém pontos de dados irregulares
    + Adicionada a capacidade de remover pontos de dados que não estão em conformidade com a frequência dominante.
  + **azureml-automl-runtime**
    + Corrigido o problema com o erro gerado se a granulação não estava presente no conjunto de treinamento exibido no conjunto de teste
    + Removida a y_query requisito durante a pontuação no serviço de previsão
  + **azureml-contrib-Mir**
    + Adiciona a funcionalidade na classe MirWebservice para recuperar o token de acesso
  + **azureml-core**
    + Os WebServices do AzureML implantados agora serão padronizados para `INFO` registro em log. Isso pode ser controlado pela definição da `AZUREML_LOG_LEVEL` variável de ambiente no serviço implantado.
    + Corrija a iteração em `Dataset.get_all` para retornar todos os conjuntos de valores registrados com o espaço de trabalho.
    + Melhorar a mensagem de erro quando o tipo inválido é passado para o `path` argumento de APIs de criação de conjunto de texto.
    + O SDK do Python usa o serviço de descoberta para usar o ponto de extremidade ' API ' em vez de ' pipelines '.
    + Alternar para as novas rotas em todas as chamadas do SDK
    + Altera o roteamento de chamadas para o ModelManagementService para uma nova estrutura unificada
      + Método de atualização do espaço de trabalho disponibilizado publicamente.
      + Parâmetro image_build_compute adicionado no método de atualização do espaço de trabalho para permitir que o usuário atualize a computação para a compilação da imagem
    +  Foram adicionadas mensagens de substituição ao antigo fluxo de trabalho de criação de perfil. Correção de limites de CPU e memória de criação de perfil
  + **azureml-interpret**
    + atualizar o azureml-interpretar para interpretação-comunidade 0,6. *
  + **azureml-mlflow**
    + Adicionar suporte para nuvens soberanas a azureml. mlflow
  + **azureml-pipeline-steps**
    + Movido `AutoMLStep` para o `azureml-pipeline-steps package` . Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Corrigido um problema em que determinados pacotes podem ser instalados em versões incorretas em execuções remotas.
  + **azureml-train-automl-runtime**
    + Correção do problema com a detecção de frequência nas execuções remotas
    + Movido `AutoMLStep` para o `azureml-pipeline-steps package` . Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Movido `AutoMLStep` para o `azureml-pipeline-steps package` . Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>SDK do Azure Machine Learning para Python v 1.1.1 RC0 (pré-lançamento)

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
  + **azureml-automl-core**
    + O log de erros foi aprimorado.
  + **azureml-automl-runtime**
    + Corrigido o problema com a previsão quando o conjunto de dados contém granulações curtas com intervalos de tempo longos.
    + Corrigido o problema quando o horizonte máximo automático é ativado e a coluna data contém datas na forma de cadeias de caracteres. Adicionamos uma conversão adequada e um erro sensato se a conversão em data não for possível
    + Usando NumPy e SciPy nativos para serialização e desserialização de dados intermediários para FileCacheStore (usado para execuções locais de AutoML)
    + Corrigido um bug em que as execuções de filho com falha poderiam ficar paralisadas no estado de execução.
  + **azureml-CLI-comum**
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
  + **azureml-core**
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para a CLI do armazenamento de datastore para registrar o contêiner de BLOBs do Azure que permitirá que você registre o contêiner de BLOB que está atrás de uma VNet
    + A criação de perfil de instância única foi corrigida para produzir uma recomendação e foi disponibilizada no SDK principal.
    + Correção do problema no aks.py _deploy
    + Valida a integridade dos modelos que estão sendo carregados para evitar falhas de armazenamento silencioso.
  + **azureml-interpret**
    + exceções do estilo do azureml adicionadas ao azureml-interpret
    + correção de serialização de DeepScoringExplainer para modelos de Keras
  + **azureml-pipeline-core**
    + O notebook de Pontuação do lote de pipeline agora usa ParallelRunStep
  + **azureml-pipeline-steps**
    + Moveu o `AutoMLStep` no `azureml-pipeline-steps` pacote. Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Parâmetro opcional side_inputs adicionado a ParallelRunStep. Esse parâmetro pode ser usado para montar a pasta no contêiner. Atualmente, os tipos com suporte são DataReference e PipelineData.
  + **azureml-tensorboard**
    + atualizado o azureml-tensorboard para dar suporte ao tensorflow 2,0
  + **azureml-train-automl-client**
    + correção do problema de substituição de FeaturizationConfig que filtra a configuração personalização personalizada.
  + **azureml-train-automl-runtime**
    + Moveu o `AutoMLStep` no `azureml-pipeline-steps` pacote. Preteriu o `AutoMLStep` no `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Dando suporte ao PyTorch versão 1,4 no estimador de PyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>SDK do Azure Machine Learning para Python v 1.1.0 RC0 (pré-lançamento)

+ **Alterações interruptivas**
  + **Controle de versão semântico 2.0.0**
    + A partir da versão 1,1, o SDK do Python do Azure ML adota o controle de versão semântico 2.0.0. [Leia mais aqui](https://semver.org/). Todas as versões subsequentes seguirão o novo esquema de numeração e o contrato de controle de versão semântico. 
  
+ **Correções de bugs e melhorias**
  + **azureml-automl-runtime**
    + Maior velocidade de personalização.
    + Correção da verificação de frequência durante a pontuação, agora nas tarefas de previsão, não exigimos equivalência de frequência estrita entre o treinamento e o conjunto de teste.
  + **azureml-core**
    + Agora, o usuário pode especificar um valor para a chave de autenticação ao regenerar chaves para WebServices.
  + **azureml-interpret**
    + Atualizado o azureml-interpretar para depender do 0.5.0 da comunidade de interpretação
  + **azureml-pipeline-core**
    + Correção de um bug em que os resultados de PythonScriptStep podem ser reutilizados incorretamente apesar de alterar a lista de argumentos
  + **azureml-pipeline-steps**
    + Exemplo de documentação adicionada para o conjunto de dados como entrada PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Os parâmetros passados em ParallelRunConfig podem ser substituídos passando parâmetros de pipeline agora. Novos parâmetros de pipeline com suporte aml_mini_batch_size, aml_error_threshold, aml_logging_level aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte da versão anterior).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK para Python v 1.0.85

+ **Novos recursos**
  + **azureml-core**
    + Obtenha o uso principal atual e a limitação de cota para recursos de AmlCompute em um determinado espaço de trabalho e assinatura
  
  + **azureml-contrib-pipeline-steps**
    + Habilitar o usuário a passar o conjunto de resultados de tabela como resultado intermediário da etapa anterior para parallelrunstep

+ **Correções de bugs e melhorias**
  + **azureml-automl-runtime**
    + Removida a necessidade de y_query coluna na solicitação para o serviço de previsão implantado. 
    + O ' y_query ' foi removido da seção de solicitação de serviço de bloco de anotações de suco de Dominick de laranja.
    + Correção do bug que impede a previsão nos modelos implantados, operando em conjuntos de dados com colunas de data e hora.
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
  + **azureml-contrib-interpret**
    + Os explicadores de texto removidos do azureml-contrib-interprete como explicações de texto foram movidos para o repositório de texto de interpretação que será liberado em breve.
  + **azureml-core**
    + Conjunto de conjuntos: os usos do conjunto de arquivos não dependem mais do numpy e do pandas para serem instalados no Python env.
    + Alterado LocalWebservice.wait_for_deployment () para verificar o status do contêiner do Docker local antes de tentar executar ping no ponto de extremidade de integridade, reduzindo muito o tempo necessário para relatar uma implantação com falha.
    + Correção da inicialização de uma propriedade interna usada em LocalWebservice. reload () quando o objeto de serviço é criado a partir de uma implantação existente usando o Construtor LocalWebservice ().
    + Mensagem de erro editada para fins de esclarecimento.
    + Adicionado um novo método chamado get_access_token () a AksWebservice que retornará o objeto AksServiceAccessToken, que contém o token de acesso, atualize após o carimbo de data/hora, expiração em carimbo de data/hora e tipo de token. 
    + O método existente de get_token () preterido em AksWebservice como o novo método retorna todas as informações retornadas por esse método.
    + Saída modificada do comando AZ ml Service Get-Access-token. Token renomeado para accessToken e refreshBy para refreshAfter. Propriedades expiryOn e TokenType adicionadas.
    + Get_active_runs fixo
  + **azureml-explain-model**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-interpret**
    + atualizado o shap para 0.33.0 e interprete-Community para 0,4. *
  + **azureml-train-automl-runtime**
    + O coeficiente de correlação de Matthews foi adicionado como uma métrica de classificação, para classificação binária e multiclasse.
    + Substituir o sinalizador de pré-processamento do código e substituído por personalização-personalização está ativado por padrão

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK para Python v 1.0.83

+ **Novos recursos**
  + Conjunto de dados: Adicione duas opções `on_error` e `out_of_range_datetime` para `to_pandas_dataframe` que o falhe quando os dados tiverem valores de erro em vez de preenchê-los com `None` .
  + Espaço de trabalho: adicionou o `hbi_workspace` sinalizador para espaços de trabalho com dados confidenciais que habilitam a criptografia adicional e desabilita o diagnóstico avançado em espaços de trabalho. Também adicionamos suporte para trazer suas próprias chaves para a instância de Cosmos DB associada, especificando os `cmk_keyvault` `resource_cmk_uri` parâmetros e ao criar um espaço de trabalho, que cria uma instância de Cosmos DB em sua assinatura ao provisionar seu espaço de trabalho. [Saiba mais aqui.](./concept-data-encryption.md#azure-cosmos-db)

+ **Correções de bugs e melhorias**
  + **azureml-automl-runtime**
    + Correção de uma regressão que causou a geração de um TypeError ao executar AutoML em versões do Python abaixo de 3.5.4.
  + **azureml-core**
    + O bug corrigido no `datastore.upload_files` caminho relativo que não foi iniciado com o `./` não pôde ser usado.
    + Mensagens de substituição adicionadas para todos os caminhos de código de classe de imagem
    + A construção de URL de Gerenciamento de Modelos fixa para a região da 21Vianet do Azure China.
    + Correção do problema em que os modelos que usam source_dir não puderam ser empacotados para Azure Functions.    
    + Adicionada uma opção para [Environment.build_local ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) para enviar uma imagem para o registro de contêiner do espaço de trabalho do AzureML
    + O SDK foi atualizado para usar a nova biblioteca de tokens no Azure Synapse de forma compatível.
  + **azureml-interpret**
    + Corrigido o bug onde nenhum foi retornado quando nenhuma explicação estava disponível para download. Agora gera uma exceção, correspondendo ao comportamento em outro lugar.
  + **azureml-pipeline-steps**
    + Não permitido passar de `DatasetConsumptionConfig` s para `Estimator` `inputs` o parâmetro do quando o `Estimator` será usado em um `EstimatorStep` .
  + **azureml-sdk**
    + Adicionado o cliente AutoML ao pacote azureml-SDK, permitindo que as execuções de AutoML remotas sejam enviadas sem instalar o pacote completo de AutoML.
  + **azureml-train-automl-client**
    + Alinhamento corrigido na saída do console para execuções de AutoML
    + Corrigido um bug em que a versão incorreta do pandas pode ser instalada em amlcompute remotas.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK para Python v 1.0.81

+ **Correções de bugs e melhorias**
  + **azureml-contrib-interpret**
    + adiar dependência de shap para interpretação-Community do azureml-interpret
  + **azureml-core**
    + O destino de computação agora pode ser especificado como um parâmetro para os objetos de configuração de implantação correspondentes. Isso é especificamente o nome do destino de computação no qual implantar, não o objeto do SDK.
    + Adicionadas informações de CreatedBy a objetos de modelo e de serviço. Pode ser acessado through.created_by
    + Foi corrigido ContainerImage. Run (), que não configurava corretamente a porta HTTP do contêiner do Docker.
    + Tornar `azureml-dataprep` opcional para `az ml dataset register` comando da CLI
    + Correção de um bug em que o `TabularDataset.to_pandas_dataframe` retornoia incorreto para um leitor alternativo e imprime um aviso.
  + **azureml-explain-model**
    + adiar dependência de shap para interpretação-Community do azureml-interpret
  + **azureml-pipeline-core**
    + Adicionada nova etapa de pipeline `NotebookRunnerStep` para executar um bloco de anotações local como uma etapa no pipeline.
    + Funções de get_all preteridas removidas para PublishedPipelines, agendas e PipelineEndpoints
  + **azureml-train-automl-client**
    + Foi iniciada a reprovação de data_script como uma entrada para AutoML.


## <a name="2019-12-09"></a>09/12/2019

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK para Python v 1.0.79

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + FeaturizationConfig removidas para registro em log
      + Atualizado o log para o log "auto"/"off"/"personalizado" apenas.
  + **azureml-automl-runtime**
    + Adicionado suporte para pandas. Série e pandas. Categórico para detectar o tipo de dados da coluna. Anteriormente, só há suporte para numpy. ndarray
      + Foram adicionadas alterações de código relacionadas para lidar com dtype categóricos corretamente.
    + A interface da função de previsão foi aprimorada: o parâmetro y_pred foi tornado opcional. -Os docstrings foram aprimorados.
  + **azureml-contrib-dataset**
    + Correção de um bug em que não foi possível montar os conjuntos de itens rotulados.
  + **azureml-core**
    + Correção de bug para `Environment.from_existing_conda_environment(name, conda_environment_name)` . O usuário pode criar uma instância do ambiente que é a réplica exata do ambiente local
    + Alterados métodos de conjuntos de valores relacionados à série temporal para `include_boundary=True` por padrão.
  + **azureml-train-automl-client**
    + Corrigido o problema em que os resultados da validação não são impressos quando mostrar saída está definido como false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK para Python v 1.0.76

+ **Alterações interruptivas**
  + Problemas de atualização do Azureml-Train-AutoML
    + Atualizando para o azureml-Train-automl>= 1.0.76 do azureml-Train-automl<1.0.76 pode causar instalações parciais, fazendo com que algumas importações de AutoML falhem. Para resolver isso, você pode executar o script de instalação encontrado em https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Ou, se você estiver usando o Pip diretamente, poderá:
      + "instalação de Pip--atualizar o azureml-Train-automl"
      + "instalação de Pip--ignorar-instalado azureml-Train-automl-Client"
    + ou você pode desinstalar a versão antiga antes de atualizar
      + "desinstalação de Pip azureml-Train-automl"
      + "instalação de Pip azureml-Train-automl"

+ **Correções de bugs e melhorias**
  + **azureml-automl-runtime**
    + AutoML agora levará em conta as classes true e false ao calcular as métricas escalares médias para tarefas de classificação binária.
    + O aprendizado de máquina e o código de treinamento foram movidos no AzureML-AutoML-Core para um novo pacote AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Ao chamar `to_pandas_dataframe` em um DataSet rotulado com a opção de download, agora você pode especificar se deseja substituir os arquivos existentes ou não.
    + Ao chamar `keep_columns` ou `drop_columns` que resulta em uma série temporal, rótulo ou coluna de imagem sendo descartada, os recursos correspondentes também serão descartados para o conjunto de resultados.
    + Corrigido um problema com o carregador de pytorch para a tarefa de detecção de objeto.
  + **azureml-contrib-interpret**
    + O widget de explicação Dashboard foi removido de azureml-contrib-interpret, pacote alterado para referenciar o novo no interpret_community
    + Versão atualizada da interpretação-Community para 0.2.0
  + **azureml-core**
    + Melhorar o desempenho do `workspace.datasets` .
    + Foi adicionada a capacidade de registrar o armazenamento de dados do Azure SQL usando o nome de usuário e a autenticação de senha
    + Correção para carregar RunConfigurations de caminhos relativos.
    + Ao chamar `keep_columns` ou `drop_columns` que resulta em uma coluna de série temporal sendo removida, os recursos correspondentes também serão descartados para o conjunto de resultados.
  + **azureml-interpret**
    + versão atualizada da interpretação-Community para 0.2.0
  + **azureml-pipeline-steps**
    + Valores com suporte documentados para `runconfig_pipeline_params` as etapas de pipeline do Azure Machine Learning.
  + **azureml-pipeline-core**
    + Adicionada a opção de CLI para baixar a saída no formato JSON para comandos de pipeline.
  + **azureml-Train-automl**
    + Divida o AzureML-Train-AutoML em dois pacotes, um pacote do cliente AzureML-Train-AutoML-Client e um pacote de treinamento de ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Adicionou um cliente fino para enviar experimentos AutoML sem a necessidade de instalar nenhuma dependência de Machine Learning localmente.
    + Registro de log fixo de atraso detectado, tamanhos de janela sem interrupção e horizontes máximos em execuções remotas detectadas automaticamente.
  + **azureml-train-automl-runtime**
    + Foi adicionado um novo pacote AutoML para isolar os componentes de aprendizado de máquina e tempo de execução do cliente.
  + **azureml-contrib-Train-RL**
    + Adicionado suporte do reforço Learning no SDK.
    + Adicionado suporte a AmlWindowsCompute no RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK para Python v 1.0.74

  + **Versão prévia dos recursos**
    + **azureml-contrib-dataset**
      + Depois de importar o azureml-contrib-DataSet, você pode chamar `Dataset.Labeled.from_json_lines` em vez de `._Labeled` criar um conjunto de um DataSet.
      + Ao chamar `to_pandas_dataframe` em um DataSet rotulado com a opção de download, agora você pode especificar se deseja substituir os arquivos existentes ou não.
      + Ao chamar `keep_columns` ou `drop_columns` que resulta em uma série temporal, rótulo ou coluna de imagem sendo descartada, os recursos correspondentes também serão descartados para o conjunto de resultados.
      + Correção de problemas com o carregador PyTorch ao chamar `dataset.to_torchvision()` .

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Adicionada a criação de perfil de modelo à CLI de visualização.
    + Corrige alterações significativas no armazenamento do Azure fazendo com que a CLI do AzureML falhe.
    + Adicionado Load Balancer tipo a MLC para tipos AKS
  + **azureml-automl-core**
    + Corrigido o problema com a detecção de horizonte máximo na série temporal, tendo valores ausentes e vários refinamentos.
    + Corrigido o problema com falhas durante a geração de divisões de validação cruzada.
    + Substitua esta seção por uma mensagem no formato de redução para aparecer nas notas de versão: tratamento aprimorado de refinamentos em conjuntos de dados de previsão.
    + Correção do problema com o mascaramento de algumas informações do usuário durante o registro em log. -Registro em log dos erros aprimorados durante a previsão de execuções.
    + Adicionar psutil como uma dependência Conda ao arquivo de implantação yml gerado automaticamente.
  + **azureml-contrib-Mir**
    + Corrige alterações significativas no armazenamento do Azure fazendo com que a CLI do AzureML falhe.
  + **azureml-core**
    + Corrige um bug que causou a implantação de modelos em Azure Functions para produzir o 500S.
    + Corrigido um problema em que o arquivo amlignore não foi aplicado em instantâneos.
    + Adicionada uma nova amlcompute.get_active_runs de API que retorna um gerador para execução e em fila é executado em um determinado amlcompute.
    + Adicionado Load Balancer tipo a MLC para tipos AKS.
    + Adicionado append_prefix parâmetro BOOL para download_files em run.py e download_artifacts_from_prefix no artifacts_client. Esse sinalizador é usado para mesclar seletivamente o FilePath de origem para que apenas o nome de arquivo ou pasta seja adicionado ao output_directory
    + Corrigir o problema de desserialização para com o uso do conjunto de um `run_config.yml` .
    + Ao chamar `keep_columns` ou `drop_columns` que resulta em uma coluna de série temporal sendo removida, os recursos correspondentes também serão descartados para o conjunto de resultados.
  + **azureml-interpret**
    + Atualizada a versão de interpretação da Comunidade para 0.1.0.3
  + **azureml-Train-automl**
    + Corrigido um problema em que automl_step pode não imprimir problemas de validação.
    + O register_model corrigido para ter sucesso mesmo que o ambiente do modelo não tenha dependências locais.
    + Corrigido um problema em que algumas execuções remotas não estavam habilitadas para o Docker.
    + Adicione o log da exceção que está causando a falha prematura de uma execução local.
  + **azureml-train-core**
    + Considere resume_from execuções no cálculo de melhores execuções de filhos de ajuste de hiperparâmetro.
  + **azureml-pipeline-core**
    + Manipulação de parâmetro fixo na construção de argumento de pipeline.
    + Adicionada a descrição de pipeline e o parâmetro de tipo de etapa YAML.
    + Novo formato YAML para etapa de pipeline e aviso de substituição adicionado para o formato antigo.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Experiência na Web

A página de aterrissagem do espaço de trabalho colaborativo em [https://ml.azure.com](https://ml.azure.com) foi aprimorada e remarcada como o Azure Machine Learning Studio.

No estúdio, você pode treinar, testar, implantar e gerenciar Azure Machine Learning ativos, como conjuntos de informações, pipelines, modelos, pontos de extremidade e muito mais.

Acesse as seguintes ferramentas de criação baseadas na Web no estúdio:

| Ferramenta baseada na Web | Descrição | 
|-|-|-|
| VM do notebook (visualização) | Estação de trabalho baseada em nuvem totalmente gerenciada | 
| [Machine Learning automatizado](tutorial-first-experiment-automated-ml.md) (visualização) | Não há experiência de código para automatizar o desenvolvimento de modelos do Machine Learning | 
| [Designer](concept-designer.md) | Ferramenta de modelagem de aprendizado de máquina de arrastar e soltar anteriormente conhecida como interface visual | 


### <a name="azure-machine-learning-designer-enhancements"></a>Aprimoramentos do designer de Azure Machine Learning

+ Anteriormente conhecido como interface visual 
+    11 novos [módulos](algorithm-module-reference/module-reference.md) , incluindo recomendados, classificadores e utilitários de treinamento, incluindo engenharia de recursos, validação cruzada e transformação de dados.

### <a name="r-sdk"></a>SDK do R 
 
Os cientistas de dados e os desenvolvedores de ia usam o [SDK do Azure Machine Learning para R](https://github.com/Azure/azureml-sdk-for-r) para criar e executar fluxos de trabalho de aprendizado de máquina com Azure Machine Learning.

O SDK do Azure Machine Learning para R usa o `reticulate` pacote para associar ao SDK do Python. Ligando diretamente ao Python, o SDK para R permite que você acesse os principais objetos e métodos implementados no SDK do Python de qualquer ambiente de R que você escolher.

Os principais recursos do SDK incluem:

+    Gerenciar recursos de nuvem para monitorar, registrar em log e organizar seus experimentos de machine learning.
+    Treine modelos usando recursos de nuvem, incluindo treinamento de modelo com aceleração de GPU.
+    Implante seus modelos como WebServices em ACI (instâncias de contêiner do Azure) e AKS (serviço kubernetes do Azure).

Consulte o [site do pacote](https://azure.github.io/azureml-sdk-for-r) para obter a documentação completa.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integração do Azure Machine Learning com a grade de eventos 

Azure Machine Learning agora é um provedor de recursos para a grade de eventos, você pode configurar eventos de Machine Learning por meio do portal do Azure ou CLI do Azure. Os usuários podem criar eventos para conclusão de execução, registro de modelo, implantação de modelo e descompasso de dados detectados. Esses eventos podem ser roteados para manipuladores de eventos com suporte na grade de eventos para consumo. Consulte os artigos sobre o [esquema](../event-grid/event-schema-machine-learning.md) de eventos do Machine Learning e [tutorial](how-to-use-event-grid.md) para obter mais detalhes.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK para Python v 1.0.72

+ **Novos recursos**
  + Os monitores de conjunto de dados adicionados por meio do pacote [**azureml-descompasso**](/python/api/azureml-datadrift) , permitindo o monitoramento de conjuntos de dados de série temporal para descompasso ou outras alterações estatísticas ao longo do tempo. Alertas e eventos podem ser disparados se o descompasso for detectado ou se outras condições nos dados forem atendidas. Consulte [nossa documentação](how-to-monitor-datasets.md) para obter detalhes.
  + Anunciando duas novas edições (também chamadas de SKU intercambiáveis) em Azure Machine Learning. Com esta versão, agora você pode criar um espaço de trabalho básico ou de Azure Machine Learning empresarial. Todos os espaços de trabalho existentes serão padronizados para a edição básica e você poderá ir para a portal do Azure ou para o estúdio para atualizar o espaço de trabalho a qualquer momento. Você pode criar um espaço de trabalho básico ou empresarial do portal do Azure. Leia [nossa documentação](./how-to-manage-workspace.md) para saber mais. No SDK, a edição do seu espaço de trabalho pode ser determinada usando a propriedade "SKU" do seu objeto de espaço de trabalho.
  + Também fizemos aprimoramentos para Azure Machine Learning computação. agora você pode exibir métricas para seus clusters (como nós totais, nós em execução, cota de núcleo total) em Azure Monitor, além de exibir os logs de diagnóstico para depuração. Além disso, você também pode exibir execuções em fila ou em execução no momento no cluster e detalhes como os IPs dos vários nós no cluster. Você pode exibi-los no portal ou usando funções correspondentes no SDK ou na CLI.

  + **Versão prévia dos recursos**
    + Estamos liberando o suporte de visualização para a criptografia de disco do SSD local no Azure Machine Learning computação. Gere um tíquete de suporte técnico para fazer com que sua assinatura seja listada para usar esse recurso.
    + Visualização pública de inferência de Azure Machine Learning lote. Azure Machine Learning inferência de lote tem como alvo grandes trabalhos de inferência que não diferenciam o tempo. A inferência de lote fornece o dimensionamento de computação de inferência econômica, com taxa de transferência incomparável para aplicativos assíncronos. Ele é otimizado para inferência de alta taxa de transferência, incêndio e esqueci sobre grandes coleções de dados.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Funcionalidades habilitadas para DataSet rotulado
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Adicionada a CLI de DataSet. Para obter mais informações: `az ml dataset --help`
    + Adicionado suporte para implantação e empacotamento de modelos com suporte (ONNX, scikit-Learn e TensorFlow) sem uma instância InferenceConfig.
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. -O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
  + **azureml-automl-core**
    + O treinamento de uma iteração seria executado em um processo filho somente quando as restrições de tempo de execução estiverem sendo definidas.
    + Adição de um Guardrail para tarefas de previsão, para verificar se um max_horizon especificado causará um problema de memória no computador determinado ou não. Se for, uma mensagem Guardrail será exibida.
    + Suporte adicionado para frequências complexas, como dois anos e um mês. -Abrangente mensagem de erro adicionada se a frequência não puder ser determinada.
    + Adicionar azureml-padrões para o Conda env gerado automaticamente para resolver a falha de implantação do modelo
    + Permitir que dados intermediários no pipeline de Azure Machine Learning sejam convertidos em DataSet tabular e usados no `AutoMLStep` .
    + Implementada atualização de finalidade de coluna para streaming.
    + Implementada atualização de parâmetro do transformador para imputer e HashOneHotEncoder para streaming.
    + Adicionou o tamanho de dados atual e o tamanho mínimo de dados exigido para as mensagens de erro de validação.
    + Atualizado o tamanho mínimo de dados necessários para validação cruzada para garantir um mínimo de dois exemplos em cada dobra de validação.
  + **azureml-CLI-comum**
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow.
    + O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
  + **azureml-contrib-GBDT**
    + Corrigido o canal de liberação para o bloco de anotações
    + Foi adicionado um aviso para o destino de computação não AmlCompute para o qual não há suporte
    + Foi adicionado um estimador de LightGMB para o pacote azureml-contrib-GBDT
  + [**azureml-core**](/python/api/azureml-core)
    + A CLI agora dá suporte ao empacotamento de modelo.
    + Adicionar aviso de reprovação para APIs de conjunto de Consulte o aviso de alteração da API do conjunto de notícias em https://aka.ms/tabular-dataset .
    + Altere [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) para retornar o nome e a versão do registro se o conjunto de registros estiver registrado.
    + Corrija um bug de que ScriptRunConfig com DataSet as argument não pode ser usado repetidamente para enviar a execução do experimento.
    + Os conjuntos de valores recuperados durante uma execução serão rastreados e poderão ser vistos na página de detalhes da execução ou chamando [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) após a conclusão da execução.
    + Permitir que dados intermediários no pipeline de Azure Machine Learning sejam convertidos em DataSet tabular e usados no [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Adicionado suporte para implantação e empacotamento de modelos com suporte (ONNX, scikit-Learn e TensorFlow) sem uma instância InferenceConfig.
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
    +  Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. O registro de modelo aceita dados de entrada de exemplo, dados de saída de exemplo e configuração de recurso para o modelo.
    + Adição de novo armazenamento de dados para o Azure Database para MySQL. Exemplo adicionado para usar o banco de dados do Azure para MySQL no DataTransferStep em pipelines de Azure Machine Learning.
    + Funcionalidade adicionada para adicionar e remover marcas de experimentos com funcionalidade adicionada para remover marcas de execuções
    + Foi adicionado o sinalizador de substituição para a implantação do serviço (ACI e AKS) no SDK e na CLI. Se fornecido, substituirá o serviço existente se o serviço com o nome já existir. Se o serviço não existir, o criará um novo serviço.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Movido de `azureml-contrib-datadrift` para `azureml-datadrift`
    + Adicionado suporte para monitoramento de conjuntos de data de série temporal para descompasso e outras medidas estatísticas
    + Novos métodos `create_from_model()` e `create_from_dataset()` para a [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) classe. O `create()` método será preterido.
    + Ajustes nas visualizações no Python e na interface do usuário no Azure Machine Learning Studio.
    + Suporte a agendamento de monitor semanal e mensal, além de diários para monitores de conjuntos de conjunto de DataSet.
    + Suporte a aterramento de métricas de monitor de dados para analisar dados históricos de monitores de conjunto de dados.
    + Correções de vários bugs
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + o azureml-dataprep não é mais necessário para enviar uma execução de pipeline de Azure Machine Learning do arquivo de pipeline `yaml` .
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Adicionar azureml-padrões para o Conda env gerado automaticamente para resolver a falha de implantação do modelo
    + O treinamento remoto do AutoML agora inclui os padrões do azureml para permitir a reutilização de Training env para inferência.
  + **azureml-train-core**
    + Adicionado suporte a PyTorch 1,3 no [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimador

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visual (visualização)

+ A interface visual do Azure Machine Learning (versão prévia) foi revisada para ser executada em [pipelines Azure Machine Learning](concept-ml-pipelines.md). Os pipelines (anteriormente conhecidos como experimentos) criados na interface visual agora estão totalmente integrados com a experiência de Azure Machine Learning principal.
  + Experiência de gerenciamento unificada com ativos do SDK
  + Controle de versão e acompanhamento de modelos de interface visual, pipelines e pontos de extremidade
  + Interface do usuário reprojetada
  + Implantação de inferência de lote adicionada
  + Suporte ao Azure kubernetes Service (AKS) adicionado para os destinos de computação de inferência
  + Fluxo de trabalho de criação do pipeline de etapa do novo Python
  + Nova [página de aterrissagem](https://ml.azure.com) para ferramentas de criação Visual

+ **Novos módulos**
  + Aplicar operação matemática
  + Aplicar transformação SQL
  + Valores de clipe
  + Resumir dados
  + Importar do banco de dados SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK para Python v 1.0.69

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Limitação de explicações de modelo para a melhor execução em vez de calcular explicações para cada execução. Fazendo esse comportamento mudar para local, remoto e ADB.
    + Suporte adicionado para explicações de modelo sob demanda para a interface do usuário
    + Adicionou psutil como uma dependência de `automl` e incluiu psutil como uma dependência Conda no amlcompute.
    + Corrigido o problema com tamanhos de janela lentos e contínuos nos conjuntos de dados de previsão, algumas séries que podem causar erros de algebra linear
      + Impressão adicional adicionada para os parâmetros que são determinados de heurística nas execuções de previsão.
  + **azureml-contrib-descompasso**
    + Proteção adicionada ao criar métricas de saída se o descompasso de nível de conjunto de resultados não estiver na primeira seção.
  + **azureml-contrib-interpret**
    + o pacote azureml-contrib-explique-Model foi renomeado para azureml-contrib-interpret
  + **azureml-core**
    + API adicionada para cancelar o registro de conjuntos de os. `dataset.unregister_all_versions()`
    + o pacote azureml-contrib-explique-Model foi renomeado para azureml-contrib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + API adicionada para cancelar o registro de conjuntos de os. DataSet. [unregister_all_versions ()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Adicionada a API do conjunto de dados para verificar a hora da alteração do dado. `dataset.data_changed_time`.
    + Poder consumir `FileDataset` e `TabularDataset` como entradas para `PythonScriptStep` , `EstimatorStep` e `HyperDriveStep` no pipeline Azure Machine Learning
    + O desempenho do `FileDataset.mount` foi aprimorado para pastas com um grande número de arquivos
    + Ser capaz de consumir [Filedataset](/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) como entradas para [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) no pipeline de Azure Machine Learning.
    + Desempenho do filedataset. a [montagem ()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) foi aprimorada para pastas com um grande número de arquivos
    + URL adicionada a recomendações de erro conhecidas em detalhes da execução.
    + Correção de um bug em run.get_metrics em que as solicitações falhariam se uma execução tivesse um número excessivo de filhos
    + Correção de um bug em [Run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) em que as solicitações falhariam se uma execução tivesse um número excessivo de filhos
    + Suporte adicionado para autenticação no cluster Arcadia.
    + Criar um objeto de teste Obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para acompanhamento do histórico de execução. A ID do experimento e o tempo arquivado são populados no objeto experimento na criação. Exemplo: experimento = experimento (espaço de trabalho, "novo experimento") experiment_id = experiment.id arquivo morto () e reativar () são funções que podem ser chamadas em um experimento para ocultar e restaurar o experimento de ser mostrado na UX ou retornado por padrão em uma chamada para experimentos de lista. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você poderá renomear o teste arquivado ao reativar o passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo: experiment1 = experimento (espaço de trabalho, "teste ativo") experiment1. Archive () # criar novo experimento ativo com o mesmo nome que o arquivado. experiment2. = Experimento (espaço de trabalho, "teste ativo") experiment1. reativar (new_name = "experimento ativo anterior") a lista de métodos estáticos () no experimento pode usar um filtro de nome e um filtro ViewType. Os valores ViewType são "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL" exemplo: archived_experiments = experimento. List (Workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = experimento. List (espaço de trabalho, Name = "First experimento", view_type = "ALL")
    + Suporte ao uso do ambiente para implantação de modelo e atualização de serviço
  + **azureml-datadrift**
    + O atributo show da classe DataDriftDector não dará mais suporte ao argumento opcional ' with_details '. O atributo show só apresentará o coeficiente de descompasso de dados e a contribuição de descompasso de dados das colunas de recursos.
    + Alterações de comportamento do atributo DataDriftDetector ' get_output ':
      + O parâmetro de entrada start_time, end_time são opcionais, em vez de obrigatórios;
      + As start_time específicas de entrada e/ou end_time com uma run_id específica na mesma chamada resultarão na exceção de erro de valor porque são mutuamente exclusivas
      + Por entrada específica start_time e/ou end_time, somente os resultados das execuções agendadas serão retornados;
      + O parâmetro ' daily_latest_only ' foi preterido.
    + Suporte à recuperação de saídas de descompasso de dados baseados em DataSet.
  + **azureml-explain-model**
    + Renomeia o pacote AzureML-explique-Model para o AzureML-interpret, mantendo o pacote antigo para compatibilidade com versões anteriores agora
    + correção de `automl` bug com explicações brutas definido como tarefa de classificação em vez de regressão por padrão no download de ExplanationClient
    + Adicionar suporte para `ScoringExplainer` que seja criado diretamente usando `MimicWrapper`
  + **azureml-pipeline-core**
    + Desempenho aprimorado para criação de pipeline grande
  + **azureml-train-core**
    + Adicionado suporte TensorFlow 2,0 no estimador TensorFlow
  + **azureml-Train-automl**
    + Criar um objeto de [teste](/python/api/azureml-core/azureml.core.experiment.experiment) Obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para acompanhamento do histórico de execução. A ID do experimento e o tempo arquivado são populados no objeto experimento na criação. Exemplo:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [arquivo morto ()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reativar ()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) são funções que podem ser chamadas em um experimento para ocultar e restaurar o experimento de ser mostrado na UX ou retornado por padrão em uma chamada para experimentos de lista. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você poderá renomear o teste arquivado ao reativar o passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A lista de métodos estáticos [()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) no experimento pode usar um filtro de nome e um filtro ViewType. Os valores ViewType são "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Exemplo:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Suporte ao uso do ambiente para implantação de modelo e atualização de serviço.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + O atributo show da classe [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) não dará mais suporte ao argumento opcional ' with_details '. O atributo show só apresentará o coeficiente de descompasso de dados e a contribuição de descompasso de dados das colunas de recursos.
    + Função DataDriftDetector [get_output] Python/API/azureml-datadrift/azureml. descompasso. DataDriftDetector. DataDriftDetector # Get-output-Star-Time-None--End-Time-None--Run-ID-None-) comportamento é alterado:
      + O parâmetro de entrada start_time, end_time são opcionais, em vez de obrigatórios;
      + As start_time específicas de entrada e/ou end_time com uma run_id específica na mesma invocação resultarão na exceção de erro de valor, pois são mutuamente exclusivas;
      + Por entrada específica start_time e/ou end_time, somente os resultados das execuções agendadas serão retornados;
      + O parâmetro ' daily_latest_only ' foi preterido.
    + Suporte à recuperação de saídas de descompasso de dados baseados em DataSet.
  + **azureml-explain-model**
    + Adicionar suporte para [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) a ser criado diretamente usando MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Desempenho aprimorado para criação de pipeline grande.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Adicionado suporte do TensorFlow 2,0 no estimador do [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) .
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A execução do pai não será mais possível quando a iteração da instalação falhar, pois a orquestração já cuida dela.
    + Adicionado suporte local-Docker e Conda local para experimentos de AutoML
    + Adicionado suporte local-Docker e Conda local para experimentos de AutoML.


## <a name="2019-10-08"></a>08-10-2019

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (versão prévia) para espaços de trabalho Azure Machine Learning

A guia experimento no [novo portal de espaço de trabalho](https://ml.azure.com) foi atualizada para que os cientistas de dados possam monitorar experimentos de forma mais eficaz. Você pode explorar os seguintes recursos:
+ Teste os metadados para filtrar e classificar facilmente sua lista de experimentos
+ Páginas de detalhes do experimento simplificado e de alto desempenho que permitem visualizar e comparar suas execuções
+ Novo design para executar páginas de detalhes para entender e monitorar as execuções de treinamento

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK para Python v 1.0.65

  + **Novos recursos**
    + Adicionados ambientes organizados. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram previamente compilados e armazenados em cache como imagens do Docker para uma execução mais rápida. Eles aparecem por padrão na lista de ambiente do espaço de trabalho, com o prefixo "AzureML".
    + Adicionados ambientes organizados. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram previamente compilados e armazenados em cache como imagens do Docker para uma execução mais rápida. Eles aparecem por padrão na lista de ambiente do [espaço de trabalho](/python/api/azureml-core/azureml.core.workspace%28class%29), com o prefixo "AzureML".

  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Adicionado o suporte de conversão de ONNX para o ADB e o HDI

+ **Versão prévia dos recursos**
  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM com suporte como Text featurizer (somente visualização)
    + Personalização de personalização com suporte para parâmetros de finalidade de coluna e transformador (somente visualização)
    + Explicações brutas com suporte quando o usuário habilita a explicação do modelo durante o treinamento (somente visualização)
    + Prophet adicionado para `timeseries` previsão como um pipeline treinado (somente visualização)

  + **azureml-contrib-descompasso**
    + Pacotes realocados de azureml-contrib-datalogoff para azureml-datalogoff; o `contrib` pacote será removido em uma versão futura

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Introduziu o FeaturizationConfig em AutoMLConfig e AutoMLBaseSettings
    + Introduziu o FeaturizationConfig em [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Substituir a finalidade da coluna para personalização com a coluna especificada e o tipo de recurso
      + Substituir parâmetros do transformador
    + Mensagem de substituição adicionada para explain_model () e retrieve_model_explanations ()
    + Prophet adicionado como um pipeline treinado (somente visualização)
    + Mensagem de substituição adicionada para explain_model () e retrieve_model_explanations ().
    + Prophet adicionado como um pipeline treinado (somente visualização).
    + Suporte adicionado para detecção automática de atraso de destino, tamanho de janela sem interrupção e horizonte máximo. Se um dos target_lags, target_rolling_window_size ou max_horizon for definido como ' auto ', a heurística será aplicada para estimar o valor do parâmetro correspondente com base nos dados de treinamento.
    + Correção de previsão no caso em que o conjunto de dados contém uma coluna de refinamento, essa granulação é de um tipo numérico e há uma lacuna entre o treinamento e o conjunto de teste
    + Correção da mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão
    + Correção de previsão no caso em que o conjunto de dados contém uma coluna de refinamento, essa granulação é de um tipo numérico e há uma lacuna entre o treinamento e o conjunto de teste.
    + Correção da mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão.
    + Adicionou um Guardrail para verificar se um conjunto de um DataSet está desequilibrado ou não. Se for, uma mensagem Guardrail seria gravada no console.
  + **azureml-core**
    + Foi adicionada a capacidade de recuperar a URL da SAS para o modelo no armazenamento por meio do objeto de modelo. Por exemplo: model.get_sas_url ()
    + Introduzir `run.get_details()['datasets']` para obter conjuntos de valores associados à execução enviada
    + Adicione `Dataset.Tabular.from_json_lines_files` a API para criar um TabularDataset de arquivos de linhas JSON. Para saber mais sobre esses dados tabulares em arquivos de linhas JSON no TabularDataset, visite [Este artigo](how-to-create-register-datasets.md) para obter a documentação.
    + Adição de campos de tamanho de VM adicionais (disco do so, número de GPUs) à função supported_vmsizes ()
    + Adicionados campos adicionais à função list_nodes () para mostrar a execução, o IP privado e o público, a porta, etc.
    + Capacidade de especificar um novo campo durante o provisionamento de cluster--remotelogin_port_public_access que pode ser definido como habilitado ou desabilitado, dependendo se você deseja deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificá-lo, o serviço abrirá de forma inteligente ou fechará a porta dependendo se você estiver implantando o cluster dentro de uma VNet.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Foi adicionada a capacidade de recuperar a URL da SAS para o modelo no armazenamento por meio do objeto de modelo. Por exemplo: modelo. [get_sas_url ()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduza executar. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' DataSets '] para obter conjuntos de valores associados à execução enviada
    + Adicionar API `Dataset.Tabular` .[ from_json_lines_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) para criar um TabularDataset de arquivos de linhas JSON. Para saber mais sobre esses dados tabulares em arquivos de linhas JSON em TabularDataset, visite https://aka.ms/azureml-data a documentação.
    + Adição de campos de tamanho de VM adicionais (disco do so, número de GPUs) à função [supported_vmsizes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Foram adicionados campos adicionais à função [list_nodes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) para mostrar a execução, a privada e o IP público, a porta, etc.
    + Capacidade de especificar um novo campo durante o [provisionamento](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  de cluster que pode ser definido como habilitado ou desabilitado, dependendo se você deseja deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificá-lo, o serviço abrirá de forma inteligente ou fechará a porta dependendo se você estiver implantando o cluster dentro de uma VNet.
  + **azureml-explain-model**
    + Documentação aprimorada para obter as saídas de explicação no cenário de classificação.
    + Adicionada a capacidade de carregar os valores y previstos na explicação para os exemplos de avaliação. O desbloqueia visualizações mais úteis.
    + Adicionada a propriedade de explicador para MimicWrapper para habilitar a obtenção do MimicExplainer subjacente.
  + **azureml-pipeline-core**
    + Adicionado o bloco de anotações para descrever Module, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Adição de RScriptStep para dar suporte ao script R executado via pipeline AML.
    + Os parâmetros de metadados corrigidos analisando em AzureBatchStep que estava causando a mensagem de erro "a atribuição para o parâmetro SubscriptionId não foi especificada".
  + **azureml-Train-automl**
    + Training_data com suporte, validation_data, label_column_name weight_column_name como formato de entrada de dados
    + Mensagem de substituição adicionada para explain_model () e retrieve_model_explanations ()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Adicionado um [bloco de anotações](https://aka.ms/pl-modulestep) para descrever o [módulo](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion e [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + Adição de [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) para dar suporte ao script R executado via pipeline AML.
    + Parâmetros de metadados corrigidos analisando em [AzureBatchStep que estava causando a mensagem de erro "a atribuição para o parâmetro SubscriptionId não foi especificada".
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Training_data com suporte, validation_data, label_column_name weight_column_name como formato de entrada de dados.
    + Mensagem de substituição adicionada para [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK para Python v 1.0.62

+ **Novos recursos**
  + Introduziu a `timeseries`  característica em TabularDataset. Essa característica permite uma fácil filtragem de carimbo de data/hora nos dados de uma TabularDataset, como a obtenção de todos os dados entre um intervalo de tempo ou os dados mais recentes.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb para um bloco de anotações de exemplo.
  + Habilitado o treinamento com TabularDataset e filedataset. 

  + **azureml-train-core**
      + Adição `Nccl` e `Gloo` suporte no estimador de PyTorch

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Preterida a configuração AutoML ' lag_length ' e LaggingTransformer.
    + Correção correta da validação de dados de entrada se elas forem especificadas em um formato de Dataflow
    + Modificou o fit_pipeline. py para gerar o grafo JSON e carregar em artefatos.
    + Renderizado o grafo em `userrun` usando `Cytoscape` .
  + **azureml-core**
    + Revisitoumos o tratamento de exceção no código ADB e fazemos alterações de acordo com o tratamento de erro por nova
    + Autenticação MSI automática adicionada para VMs do notebook.
    + Corrige o bug em que modelos corrompidos ou vazios podem ser carregados devido a tentativas com falha.
    + Corrigido o bug em que `DataReference` o nome é alterado quando o `DataReference` modo é alterado (por exemplo, ao chamar `as_upload` , `as_download` , ou `as_mount` ).
    + Make `mount_point` e `target_path` optional para `FileDataset.mount` e `FileDataset.download` .
    + A exceção de que a coluna de carimbo de data/hora não pode ser encontrada será lançada se a API relacionada à hora de série for chamada sem a coluna de carimbo de data/hora correta atribuída ou as colunas de carimbo de data/hora atribuída
    + As colunas de série de tempo devem ser atribuídas com a coluna cujo tipo é Date, caso contrário a exceção é esperada
    + As colunas de série de tempo atribuindo a API ' with_timestamp_columns ' pode não receber nenhum valor/nome de coluna de carimbo de data/hora grande, o que limpará as colunas de carimbo de data/hora atribuídas
    + A exceção será gerada quando a coluna de carimbo de data/hora de alta granularidade ou refinada for descartada com a indicação para o usuário que o descarte pode ser feito após a exclusão da coluna de carimbo de data/hora na lista suspensa ou chamada with_time_stamp com nenhum valor para liberar colunas timestamp
    + A exceção será gerada quando a coluna de carimbo de data/hora de alta granularidade ou refinada não estiver incluída na lista manter colunas com a indicação para o usuário que pode ser realizada após a inclusão da coluna timestamp na lista manter coluna ou chamar with_time_stamp com nenhum valor para liberar colunas timestamp.
    + Adicionado log para o tamanho de um modelo registrado.
  + **azureml-explain-model**
    + Correção de aviso impresso no console quando o pacote do Python "empacotamento" não está instalado: "usando a versão mais antiga do que com suporte do lightgbm, atualize para a versão superior à 2.2.1"
    + Corrigida a explicação do modelo de download com a fragmentação para obter explicações globais com muitos recursos
    + O explicador de imitação corrigida não contém exemplos de inicialização na explicação de saída
    + Corrigido o erro imutável nas propriedades definidas ao carregar com o cliente de explicação usando dois tipos diferentes de modelos
    + Adição de um parâmetro de get_raw ao explicador de pontuação. explique () para que um explicador de Pontuação possa retornar valores de engenharia e brutos.
  + **azureml-Train-automl**
    + Introduziu as APIs públicas do AutoML para obter explicações de suporte de `automl` explicar o SDK – uma maneira mais recente de dar suporte a explicações de AutoML ao desacoplar AutoML personalização e explicar o suporte à explicação bruta integrada ao SDK do azureml explicar SDK para modelos de AutoML.
    + Removendo o azureml-padrões de ambientes de treinamento remoto.
    + Local do repositório de cache padrão alterado de FileCacheStore com base em um para AzureFileCacheStore um para AutoML no caminho de código Azure Databricks.
    + Correção correta da validação de dados de entrada se elas forem especificadas em um formato de Dataflow
  + **azureml-train-core**
    + Revertida source_directory_data_store substituição.
    + Foi adicionada a capacidade de substituir as versões de pacote instaladas do azureml.
    + Adicionado suporte a dockerfile no `environment_definition` parâmetro em estimadores.
    + Parâmetros de treinamento distribuídos simplificados em estimadores.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (versão prévia) para espaços de trabalho Azure Machine Learning
A nova experiência na Web permite que cientistas de dados e engenheiros de dados concluam seu ciclo de vida de aprendizado de máquina de ponta a ponta de preparando e visualizando dados para treinar e implantar modelos em um único local.

![Interface do usuário do Azure Machine Learning Workspace (versão prévia)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Principais recursos:**

Usando essa nova interface de Azure Machine Learning, agora você pode:
+ Gerencie seus notebooks ou conecte-se ao Jupyter
+ [Executar experimentos de ML automatizados](tutorial-first-experiment-automated-ml.md)
+ [Criar conjuntos de itens de arquivos locais, repositórios de armazenamento & arquivos da Web](how-to-create-register-datasets.md)
+ Explorar & preparar conjuntos de valores para a criação do modelo
+ Monitorar descompasso de dados para seus modelos
+ Exibir os recursos recentes de um painel

No momento desta versão, há suporte para os seguintes navegadores: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Problemas conhecidos:**

1. Atualize seu navegador se você vir "algo deu errado! Erro ao carregar arquivos de parte "quando a implantação está em andamento.

1. Não é possível excluir ou renomear o arquivo em arquivos e blocos de anotações. Durante a visualização pública, você pode usar a interface do usuário do Jupyter ou terminal na VM do notebook para executar operações de atualização de arquivo. Como é um sistema de arquivos de rede montado, todas as alterações, que você faz na VM do notebook, são refletidas imediatamente no espaço de trabalho do bloco de anotações.

1. Para SSH na VM do bloco de anotações:
   1. Localize as chaves SSH que foram criadas durante a configuração da VM. Ou localize as chaves no espaço de trabalho Azure Machine Learning > guia abrir computação > localizar a VM do bloco de anotações na lista > abrir suas propriedades: copie as chaves da caixa de diálogo.
   1. Importe as chaves SSH públicas e privadas para o computador local.
   1. Use-os para SSH na VM do notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK para Python v 1.0.60

+ **Novos recursos**
  + Introduziu filedataset, que faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Os arquivos podem ser de qualquer formato. O filedataset fornece a capacidade de baixar ou montar os arquivos em sua computação. 
  + Adicionado suporte de YAML de pipeline para a etapa PythonScript, etapa adla, databricks etapa, DataTransferStep e etapa AzureBatch

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + AutoArima agora é um pipeline sugerido para visualização apenas.
    + Relatório de erros aprimorado para previsão.
    + Melhorou o registro em log usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
    + Foi removido o check-in max_concurrent_iterations ser menor que o número total de iterações.
    + Os modelos AutoML agora retornam AutoMLExceptions
    + Esta versão melhora o desempenho de execução das execuções locais do Machine Learning automatizado.
  + **azureml-core**
    + Introduza Dataset.get_all (Workspace), que retorna um dicionário de `TabularDataset` e `FileDataset` objetos com chave pelo nome de registro.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduza `parition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files` . As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. ' {column_name} ' cria uma coluna de cadeia de caracteres e ' {column_name: YYYY/MM/DD/HH/mm/ss} ' cria a coluna DateTime, onde ' yyyy ', ' MM ', ' dd ', ' HH ', ' mm ' e ' ss ' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo DateTime. O partition_format deve começar da posição da primeira chave de partição até o final do caminho do arquivo. Por exemplo, considerando o caminho '.. /USA/2019/01/01/data.csv ', em que a partição é por país e hora, partition_format = '/{Country}/{PartitionDate: YYYY/MM/DD}/data.csv ' cria a coluna de cadeia de caracteres ' Country ' com o valor ' EUA ' e a coluna DateTime ' PartitionDate ' com o valor ' 2019-01-01 '.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduza `partition_format` como argumento para `Dataset.Tabular.from_delimited_files` e `Dataset.Tabular.from_parquet.files` . As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. ' {column_name} ' cria uma coluna de cadeia de caracteres e ' {column_name: YYYY/MM/DD/HH/mm/ss} ' cria a coluna DateTime, onde ' yyyy ', ' MM ', ' dd ', ' HH ', ' mm ' e ' ss ' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo DateTime. O partition_format deve começar da posição da primeira chave de partição até o final do caminho do arquivo. Por exemplo, considerando o caminho '.. /USA/2019/01/01/data.csv ', em que a partição é por país e hora, partition_format = '/{Country}/{PartitionDate: YYYY/MM/DD}/data.csv ' cria a coluna de cadeia de caracteres ' Country ' com o valor ' EUA ' e a coluna DateTime ' PartitionDate ' com o valor ' 2019-01-01 '.
    + `to_csv_files` e foram `to_parquet_files` adicionados métodos ao `TabularDataset` . Esses métodos habilitam a conversão entre um `TabularDataset` e um `FileDataset` convertendo os dados em arquivos do formato especificado.
    + Faça logon automaticamente no registro da imagem base ao salvar um Dockerfile gerado por Model. Package ().
    + ' gpu_support ' não é mais necessário; O AML agora detecta e usa automaticamente a extensão do Docker NVIDIA quando ela está disponível. Ela será removida em uma versão futura.
    + Adicionado suporte para criar, atualizar e usar PipelineDrafts.
    + Esta versão melhora o desempenho de execução das execuções locais do Machine Learning automatizado.
    + Os usuários podem consultar métricas do histórico de execuções por nome.
    + Melhorou o registro em log usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
  + **azureml-explain-model**
    + Adicionado feature_maps parâmetro ao novo MimicWrapper, permitindo que os usuários obtenham explicações de recursos brutos.
    + Os carregamentos de conjunto de ativos agora estão desativados por padrão para o upload de explicação e podem ser habilitados novamente com upload_datasets = true
    + Os parâmetros de filtragem "is_law" foram adicionados à lista de explicação e às funções de download.
    + Adiciona `get_raw_explanation(feature_maps)` o método a objetos de explicação global e local.
    + Adicionada a verificação de versão para lightgbm com aviso impresso, se abaixo da versão com suporte
    + Uso otimizado de memória no envio em lote de explicações
    + Os modelos AutoML agora retornam AutoMLExceptions
  + **azureml-pipeline-core**
    + Suporte adicionado para criar, atualizar e usar PipelineDrafts-pode ser usado para manter definições de pipeline mutáveis e usá-las interativamente para executar
  + **azureml-Train-automl**
    + Foi criado um recurso para instalar versões específicas de pytorch v 1.1.0, :::no-loc text="cuda"::: toolkit 9,0, pytorch-transformadores, que é necessário para habilitar o Bert/XLNet no ambiente de tempo de execução do Python remoto.
  + **azureml-train-core**
    + Falha antecipada de alguns erros de definição de espaço de hiperparâmetro diretamente no SDK, em vez de no lado do servidor.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>SDK v 1.1.14 do Azure Machine Learning data Prep
+ **Correções de bugs e melhorias**
  + Habilitado gravação em ADLS/ADLSGen2 usando caminho e credenciais brutos.
  + Corrigido um bug que causou o `include_path=True` trabalho de não funcionar para o `read_parquet` .
  + Correção da `to_pandas_dataframe()` falha causada pela exceção "valor da propriedade inválido: hostSecret".
  + Correção de um bug em que os arquivos não puderam ser lidos em DBFS no modo Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK para Python v 1.0.57
+ **Novos recursos**
  + Habilitado `TabularDataset` para ser consumido pelo AutomatedML. Para saber mais sobre o `TabularDataset` , visite https://aka.ms/azureml/howto/createdatasets .

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Agora você pode atualizar o certificado TLS/SSL para o ponto de extremidade de Pontuação implantado no cluster AKS para o Microsoft generated e o certificado do cliente.
  + **azureml-automl-core**
    + Correção de um problema em AutoML eram linhas com rótulos ausentes não foram removidas corretamente.
    + Aprimoramento do log de erros no AutoML; as mensagens de erro completas agora serão sempre gravadas no arquivo de log.
    + O AutoML atualizou sua fixação de pacote para incluir `azureml-defaults` , `azureml-explain-model` e `azureml-dataprep` . O AutoML não avisará mais sobre incompatibilidades de pacotes (exceto o `azureml-train-automl` pacote).
    + Corrigido um problema em `timeseries`  que as divisões de CV são de tamanho diferente, causando a falha do cálculo de compartimento.
    + Ao executar a iteração de Ensemble para o tipo de treinamento de validação cruzada, se acabarmos tendo problemas para baixar os modelos treinados em todo o conjunto de ativos, estamos tendo uma inconsistência entre os pesos do modelo e os modelos que estavam sendo inseridos no Ensemble de votação.
    + O erro foi corrigido, gerado quando os rótulos de treinamento e/ou de validação (y e y_valid) são fornecidos na forma de dataframe do pandas, mas não como matriz numpy.
    + Corrigido o problema com as tarefas de previsão quando nenhuma foi encontrada nas colunas booleanas das tabelas de entrada.
    + Permitir que os usuários do AutoML descartam a série de treinamento que não são longas o suficiente ao prever. -Permitir que usuários do AutoML removam granulares do conjunto de teste que não existe no conjunto de treinamento ao prever.
  + **azureml-core**
    + Correção do problema com blob_cache_timeout ordenação de parâmetros.
    + Adicionados tipos de exceção de ajuste externo e de transformação a erros do sistema.
    + Suporte adicionado para segredos de Key Vault para execuções remotas. Adicione uma classe azureml. Core. keyvault. keyvault para adicionar, obter e listar segredos do keyvault associado ao seu espaço de trabalho. As operações com suporte são:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.keyvault.Keyvault.set_secret (nome, valor)
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret (nome)
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets ()
    + Métodos adicionais para obter o keyvault padrão e obter segredos durante a execução remota:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.run.Run.get_secret (nome)
      + azureml.core.run.Run.get_secrets (secrets_list)
    + Foram adicionados parâmetros adicionais de substituição para o comando de CLI enviar-hyperdrive.
    + Melhorar a confiabilidade das chamadas de API, expandindo repetições para exceções de biblioteca de solicitações comuns.
    + Adicione suporte para envio de execuções de uma execução enviada.
    + Corrigido o problema de token SAS de expiração no arquivo de monitoramento, que fez com que os arquivos pararam de ser carregados depois que seu token inicial tivesse expirado.
    + Suporte à importação de arquivos HTTP CSV/TSV no SDK do Python do conjunto de
    + O método Workspace. Setup () foi substituído. A mensagem de aviso mostrada aos usuários sugere o uso de Create () ou Get ()/from_config () em vez disso.
    + Adição de Environment.add_private_pip_wheel (), que permite carregar pacotes python personalizados privados `whl` no espaço de trabalho e usá-los com segurança para criar/materializar o ambiente.
    + Agora você pode atualizar o certificado TLS/SSL para o ponto de extremidade de Pontuação implantado no cluster AKS para o Microsoft generated e o certificado do cliente.
  + **azureml-explain-model**
    + Parâmetro adicionado para adicionar uma ID de modelo a explicações no carregamento.
    + `is_raw`Marcação adicionada a explicações na memória e no carregamento.
    + Adicionado suporte e testes do pytorch para o pacote azureml-explique-Model.
  + **azureml-opendatasets**
    + Suporte para detectar e registrar em log o ambiente de teste automático.
    + Classes adicionadas para nos colocar população por município e zip.
  + **azureml-pipeline-core**
    + Adicionada a propriedade de rótulo às definições de porta de entrada e saída.
  + **azureml-telemetry**
    + Correção de configuração de telemetria incorreta.
  + **azureml-Train-automl**
    + Correção do bug em que ocorreu uma falha na instalação, o erro não estava sendo registrado no campo "erros" para a execução da instalação e, portanto, não foi armazenado em "erros" da execução do pai.
    + Correção de um problema em AutoML eram linhas com rótulos ausentes não foram removidas corretamente.
    + Permitir que os usuários do AutoML descartam a série de treinamento que não são longas o suficiente ao prever.
    + Permitir que os usuários do AutoML removam granulares do conjunto de teste que não existe no conjunto de treinamento ao prever.
    + Agora, o AutoMLStep passa pela `automl` configuração para o back-end para evitar problemas de alterações ou adições de novos parâmetros de configuração.
    + O AutoML data Guardrail agora está em visualização pública. O usuário verá um relatório de guardrail de dados (para tarefas de classificação/regressão) após o treinamento e também poderá acessá-lo por meio da API do SDK.
  + **azureml-train-core**
    + Adicionado suporte do Torch 1,2 no estimador do PyTorch.
  + **azureml-widgets**
    + Gráficos de matriz de confusão aprimorados para treinamento de classificação.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>SDK v 1.1.12 do Azure Machine Learning data Prep
+ **Novos recursos**
  + Listas de cadeias de caracteres agora podem ser passadas como entrada para `read_*` métodos.

+ **Correções de bugs e melhorias**
  + O desempenho do `read_parquet` foi aprimorado durante a execução no Spark.
  + Corrigido um problema em que `column_type_builder` houve falha no caso de uma única coluna com formatos de data ambíguos.

### <a name="azure-portal"></a>Portal do Azure
+ **Recurso de visualização**
  + O streaming do arquivo de log e saída agora está disponível para páginas de detalhes de execução. Os arquivos transmitirão atualizações em tempo real quando a alternância de visualização estiver ativada.
  + A capacidade de definir a cota em um nível de espaço de trabalho é liberada na versão prévia. As cotas de AmlCompute são alocadas no nível da assinatura, mas agora podemos distribuir essa cota entre espaços de trabalho e alocá-la para compartilhamento e governança justas. Basta clicar na folha **usos + cotas** na barra de navegação à esquerda do espaço de trabalho e selecionar a guia **configurar cotas** . Você deve ser um administrador de assinatura para poder definir cotas no nível do espaço de trabalho, pois essa é uma operação entre espaços de trabalho.

## <a name="2019-08-05"></a>05/08/2019

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK para Python v 1.0.55

+ **Novos recursos**
  + A autenticação baseada em token agora tem suporte para as chamadas feitas ao ponto de extremidade de Pontuação implantado em AKS. Continuaremos a dar suporte à autenticação baseada em chave atual e os usuários podem usar um desses mecanismos de autenticação de cada vez.
  + Capacidade de registrar um armazenamento de BLOBs que está atrás da rede virtual (VNet) como um repositório de armazenamento.

+ **Correções de bugs e melhorias**
  + **azureml-automl-core**
    + Corrige um bug em que o tamanho de validação para as divisões de CV é pequeno e resulta em gráficos de previsão inválidas versus reais para regressão e previsões.
    + O registro em log de tarefas de previsão nas execuções remotas é melhorado. agora, o usuário receberá uma mensagem de erro abrangente se a execução tiver falhado.
    + Correção de falhas de `Timeseries` se o sinalizador de pré-processamento for verdadeiro.
    + Foram feitas algumas mensagens de erro de validação de dados de previsão mais acionáveis.
    + Redução do consumo de memória das execuções de AutoML ao descartar e/ou carregar o carregamento lento de conjuntos de valores, especialmente entre as execuções de processo
  + **azureml-contrib-explicação-modelo**
    + Foi adicionado model_task sinalizador aos explicadores para permitir que o usuário substitua a lógica de inferência automática padrão para o tipo de modelo
    + Alterações do widget: o instala automaticamente com o `contrib` , não mais `nbextension` explicação de instalação/habilitação-suporte com importância de recurso global (por exemplo, permutadamente)
    + Alterações no painel: plotagens de caixa e gráficos violino além de `beeswarm` plotar na página de resumo-reprocessamento muito mais rápido de `beeswarm` plotagem no controle deslizante ' top-k '-mensagem útil explicando como as primeiras k são mensagens personalizáveis úteis computadas no lugar de gráficos quando os dados não são fornecidos
  + **azureml-core**
    + Adicionado o método Model. Package () para criar imagens do Docker e Dockerfiles que encapsulam modelos e suas dependências.
    + Os WebServices locais foram atualizados para aceitar o InferenceConfigs que contém objetos de ambiente.
    + Corrigido o modelo. Register () produzindo modelos inválidos quando '. ' (para o diretório atual) é passado como o parâmetro model_path.
    + Adicionar Run.submit_child, a funcionalidade espelha o experimento. Submit ao especificar a execução como o pai da execução filho enviada.
    + Suporte a opções de configuração do modelo. Registre-se no Run.register_model.
    + Capacidade de executar trabalhos JAR no cluster existente.
    + Agora oferecendo suporte a parâmetros instance_pool_id e cluster_log_dbfs_path.
    + Adicionado suporte para usar um objeto de ambiente ao implantar um modelo em um WebService. O objeto de ambiente agora pode ser fornecido como parte do objeto InferenceConfig.
    + Adicionar mapeamento de appinsifht para novas regiões-centralus-oesteus-northcentralus
    + Adicionada documentação para todos os atributos em todas as classes de armazenamento de Datastore.
    + Adicionado blob_cache_timeout parâmetro a `Datastore.register_azure_blob_container` .
    + Foram adicionados save_to_directory e métodos load_from_directory ao azureml. Core. Environment. Environment.
    + Foram adicionados os comandos "AZ ml Environment download" e "AZ ml Environment Register" à CLI.
    + Método Environment.add_private_pip_wheel adicionado.
  + **azureml-explain-model**
    + Adicionado controle de conjunto de alterações a explicações usando o serviço de conjunto de serviços (versão prévia).
    + Tamanho de lote padrão reduzido ao transmitir explicações globais de 10K para 100.
    + Foi adicionado model_task sinalizador aos explicadores para permitir que o usuário substitua a lógica de inferência automática padrão para o tipo de modelo.
  + **azureml-mlflow**
    + Corrigido o bug em mlflow.azureml.build_image em que os diretórios aninhados são ignorados.
  + **azureml-pipeline-steps**
    + Foi adicionada a capacidade de executar trabalhos JAR no cluster Azure Databricks existente.
    + Adicionado instance_pool_id de suporte e parâmetros de cluster_log_dbfs_path para a etapa DatabricksStep.
    + Adicionado suporte para parâmetros de pipeline na etapa DatabricksStep.
  + **azureml-Train-automl**
    + Adicionado `docstrings` para os arquivos relacionados ao Ensemble.
    + Documentos atualizados para um idioma mais apropriado para o `max_cores_per_iteration` e o `max_concurrent_iterations`
    + O registro em log de tarefas de previsão nas execuções remotas é melhorado. agora, o usuário receberá uma mensagem de erro abrangente se a execução tiver falhado.
    + Get_data removido do bloco de anotações do pipeline `automlstep` .
    + Iniciado o suporte `dataprep` no `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>SDK v 1.1.10 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Agora você pode solicitar para executar inspetores específicos (por exemplo, histograma, gráfico de dispersão, etc.) em colunas específicas.
  + Adicionado um argumento paralelizate a `append_columns` . Se for true, os dados serão carregados na memória, mas a execução será executada em paralelo; Se for false, a execução será transmitida, mas de thread único.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v 1.0.53

+ **Novos recursos**
  + O Machine Learning automatizado agora dá suporte a modelos de ONNX de treinamento no destino de computação remota
  + Azure Machine Learning agora fornece a capacidade de retomar o treinamento de arquivos de execução, ponto de verificação ou modelo anteriores.
    + Saiba como [usar os estimadores para retomar o treinamento de uma execução anterior](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correções de bugs e melhorias**
  + **azure-cli-ml**
    + Os comandos da CLI "implantação de modelo" e "atualização de serviço" agora aceitam parâmetros, arquivos de configuração ou uma combinação dos dois. Parâmetros têm precedência sobre atributos em arquivos.
    + A descrição do modelo agora pode ser atualizada após o registro
  + **azureml-automl-core**
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar suporte para NimbusML estimados & pipelines a serem usados em estimadores AutoML.
    + Corrigir um bug no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante mesmo se as pontuações permaneceram constantes.
    + Habilite a reutilização de algumas featurizations em divisões de CV para tarefas de previsão. Isso acelera o tempo de execução da configuração executada por aproximadamente um fator de n_cross_validations para featurizations caras, como janelas atrasadas e sem interrupção.
    + Resolver um problema se o tempo estiver fora do intervalo de tempo com suporte do pandas. Agora, geramos uma DataException se o tempo for menor que PD. Timestamp. min ou maior que PD. Timestamp. Max
    + A previsão agora permite diferentes frequências em conjuntos de treinamento e teste se eles puderem ser alinhados. Por exemplo, "começando trimestralmente em Janeiro" e em "a partir de outubro", a cada trimestre, pode ser alinhado.
    + A propriedade "Parameters" foi adicionada ao TimeSeriesTransformer.
    + Remova as classes de exceção antigas.
    + Em tarefas de previsão, o `target_lags` parâmetro agora aceita um único valor inteiro ou uma lista de inteiros. Se o inteiro tiver sido fornecido, apenas uma latência será criada. Se uma lista for fornecida, os valores exclusivos de retardo serão feitos. target_lags = [1, 2, 2, 4] gerará um retardo de um, dois e quatro períodos.
    + Corrigir o bug sobre a perda de tipos de colunas após a transformação (bug vinculado);
    + No `model.forecast(X, y_query)` , permitir que y_query seja um tipo de objeto que contém nenhum (s) no início (#459519).
    + Adicionar valores esperados à `automl` saída
  + **azureml-contrib-descompasso**
    +  Melhorias no notebook de exemplo, incluindo alternar para o azureml-opendatasets em vez de azureml-contrib-opendatasets e melhorias de desempenho ao aprimorar dados
  + **azureml-contrib-explicação-modelo**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Segmentações adicionadas a explicações de imagem no explicador de imagem para o pacote AzureML-contrib-explique-Model
    + Adicionar suporte esparso scipy para LimeExplainer
    + Adicionado `batch_size` ao explicador de imitação quando `include_local=False` , para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correção para chamar set_featurizer_timeseries_params (): tipo de valor de dict Change e verificação nula-Adicionar bloco de anotações para `timeseries`  featurizer
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML
    + Corrigido o bug com o carregamento do repositório de armazenamento em que uma pasta vazia é criada, se `target_path` iniciada com `/`
    + Corrigido o `deepcopy` problema em ServicePrincipalAuthentication.
    + Adicionados os comandos "AZ ml Environment show" e "AZ ml Environment List" à CLI.
    + Os ambientes agora dão suporte à especificação de um base_dockerfile como uma alternativa a um base_image já criado.
    + A configuração RunConfiguration não utilizada auto_prepare_environment foi marcada como preterida.
    + A descrição do modelo agora pode ser atualizada após o registro
    + Bugfix: o modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Correção do bug que imprimiu a duração em branco para implantações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + Exceções de falha aprimoradas para a criação do espaço de trabalho. De modo que os usuários não vejam "não é possível criar o espaço de trabalho. Não é possível localizar... " como a mensagem e, em vez disso, veja a falha de criação real.
    + Adicione suporte para autenticação de token em WebServices AKS.
    + Adicionar `get_token()` método a `Webservice` objetos.
    + Adicionado suporte à CLI para gerenciar os conjuntos de computadores de aprendizado de máquina.
    + `Datastore.register_azure_blob_container` Agora, opcionalmente, usa um `blob_cache_timeout` valor (em segundos) que configura os parâmetros de montagem do blobfuse para habilitar a expiração do cache para esse repositório de armazenamento. O padrão é sem tempo limite, como quando um blob é lido, ele permanecerá no cache local até que o trabalho seja concluído. A maioria dos trabalhos preferirá essa configuração, mas alguns trabalhos precisarão ler mais dados de um grande DataSet do que caberá em seus nós. Para esses trabalhos, o ajuste desse parâmetro irá ajudá-lo a ter sucesso. Tome cuidado ao ajustar esse parâmetro: definir o valor muito baixo pode resultar em baixo desempenho, pois os dados usados em uma época podem expirar antes de serem usados novamente. Todas as leituras serão feitas do armazenamento de BLOBs/rede em vez do cache local, o que afeta negativamente os tempos de treinamento.
    + A descrição do modelo agora pode ser atualizada corretamente após o registro
    + A exclusão de modelo e imagem agora fornece mais informações sobre objetos upstream que dependem deles, o que faz com que a exclusão falhe
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
  + **azureml-explain-model**
    + Argumento de transformações fixas para o explicador de verde-limão para importância de recurso bruto no azureml-contrib-explique-Package
    + Adicionar suporte esparso scipy para LimeExplainer
    + wrapper de explicador linear de forma adicionada, bem como outro nível para explicador tabular para explicar modelos lineares
    + para o explicador de imitação em explicar a biblioteca de modelos, erro fixo quando include_local = false para entrada de dados esparsas
    + Adicionar valores esperados à `automl` saída
    + correção do recurso de permutação fixa quando o argumento de transformações foi fornecido para obter a importância do recurso bruto
    + Adicionado `batch_size` ao explicador de imitação quando `include_local=False` , para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
    + para a biblioteca de explicabilidade de modelo, os explicadores Blackbox fixos em que a entrada do dataframe do pandas é necessária para previsão
    + Correção de um bug em que `explanation.expected_values` às vezes retornaria um float em vez de uma lista com um float.
  + **azureml-mlflow**
    + Melhorar o desempenho de mlflow.set_experiment (experiment_name)
    + Corrigir o bug em uso de InteractiveLoginAuthentication para mlflow tracking_uri
    + Melhorar a utilização de recursos de execuções remotas usando o azureml. mlflow.
    + Melhorar a documentação do pacote azureml-mlflow
    + Bug de patch em que mlflow.log_artifacts ("my_dir") salvaria artefatos em "my_dir/<artefato-Paths>" em vez de "<artefato-Paths>"
  + **azureml-opendatasets**
    + PIN `pyarrow` de `opendatasets` para versões antigas (<0.14.0) devido a um problema de memória introduzido recentemente.
    + Mova o azureml-contrib-opendatasets para o azureml-opendatasets.
    + Permita que as classes Open DataSet sejam registradas no espaço de trabalho Azure Machine Learning e aproveitem as funcionalidades do conjunto de recursos AML diretamente.
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-pipeline-steps**
    + O DBFS datastore agora tem suporte para entradas e saídas em DatabricksStep.
    + Documentação atualizada para a etapa do lote do Azure com relação a entradas/saídas.
    + Em AzureBatchStep, alterado *delete_batch_job_after_finish* valor padrão para *true*.
  + **azureml-telemetry**
    +  Mova o azureml-contrib-opendatasets para o azureml-opendatasets.
    + Permita que as classes Open DataSet sejam registradas no espaço de trabalho Azure Machine Learning e aproveitem as funcionalidades do conjunto de recursos AML diretamente.
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.
  + **azureml-Train-automl**
    + Documentação atualizada em get_output para refletir o tipo de retorno real e fornecer observações adicionais sobre a recuperação de propriedades de chave.
    + Atualize a dependência NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionar valores esperados à `automl` saída
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado
    + A configuração RunConfiguration não utilizada auto_prepare_environment foi marcada como preterida.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>SDK v 1.1.9 do Azure Machine Learning data Prep

+ **Novos recursos**
  + Adicionado suporte para ler um arquivo diretamente de uma URL http ou HTTPS.

+ **Correções de bugs e melhorias**
  + Mensagem de erro aprimorada ao tentar ler um conjunto de parquet de uma fonte remota (que não tem suporte no momento).
  + Corrigido um bug ao gravar no formato de arquivo parquet no ADLS Gen 2 e atualizar o nome do contêiner ADLS Gen 2 no caminho.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface Visual
+ **Versão prévia dos recursos**
  + Módulo "executar script R" adicionado na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v 1.0.48

+ **Novos recursos**
  + **azureml-opendatasets**
    + o **azureml-contrib-opendatasets** agora está disponível como **azureml-opendatasets**. O pacote antigo ainda pode funcionar, mas é recomendável usar o **azureml-opendatasets** avançando para obter recursos e aprimoramentos mais avançados.
    + Esse novo pacote permite que você registre conjuntos de registros abertos como DataSet no espaço de trabalho Azure Machine Learning e aproveite quaisquer funcionalidades oferecidas pelo conjunto de recursos.
    + Ele também inclui recursos existentes, como o consumo de conjuntos de valores abertos como o pandas/SPARK dataframes e junções de local para alguns conjuntos de um conjunto de recursos, como clima.

+ **Versão prévia dos recursos**
    + HyperDriveConfig agora pode aceitar o objeto de pipeline como um parâmetro para dar suporte ao ajuste de hiperparâmetro usando um pipeline.

+ **Correções de bugs e melhorias**
  + **azureml-Train-automl**
    + Corrigido o bug sobre a perda de tipos de colunas após a transformação.
    + Foi corrigido o bug para permitir que y_query seja um tipo de objeto que contenha nenhum (s) no início.
    + Correção do problema no procedimento de seleção de Ensemble que estava aumentando desnecessariamente o Ensemble resultante, mesmo se as pontuações permaneceram constantes.
    + Corrigido o problema com as configurações permitir list_models e bloquear list_models em AutoMLStep.
    + Correção do problema que impedia o uso de pré-processamento quando AutoML teria sido usado no contexto de pipelines do Azure ML.
  + **azureml-opendatasets**
    + Você moveu o azureml-contrib-opendatasets para o azureml-opendatasets.
    + As classes Open DataSet permitidas foram registradas para Azure Machine Learning espaço de trabalho e aproveitam as funcionalidades do conjunto de recursos AML diretamente.
    + Desempenho aprimorado do NoaaIsdWeather enriquecer com uma versão não SPARK significativamente.
  + **azureml-explain-model**
    + Documentação online atualizada para objetos de interpretação.
    + Adicionado `batch_size` ao explicador de imitação quando `include_local=False` , para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel para a biblioteca de explicabilidade de modelo.
    + Correção do problema em que `explanation.expected_values` às vezes retornaria um float em vez de uma lista com um float.
    + Adicionados valores esperados para `automl` saída para o explicador de imitação na biblioteca de modelos de explicação.
    + Correção do recurso de permutação corrigida quando o argumento de transformações foi fornecido para obter a importância do recurso bruto.
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de DBFS na CLI do AzureML.
    + Corrigido o problema com o carregamento do repositório de armazenamento em que uma pasta vazia é criada, se `target_path` iniciada com `/` .
    + Comparação habilitada de dois conjuntos de valores.
    + O modelo e a exclusão de imagem agora fornecem mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Preterida a configuração RunConfiguration não utilizada no auto_prepare_environment.
  + **azureml-mlflow**
    + Melhor utilização de recursos de execuções remotas que usam o azureml. mlflow.
    + A documentação do pacote azureml-mlflow foi aprimorada.
    + Corrigido o problema em que mlflow.log_artifacts ("my_dir") salvaria artefatos em "my_dir/Artifact-Paths" em vez de "artefatos".
  + **azureml-pipeline-core**
    + O parâmetro hash_paths para todas as etapas de pipeline foi preterido e será removido no futuro. Por padrão, o conteúdo do source_directory é com hash (exceto os arquivos listados em `.amlignore` ou `.gitignore` )
    + Melhorando o módulo e o ModuleStep para dar suporte a módulos específicos de tipo de computação, para se preparar para a integração do RunConfiguration e outras alterações para desbloquear o uso de módulo específico de tipo de computação em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: documentação aprimorada com relação a entradas/saídas.
    + AzureBatchStep: alterou delete_batch_job_after_finish valor padrão para true.
  + **azureml-train-core**
    + As cadeias de caracteres agora são aceitas como destino de computação para ajuste de hiperparâmetro automatizado.
    + Preterida a configuração RunConfiguration não utilizada no auto_prepare_environment.
    + Parâmetros preteridos `conda_dependencies_file_path` e `pip_requirements_file_path` em favor de `conda_dependencies_file` e, `pip_requirements_file` respectivamente.
  + **azureml-opendatasets**
    + Melhore significativamente o desempenho do NoaaIsdWeather enriquecer na versão que não seja do SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v 1.0.33 lançado.

+ O Modelos de Aceleração de Hardware do Azure ML no [FPGAs](how-to-deploy-fpga-web-service.md) está disponível para o público em geral.
  + Agora você pode [usar o pacote azureml-da aceleração extra-Models](how-to-deploy-fpga-web-service.md) para:
    + Treinar os pesos de uma rede neural profunda com suporte (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usar o aprendizado de transferência com o DNN com suporte
    + Registrar o modelo com o Serviço Gerenciamento de Modelos e colocar o modelo em contêiner
    + Implantar o modelo em uma VM do Azure com um FPGA em um cluster do AKS (serviço kubernetes do Azure)
  + Implantar o contêiner em um dispositivo de servidor [Azure data Box Edge](../databox-online/azure-stack-edge-overview.md)
  + Pontuar seus dados com o ponto de extremidade gRPC com este [exemplo](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Aprendizado de máquina automatizado

+ Limpeza de recursos para permitir a adição dinâmica :::no-loc text="featurizers"::: para otimização de desempenho. Novo :::no-loc text="featurizers"::: : incorporações de trabalho, peso de evidência, codificações de destino, codificação de destino de texto, distância do cluster
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

+ **Acompanhamento de referência do git**<br/> Os clientes estão solicitando recursos básicos de integração do git por algum tempo, pois ele ajuda a manter uma trilha de auditoria completa. Implementamos o rastreamento em entidades principais no Azure ML para metadados relacionados ao git (repositório, confirmação, estado de limpeza). Essas informações serão coletadas automaticamente pelo SDK e pela CLI.

+ **Criação de perfil do modelo & serviço de validação**<br/> Os clientes freqüentemente reclamam da dificuldade de dimensionar corretamente a computação associada ao seu serviço de inferência. Com nosso serviço de criação de perfil de modelo, o cliente pode fornecer entradas de exemplo e criarei um perfil em 16 diferentes configurações de CPU/memória para determinar o dimensionamento ideal para a implantação.

+ **Traga sua própria imagem base para inferência**<br/> Outra reclamação comum foi a dificuldade de migrar da experimentação para a inferência recompartilhamento de dependências. Com nosso novo recurso de compartilhamento de imagem base, agora você pode reutilizar suas imagens base de experimentação, dependências e todas, para inferência. Isso deve acelerar as implantações e reduzir a lacuna do loop interno para o exterior.

+ **Experiência de geração de esquema do Swagger aprimorada**<br/> Nosso método de geração Swagger anterior era propenso a erros e impossível de ser automatizado. Temos uma nova maneira embutida de gerar esquemas do Swagger de qualquer função do Python por meio de decoradores. Nós abrimos esse código e nosso protocolo de geração de esquema não está acoplado à plataforma ML do Azure.

+ **O CLI do Azure ML está geralmente disponível (GA)**<br/> Os modelos agora podem ser implantados com um único comando da CLI. Temos comentários comuns do cliente que ninguém implanta um modelo de ML de um notebook Jupyter. A [**documentação de referência da CLI**](./reference-azure-machine-learning-cli.md) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v 1.0.30 lançado.

O [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?preserve-view=true&view=azure-ml-py) foi introduzido para adicionar uma nova versão de um pipeline publicado enquanto mantém o mesmo ponto de extremidade.

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
  + Azure Machine Learning os estimadores DNN agora fornecem suporte interno a várias versões. Por exemplo, `TensorFlow` o estimador agora aceita um `framework_version` parâmetro e os usuários podem especificar a versão ' 1,10 ' ou ' 1,12 '. Para obter uma lista das versões com suporte na versão atual do SDK, chame `get_supported_versions()` na classe da estrutura desejada (por exemplo, `TensorFlow.get_supported_versions()` ).
  Para obter uma lista das versões com suporte da versão mais recente do SDK, consulte a [documentação do estimador do DNN](/python/api/azureml-train-core/azureml.train.dnn?preserve-view=true&view=azure-ml-py).

## <a name="2019-03-25"></a>25-03-2019

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v 1.0.21

+ **Novos recursos**
  + O método *azureml.Core.Run.create_children* permite a criação de baixa latência de várias execuções filho com uma única chamada.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v 1.0.18

 + **Alterações**
   + O pacote azureml-tensorboard substitui o azureml-contrib-tensorboard.
   + Com esta versão, você pode configurar uma conta de usuário em seu amlcompute (cluster de computação gerenciada), ao criá-la. Isso pode ser feito passando essas propriedades na configuração de provisionamento. Você pode encontrar mais detalhes na [documentação de referência do SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

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
  + Azure Machine Learning agora fornece suporte de primeira classe para o DNN Framework popular. Usar [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py) usuários de classe pode facilmente treinar e implantar modelos de encadeamento.
    + Saiba como [executar o treinamento distribuído com o ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Saiba como [executar o ajuste de hiperparâmetro com o encadeamento usando hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines adicionou a capacidade de disparar uma execução de pipeline com base nas modificações do repositório de armazenamento. O [notebook de agendamento](https://aka.ms/pl-schedule) de pipeline é atualizado para demonstrar esse recurso.

+ **Correções de bugs e melhorias**
  + Adicionamos suporte em pipelines de Azure Machine Learning para definir a propriedade source_directory_data_store para um armazenamento de um repositório desejado (como um armazenamento de BLOBs) no [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?preserve-view=true&view=azure-ml-py) que são fornecidos para o [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py). Por padrão, as etapas usam o repositório de arquivos do Azure como o repositório de armazenamento de backup, que pode ter problemas de limitação quando um grande número de etapas é executado simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Nova experiência de editor de tabela de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna do bem para a área de tabela em que uma visualização da tabela será exibida. As colunas podem ser reorganizadas.
  + Novo Visualizador de arquivos de logs
  + Links para execuções de teste, computação, modelos, imagens e implantações na guia atividades

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
