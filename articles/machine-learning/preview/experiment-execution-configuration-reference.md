---
title: "Arquivos de configuração de execução do Azure Machine Learning Workbench"
description: "Este documento detalha as definições de configurações para a execução do experimento do Azure ML Workbench."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: e1356439385cc7fe66985bd2b84e4121386ec23d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench-execution-configuration-files"></a>Arquivos de configuração de execução do Azure Machine Learning Workbench

Ao enviar um script para o Azure Machine Learning (Azure ML) Workbench, o comportamento de execução é controlado pelos arquivos na pasta **aml_config**. Esta pasta está na raiz de pastas do seu projeto. É importante entender o conteúdo desses arquivos para alcançar o resultado desejado para a sua execução de uma maneira ideal.

A seguir estão os arquivos relevantes nesta pasta:
- conda_dependencies.yml
- spark_dependencies.yml
- arquivos de destino de computação
    - \<nome do destino de computação>.compute
- arquivos de configuração de execução
    - \<nome de configuração de execução>. runconfig

>[!NOTE]
>Normalmente, você tem um arquivo de destino de computação e um arquivo de configuração de execução para cada destino de computação criado. No entanto, você pode criar esses arquivos de forma independente e ter vários arquivos de configuração de execução apontando para o mesmo destino de computação.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Esse arquivo é um [arquivo do ambiente conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) que especifica a versão do tempo de execução do Python e pacotes dos quais os seu código depende. Quando o Azure ML Workbench executa um script em um contêiner do Docker ou cluster HDInsight, ele cria um [ambiente conda](https://conda.io/docs/using/envs.html) para que seu script seja executado. 

Nesse arquivo, você deve especificar pacotes do Python de que seu script precisa para execução. O serviço de execução do Azure ML Workbench cria o ambiente conda na imagem do Docker de acordo com sua lista de dependências. A lista de pacotes aqui deve estar acessível pelo mecanismo de execução. Por esse motivo, os pacotes precisam ser listados nos canais, como:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* um ponto de extremidade publicamente acessível (URL)
* ou um caminho de arquivo local
* outros acessíveis pelo mecanismo de execução

>[!NOTE]
>Quando em execução no cluster do HDInsight, o Azure ML Workbench cria um ambiente de conda apenas para a sua execução. Isso permite que diferentes usuários executem em ambientes de Python diferentes no mesmo cluster.  

Aqui está um exemplo de um típico arquivo **conda_dependencies.yml**.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

O Azure ML Workbench usa o mesmo ambiente conda sem recriação, desde que o **conda_dependencies.yml** permaneça intacto. No entanto, se houver alguma alteração no arquivo, ele resultará em recompilação da imagem do Docker.

>[!NOTE]
>Se você direcionar a execução contra contexto de computação _local_, o arquivo **conda_dependencies.yml** **não** será usado. As dependências do pacote para o seu ambiente do Azure ML Workbench Python local precisam ser instaladas manualmente.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Esse arquivo Especifica o nome do aplicativo Spark ao enviar um script PySpark e pacotes Spark que precisam ser instalados. Você também pode especificar qualquer repositório Maven público, bem como o pacote Spark que pode ser encontrado nesses repositórios Maven.

Aqui está um exemplo:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Parâmetros de ajuste de cluster, como o tamanho do trabalho, núcleos devem ir para a seção "configuração" no arquivo spark_dependecies.yml 

>[!NOTE]
>Se você estiver executando o script no ambiente de Python, o arquivo *spark_dependencies.yml* será ignorado. Ele terá efeito apenas se você estiver executando em Spark (seja em Docker ou HDInsight Cluster).

## <a name="run-configuration"></a>Configuração de execução
Para especificar uma configuração de execução específica, é necessário um par de arquivos. Normalmente, eles são gerados usando um comando da CLI. Porém, você também pode clonar os existentes, renomeá-los e editá-los.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> -type cluster
```

Este comando cria um par de arquivos com base no destino de computação especificado. Digamos que você tenha nomeado seu destino de computação como _foo_. Este comando gera _foo.compute_ e _foo.runconfig_ na sua pasta **aml_config**.

>[!NOTE]
> Nomes _local_ ou _docker_ para os arquivos de configuração de execução são arbitrários. O Azure ML Workbench adiciona que essas duas configurações de execução quando você cria um projeto em branco para sua conveniência. Você pode renomear arquivos "<run configuration name>. runconfig" que vêm com o modelo de projeto ou criar novos com qualquer nome que você deseje.

### <a name="compute-target-namecompute"></a>\<nome do destino de computação>.compute
O arquivo _\<compute target name>.compute_ especifica informações de conexão e configuração para o destino de computação. É uma lista de pares nome-valor. A seguir estão as configurações com suporte.

**tipo**: tipo do ambiente de computação. Os valores para os quais há suporte são:
  - local
  - docker
  - remotedocker
  - cluster

**baseDockerImage**: imagem do Docker usada para executar o script Python/PySpark. O valor padrão é _microsoft/mmlspark:plus-0.7.91_. Também há suporte para uma outra imagem: _microsoft/mmlspark:plus-gpu-0.7.91_, que oferece acesso à GPU no computador host (se houver GPU).

**endereço**: o endereço IP ou FQDN (nome de domínio totalmente qualificado) da máquina virtual ou nó principal do cluster do HDInsight.

**nome de usuário**: o nome de usuário SSH para acessar a máquina virtual ou o nó principal do HDInsight.

**senha**: a senha criptografada para a conexão SSH.

**sharedVolumes**: sinalizador para indicar que o mecanismo de execução deve usar o recurso de volume compartilhado do Docker para enviar e receber arquivos de projeto. Ter esse sinalizador ativado pode acelerar a execução, uma vez que o Docker pode acessar projetos diretamente sem a necessidade de copiá-los. É melhor definir _false_ se o mecanismo do Docker estiver em execução no Windows, já que o compartilhamento de volume para Docker no Windows pode ser instável. Defina-a como _true_ se estiver em execução no macOS ou no Linux.

**nvidiaDocker**: esse sinalizador, quando definido como _true_, diz ao serviço de execução do Azure ML Workbench para usar o comando _nvidia-docker_, em vez do comando _docker_ normal, para inicializar a imagem do Docker. O mecanismo _nvidia-docker_ permite que o contêiner do Docker acesse o hardware da GPU.  configuração será necessária se você quiser usar a execução de GPU no contêiner do Docker. Somente o host Linux dá suporte a _nvidia-docker_. Por exemplo, DSVM com base em Linux no Azure é enviada com _nvidia-docker_. O _nvidia-docker_ a partir de agora não tem suporte no Windows.

**nativeSharedDirectory**: essa propriedade especifica o diretório base (por exemplo: _~/.azureml/share/_) em que os arquivos podem ser salvos para serem compartilhados entre execuções no mesmo destino de computação. Se essa configuração for usada durante a execução em um contêiner do Docker, _sharedVolumes_ deverá ser definido como true. Caso contrário, a execução falhará.

### <a name="run-configuration-namerunconfig"></a>\<nome de configuração de execução>. runconfig
_\<run configuration name>.runconfig_ especifica o comportamento de execução do Azure ML Workbench. Você pode configurar os comportamentos de execução como acompanhar o histórico de execução ou qual destino de computação usar junto com muitos outros. Os nomes dos arquivos de configuração de execução são usados para preencher o menu suspenso do contexto de execução no aplicativo de área de trabalho do Azure ML Workbench.

**ArgumentVector**: essa seção especifica o script a ser executado como parte dessa execução e os parâmetros do script. Por exemplo, se você tiver o trecho de código a seguir em seu arquivo "<run configuration name>. runconfig" 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experiment submit foo.runconfig"_ executa automaticamente o comando com o arquivo _myscript.py_ passando 234 como um parâmetro e define o sinalizador --verbose.

**Destino**: este parâmetro é o nome do arquivo _.compute_ a que o arquivo _runconfig_ faz referência. Ele geralmente aponta para o arquivo _foo.compute_, mas você pode editá-lo para que aponte para um destino de computação diferente.

**Variáveis de ambiente**: esta seção permite aos usuários definir variáveis de ambiente como parte de suas execuções. O usuário pode especificar variáveis de ambiente usando pares de nome-valor nos seguintes formatos:
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Essas variáveis de ambiente podem ser acessadas no código do usuário. Por exemplo, esse código phyton imprime a variável de ambiente chamada "EXAMPLE_ENV_VAR"
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Estrutura**: essa propriedade especifica se o Azure ML Workbench deve inicializar uma sessão do Spark para executar o script. O valor padrão é _PySpark_. Defina-o como _Python_ se você não estiver executando o código PySpark, o que pode ajudar a inicializar o trabalho mais rapidamente com menos sobrecarga.

**CondaDependenciesFile**: essa propriedade aponta para o arquivo que especifica as dependências do ambiente conda na pasta *aml_config*. Se definido como _null_, aponta para o arquivo **conda_dependencies.yml** padrão.

**SparkDependenciesFile**: essa propriedade aponta para o arquivo que especifica as dependências do Spark na pasta **aml_config**. Ele é definido como _null_ por padrão e aponta para o arquivo **spark_dependencies.yml** padrão.

**PrepareEnvironment**: essa propriedade, quando definida como _true_, diz ao serviço de execução para preparar o ambiente conda com base em dependências conda especificadas como parte da execução inicial. Essa propriedade é eficaz somente quando você executa em um ambiente de Docker. Essa configuração não terá efeito se você estiver executando em um ambiente _local_. 

**TrackedRun**: esse sinalizador indica ao serviço de execução se ele deve ou não acompanhar a execução na infraestrutura de histórico de execução do Azure ML Workbench. O valor padrão é _true_. 

**UseSampling**: _UseSampling_ especifica se os conjuntos de dados de exemplo ativos para fontes de dados são usados para a execução. Se definido como _false_, fontes de dados vão ingerir e usar os dados completos lidos do armazenamento de dados. Se definido como _true_, exemplos de ativos serão usados. Os usuários podem usar **DataSourceSettings" para especificar quais conjuntos de dados de exemplo específicos usar se quiserem substituir o exemplo ativado. 

**DataSourceSettings**: essa seção de configuração especifica as configurações de fonte de dados. Nesta seção, o usuário especifica qual amostra de dados existente para uma determinada fonte de dados é usada como parte da execução. 

A definição de configuração a seguir especifica que esse exemplo denominado "MySample" é usado para a fonte de dados chamada "MyDataSource"
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: substituições de fonte de dados podem ser usadas quando o usuário deseja mudar de uma fonte de dados para outra sem alterar seu código. Por exemplo, os usuários podem mudar de um arquivo local amostrado para o dataset original maior armazenado no Blob do Azure alterando a referência de fonte de dados. Quando uma substituição for usada, o Azure ML Workbench executa seus pacotes de preparação de dados e fontes de dados referenciando a fonte de dados de substituição.

O exemplo a seguir substitui as referências de "mylocal.datasource" em fontes de dados do Azure ML e pacotes de preparação de dados por "myremote.dsource". 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

Com base na substituição acima, o exemplo de código a seguir agora lê de "myremote.dsource", em vez de "mylocal.dsource", sem que usuários alterem seu código.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [configuração do ambiente de execução](experiment-execution-configuration.md)
