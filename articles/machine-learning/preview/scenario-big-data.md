---
title: "Previsão de carga de trabalho de servidor em dados de terabytes – Azure | Microsoft Docs"
description: "Como treinar um modelo de aprendizado de máquina sobre Big Data usando o Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: b76253fad43be231591023c4d4466bf6e3f329a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="server-workload-forecasting-on-terabytes-data"></a>Previsão de carga de trabalho de servidor em dados de terabytes

Este exemplo ilustra como os cientistas de dados podem usar o Azure ML Workbench para desenvolver soluções que exigem o uso de Big Data. Mostramos como um usuário, usando o Azure ML Workbench, pode seguir um caminho feliz de começar de um exemplo de grande conjunto de dados, iterar por meio da preparação de dados, engenharia de recursos e aprendizado de máquina e estender eventualmente o processo para todo o grande conjunto de dados. 

Ao longo do caminho, mostramos os principais recursos a seguir do Azure ML Workbench:
* Fácil comutação entre os destinos de computação: mostramos como o usuário pode configurar diferentes destinos de computação e usá-los em experimentação. Neste exemplo, usamos um DSVM Ubuntu e um cluster do HDInsight como os destinos de computação. Também mostramos ao usuário como configurar os destinos de computação dependendo da disponibilidade dos recursos. Particularmente, após expandir o cluster Spark (ou seja, incluindo mais nós de trabalho no cluster Spark), como o usuário pode usar os recursos por meio do Azure ML Workbench para acelerar execuções de experimento.
* Acompanhamento do histórico de execuções: mostramos ao usuário como o Azure ML Workbench pode ser usado para acompanhar o desempenho de modelos ML e outras métricas de interesse.
* Operacionalização do modelo de aprendizado de máquina: mostramos o uso das ferramentas internas no Azure ML Worbench para implantar o modelo ML treinado como serviço Web no ACS (Serviço de Contêiner do Azure). Também mostramos como usar o serviço Web para obter previsões de minilote por meio de chamadas à API REST. 
* Suporte a dados de terabytes.



## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

O repositório público do GitHub para este exemplo contém todos os materiais, incluindo os exemplos de código: 
 
[https://github.com/Azure/MachineLearningSamples-BigData](https://github.com/Azure/MachineLearningSamples-BigData)



## <a name="use-case-overview"></a>Visão geral de casos de uso


Prever a carga de trabalho nos servidores é uma necessidade de negócios comum para empresas de tecnologia que gerenciam suas próprias infraestruturas. Para reduzir o custo de infraestrutura, os serviços em execução em servidores subutilizados deve ser agrupados para serem executados em um número menor de computadores e os serviços em execução em servidores sobrecarregados deverão receber mais máquinas para executar. Neste cenário, vamos nos concentrar na previsão de carga de trabalho para cada computador (ou servidor). Em particular, usamos os dados da sessão em cada servidor para prever a classe de carga de trabalho do servidor no futuro. Classificamos o carregamento de cada servidor em classes baixa, média e alta usando o Classificador Random Forest no [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). As técnicas de aprendizado de máquina e o fluxo de trabalho neste exemplo podem ser facilmente estendidas para outros problemas semelhantes. 


## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para executar este exemplo são os seguintes:

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
* Este cenário pressupõe que você esteja executando o Azure ML (Machine Learning) Workbench no Windows 10. Se você estiver usando o macOS, a instrução será basicamente a mesma.
* Uma DSVM (Máquina Virtual de Ciência de Dados) para Linux (Ubuntu). É possível provisionar uma DSVM Ubuntu seguindo as [instruções](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Clique [aqui](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu) para ver o início rápido. Recomendamos usar uma máquina virtual com pelo menos 8 núcleos e 32 GB de memória.  Você precisa do endereço IP da DSVM, nome de usuário e senha para testar este exemplo. Salve a tabela a seguir com as informações da DSVM para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
Endereço IP da DSVM | xxx|
 Nome de usuário  | xxx|
 Senha   | xxx|

 É possível optar por usar qualquer VM (máquina virtual) com o [Mecanismo do Docker](https://docs.docker.com/engine/) instalado.

* Um cluster Spark do HDInsight com HDP versão 3.6 e Spark versão 2.1. x. Acesse [Criar um cluster Apache Spark no Azure HDInsight] (https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) para obter detalhes sobre como criar clusters do HDInsight. É recomendável usar um cluster de três trabalhos com cada trabalho tendo 16 núcleos e 112 GB de memória. Ou é possível escolher apenas o tipo de VM "`D12 V2`" para o nó de cabeçalho e "`D14 V2`" para o nó de trabalho. A implantação do cluster leva cerca de 20 minutos. Você precisa do nome do cluster, nome de usuário do SSH e senha para testar este exemplo. Salve a tabela a seguir com as informações do cluster do Azure HDInsight para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
 Nome do cluster| xxx|
 Nome de usuário  | xxx (por padrão, é sshuser)|
 Senha   | xxx|


* Uma conta de armazenamento do Azure. É possível seguir as [instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta de armazenamento do Azure. Além disso, crie dois contêineres de Blob particulares com o nome "`fullmodel`" e "`onemonthmodel`" nesta conta de armazenamento. A conta de armazenamento é usada para salvar os resultados de computação intermediários e os modelos de aprendizado de máquina. Você precisa do nome da conta de armazenamento e da chave de acesso para testar este exemplo. Salve a tabela a seguir com as informações da conta de armazenamento do Azure para etapas posteriores:

 Nome do campo| Valor |  
 |------------|------|
 Nome da conta de armazenamento| xxx|
 Chave de acesso  | xxx|


A DSVM Ubuntu e o cluster do Azure HDInsight criados na lista de pré-requisitos são destinos de computação. Os destinos de computação são o recurso de computação no contexto do Azure ML Workbench, que pode ser diferente do computador no qual o Azure ML Workbench é executado.   

## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar modelos de projeto**, digite "Previsão da carga de trabalho sobre dados de terabytes" e selecione o modelo
5.  Clique em **Criar**

É possível criar um projeto do Azure ML Workbench com um repositório git pré-criado seguindo esta [instrução](./tutorial-classifying-iris-part-1.md).  
Execute o status do git para inspecionar o status dos arquivos para controle de versão.

## <a name="data-description"></a>Descrição dos dados

Os dados usados no cenário são dados de carga de trabalho de servidor sintetizados e hospedados em uma conta de armazenamento de blobs do Azure acessível publicamente. As informações da conta de armazenamento específicas podem ser encontradas no campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). É possível usar os dados diretamente do armazenamento de blobs do Azure. Caso o armazenamento seja usado por muitos usuários simultaneamente, é possível optar por usar [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) para baixar os dados para seu próprio armazenamento. 

O tamanho total dos dados é de cerca de 1 TB. Cada arquivo tem cerca de 1 a 3 GB e está no formato de arquivo CSV sem cabeçalho. Cada linha de dados representa a carga de uma transação em um servidor específico.  As informações detalhadas do esquema de dados são as seguintes:

Número da coluna | Nome do campo| Tipo | Descrição |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Datetime |    Hora de início da sessão
2  |`SessionEnd`    | Datetime | Hora de término da sessão
3 |`ConcurrentConnectionCounts` | Número inteiro | Número de conexões simultâneas
4 | `MbytesTransferred` | Duplo | Dados normalizados transferidos em megabytes
5 | `ServiceGrade` | Número inteiro |  Nível de serviço da sessão
6 | `HTTP1` | Número inteiro|  se a sessão usa HTTP1 ou HTTP2
7 |`ServerType` | Número inteiro   |Tipo de servidor
8 |`SubService_1_Load` | Duplo |   Carga do subserviço 1
9 | `SubService_1_Load` | Duplo |  Carga do subserviço 2
10 | `SubService_1_Load` | Duplo |     Carga do subserviço 3
11 |`SubService_1_Load` | Duplo |  Carga do subserviço 4
12 | `SubService_1_Load`| Duplo |      Carga do subserviço 5
13 |`SecureBytes_Load`  | Duplo | Carga de bytes seguros
14 |`TotalLoad` | Duplo | Carga total no servidor
15 |`ClientIP` | Cadeia de caracteres|    Endereço IP do cliente
16 |`ServerIP` | Cadeia de caracteres|    Endereço IP do servidor



Observe que embora os tipos de dados esperados sejam listados na tabela anterior, devido aos valores ausentes e problemas de dados sujos, não há garantia de que o tipo de dados seja conforme o esperado e o processamento dos dados deve levar isso em consideração. 


## <a name="scenario-structure"></a>Estrutura do cenário

Os arquivos neste exemplo são organizados da seguinte maneira.

| Nome do Arquivo | Tipo | Descrição |
|-----------|------|-------------|
| `Code` | Pasta | A pasta contém todo o código no exemplo |
| `Config` | Pasta | A pasta contém os arquivos de configuração |
| `Image` | Pasta | A pasta usada para salvar as imagens para o arquivo LEIAME |
| `Model` | Pasta | A pasta usada para salvar arquivos de modelo baixados do armazenamento de Blobs do Azure |
| `Code/etl.py` | Arquivo Python | o arquivo Python usado para a preparação de dados e engenharia de recursos |
| `Code/train.py` | Arquivo Python | O arquivo Python usado para treinar um modelo multiclassificação de três classes  |
| `Code/webservice.py` | Arquivo Python | O arquivo Python usado para operacionalização  |
| `Code/scoring_webservice.py` | Arquivo Python |  O arquivo Python usado para a transformação de dados e para chamar o serviço Web |
| `Code/O16Npreprocessing.py` | Arquivo Python | O arquivo Python usado para pré-processar os dados para scoring_webservice.py.  |
| `Code/util.py` | Arquivo Python | O arquivo Python, que contém o código para ler e gravar blobs do Azure.  
| `Config/storageconfig.json` | Arquivo JSON | O arquivo de configuração para o contêiner de blobs do Azure que armazena os resultados intermediários e o modelo para processar e treinar em dados de um mês |
| `Config/fulldata_storageconfig.json` | Arquivo Json |  O arquivo de configuração para o contêiner de blobs do Azure que armazena os resultados intermediários e o modelo para processar e treinar em conjunto de dados completos|
| `Config/webservice.json` | Arquivo JSON | O arquivo de configuração para scoring_webservice.py|
| `Config/conda_dependencies.yml` | Arquivo YAML | O arquivo de dependência Conda |
| `Config/conda_dependencies_webservice.yml` | Arquivo YAML | O arquivo de dependência Conda para serviço Web|
| `Config/dsvm_spark_dependencies.yml` | Arquivo YAML | o arquivo de dependência Spark para DSVM Ubuntu |
| `Config/hdi_spark_dependencies.yml` | Arquivo YAML | o arquivo de dependência Spark para o cluster Spark do HDInsight |
| `README.md` | Arquivo markdown | O arquivo markdown README |
| `Code/download_model.py` | Arquivo Python | O arquivo Python usado para baixar os arquivos de modelo do Blob do Azure para o disco local |
| `Docs/DownloadModelsFromBlob.md` | Arquivo markdown | O arquivo markdown, que contém as instruções de como executar `Code/download_model.py` |



### <a name="data-flow"></a>Fluxo de dados

O código no [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) carrega dados do contêiner publicamente acessível (campo `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Ele inclui a preparação de dados e a engenharia de recursos e salva os resultados e modelos de computação intermediários em seu próprio contêiner privado. O código no [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) carrega os resultados de computação intermediários do contêiner privado, treina o modelo de classificação multiclasse e finalmente grava o modelo de aprendizado de máquina treinado no contêiner privado. É recomendável que o usuário use um contêiner para experimentação no conjunto de dados de um mês e, em seguida, outro para experimentação no conjunto de dados completo. Como os dados e os modelos são salvos como arquivo Parquet, cada arquivo é realmente uma pasta no contêiner que contém vários blobs. O contêiner resultante tem a seguinte aparência:

| Nome do prefixo do blob | Tipo | Descrição |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modelo do scaler padrão para recursos numéricos |
| stringIndexModel | Parquet | Modelo do indexador de cadeia de caracteres para recursos não numéricos|
| oneHotEncoderModel|Parquet | Modelo do codificador one-hot para recursos categóricos |
| mlModel | Parquet | modelo de aprendizado de máquina treinado |
| informações| Arquivo pickle Python | as informações sobre os dados transformados, incluindo o início do treinamento, término do treinamento, duração, o carimbo de data/hora para a divisão de teste-treinamento e colunas para indexação e codificação one-hot.

Todos os arquivos/blobs na tabela anterior são usados para operacionalização.


### <a name="model-development"></a>Desenvolvimento do modelo

#### <a name="architecture-diagram"></a>Diagrama da arquitetura


O diagrama a seguir mostra o fluxo de trabalho de ponta a ponta do uso do Azure ML Workbench para desenvolver o modelo: ![arquitetura](media/scenario-big-data/architecture.PNG)



A seguir, mostramos o desenvolvimento do modelo usando a funcionalidade de destino de computação remota no Azure ML Workbench. Primeiro, carregamos dados de exemplo pequenos e executamos o script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) em uma DSVM Ubuntu para rápida iteração. É possível limitar ainda mais o trabalho que fazemos no [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) passando um argumento extra para iteração mais rápida. No final, usamos um cluster do HDInsight para treinar usando dados completos.     

O arquivo [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) executa o carregamento dos dados, a preparação de dados e a engenharia de recursos. Ele aceita dois argumentos: (1) o arquivo de configuração para o contêiner de armazenamento de Blobs do Azure para armazenar os resultados e modelos de computação intermediários, (2) depurar configuração para iteração mais rápida.

O primeiro argumento, `configFilename`, é um arquivo de configuração local em que você armazena as informações de armazenamento de Blobs do Azure e especifica para onde carregar os dados. Por padrão, é o [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) e será usado na execução de dados de um mês. Também incluímos o [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), de que você precisa para usar na execução do conjunto de dados completo. O conteúdo na configuração é o seguinte: 

| Campo | Tipo | Descrição |
|-----------|------|-------------|
| storageAccount | Cadeia de caracteres | Nome da conta de armazenamento do Microsoft Azure |
| storageContainer | Cadeia de caracteres | Contêiner na conta de armazenamento do Microsoft Azure para armazenar resultados intermediários |
| storageKey | Cadeia de caracteres |Chave de acesso da conta de armazenamento do Microsoft Azure |
| dataFile|Cadeia de caracteres | Arquivos de fonte de dados  |
| duration| Cadeia de caracteres | duração dos dados nos arquivos de fonte de dados|

Modifique `Config/storageconfig.json` e `Config/fulldata_storageconfig.json` para configurar a conta de armazenamento, a chave de armazenamento e o contêiner de blobs para armazenar os resultados intermediários. Por padrão, o contêiner de blobs para a execução de dados de um mês é "`onemonthmodel`" e o contêiner de blobs para a execução do conjunto de dados completo é "`fullmodel`." Certifique-se de criar esses dois contêineres na conta de armazenamento. O campo `"dataFile"` no [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configura quais dados são carregados no [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) e o `"duration"` configura o intervalo que os dados incluem. Se a duração for definida como 'ONE_MONTH', os dados carregados deverão ser apenas um arquivo csv entre os sete arquivos dos dados para junho de 2016. Se a duração for 'COMPLETA', o conjunto de dados completo, que tem 1 TB, será carregado. Não é necessário alterar `"dataFile"` e `"duration"` nesses dois arquivos de configuração.

O segundo argumento é DEBUG. Defini-lo como 'FILTER_IP' permite uma iteração mais rápida. O uso desse parâmetro é útil quando você deseja depurar o script.

> [!NOTE]
> Substitua qualquer variável de argumento em todos os comandos a seguir por seu valor real.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Desenvolvimento de modelo no Docker da DSVM Ubuntu

#####  <a name="1-setting-up-the-compute-target-for-docker-on-ubuntu-dsvm"></a>1. Configurando o destino de computação para o Docker na DSVM Ubuntu

Inicie a linha de comando no Azure ML Workbench clicando no menu "Arquivo" no canto superior esquerdo do Azure ML Workbench e escolhendo "Abrir prompt de comando", em seguida execute 

```az ml computetarget attach --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password --type remotedocker```

Quando a linha de comando terminar a execução com êxito, você verá os dois arquivos a seguir criados na pasta aml_config do seu projeto:

    dockerdsvm.compute: contains the connection and configuration information for a remote execution target
    dockerdsvm.runconfig: set of run options used when executing within the Azure ML Workbench application

Navegue até dockerdsvm.runconfig e altere a configuração dos seguintes campos, conforme mostrado abaixo:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Prepare o ambiente de projeto executando:

```az ml experiment prepare -c dockerdsvm```


Ao definir "PrepareEnvironment" como true, você permitirá que o Azure ML Workbench crie o ambiente de tempo de execução sempre que enviar um trabalho. `Config/conda_dependencies.yml` e `Config/dsvm_spark_dependencies.yml` contêm a personalização do ambiente de tempo de execução. É sempre possível modificar as dependências Conda, a configuração e as dependências do Spark editando esses dois arquivos YMAL. Neste exemplo, adicionamos `azure-storage` e `azure-ml-api-sdk` como pacotes python extra no `Config/conda_dependencies.yml` e adicionamos "`spark.default.parallelism`","`spark.executor.instances`" e "`spark.executor.cores` etc. no `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Preparação de dados e engenharia de recursos no Docker DSVM

Execute o script `etl.py` no Docker DSVM com o parâmetro de depuração que filtra os dados carregados com endereços IP de servidor específico:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Navegue até o painel lateral, clique em "Executar" para ver o histórico de execuções de `etl.py`. Observe que o tempo de execução é de aproximadamente dois minutos. Se você planejar alterar seu código para incluir novos recursos, fornecer FILTER_IP como o segundo argumento propiciará uma iteração mais rápida. Talvez seja necessário executar esta etapa várias vezes ao lidar com seu próprios problemas de aprendizado de máquina para explorar o conjunto de dados ou criar novos recursos. Com a restrição personalizada sobre quais dados devem ser carregados e a filtragem adicional de quais dados devem ser processados, é possível, portanto, acelerar o processo de iteração em seu desenvolvimento de modelo. Ao testar, você deve salvar periodicamente as alterações em seu código no repositório do git.  Observe que usamos o código a seguir no `etl.py` para permitir o acesso ao contêiner privado:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Em seguida, execute o script `etl.py` no Docker DSVM sem o parâmetro de depuração FILTER_IP

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Navegue até o painel lateral, clique em "Executar" para ver o histórico de execuções de `etl.py`. Observe que o tempo de execução é de cerca de 4 minutos. O resultado processado desta etapa é salvo no contêiner e carregado para treinamento em train.py. Além disso, os indexadores de cadeias de caracteres, os pipelines de codificador e os scalers padrão também são salvos no contêiner privado e usados na operacionalização (O16N). 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Treinamento de modelo no Docker DSVM

Execute o script `train.py` no Docker DSVM:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Esta etapa carrega os resultados de computação intermediários da execução de `etl.py` e treina um modelo de aprendizado de máquina. Esta etapa leva cerca de dois minutos.

Quando você tiver concluído com êxito a experimentação nos dados pequenos, será possível continuar executando a experimentação no conjunto de dados completo. É possível começar usando o mesmo código e experimentar com as alterações no destino de computação e do argumento.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Desenvolvimento do modelo no cluster do HDInsight

##### <a name="1-create-compute-target-in-azure-ml-workbench-for-the-hdinsight-cluster"></a>1. Criar destino de computação no Azure ML workbench para o cluster do HDInsight

```az ml computetarget attach --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password --type cluster```

Quando a linha de comando for concluída com êxito, você verá os dois arquivos a seguir na pasta aml_config:
    
    myhdo.compute: contains connection and configuration information for a remote execution target
    myhdi.runconfig: set of run options used when executing within the Azure ML Workbench application


Navegue até myhdi.runconfig e altere a configuração dos seguintes campos, conforme mostrado abaixo:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Prepare o ambiente de projeto executando:

```az ml experiment prepare -c myhdi```

Esta etapa pode levar até sete minutos.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Preparação de dados e engenharia de recursos no cluster do HDInsight

Execute o script `etl.py` com fulldata no cluster do HDInsight

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Como este trabalho tem uma duração relativamente longa (cerca de duas horas), podemos usar "-a" para desabilitar o fluxo de saída. Quando o trabalho for concluído, no "Histórico de execuções", é possível examinar o log do driver e também o log do controlador. Se você tiver um cluster maior, sempre será possível reconfigurar as configurações no `Config/hdi_spark_dependencies.yml` para usar mais instâncias ou mais núcleos. Por exemplo, se você tiver um cluster de quatro nós de trabalho, será possível aumentar o valor do "`spark.executor.instances`" de 5 para 7. É possível ver a saída dessa etapa no contêiner "fullmodel" em sua conta de armazenamento. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Treinamento de modelo no cluster do HDInsight

Execute o script `train.py` no cluster do HDInsight:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Como este trabalho tem uma duração relativamente longa (cerca de meia hora), usamos "-a" para desabilitar o fluxo de saída.

#### <a name="run-history-exploration"></a>Exploração do histórico de execuções

O histórico de execuções é um recurso que permite o acompanhamento de sua experimentação no Azure ML Workbench. Por padrão, ele controla a duração da experimentação. Em nosso exemplo específico, quando migramos para o conjunto de dados completo para "`Code/etl.py`" na experimentação, observamos que a duração aumenta consideravelmente. Também é possível registrar métricas específicas para fins de acompanhamento. Para habilitar o acompanhamento de métricas, adicione as seguintes linhas de código ao cabeçalho do seu arquivo python:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Veja um exemplo para acompanhar uma métrica específica:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Navegue até "Execuções" na barra lateral direita do Azure ML Workbench para ver o histórico de execuções para cada arquivo python. Além disso, acesse o repositório do github, um novo branch com o nome observando com o “AMLHistory” é criado para acompanhar a alteração realizada em seu script em cada execução. 


### <a name="operationalization"></a>Operacionalização

Nesta seção, operacionalizaremos o modelo criado nas etapas anteriores como serviço Web e demonstremos como é possível usar o serviço Web para prever a carga de trabalho. Usamos CLIs (interfaces de linha de comando) de operacionalização do Azure ML para empacotar o código e as dependências como imagens do Docker e publicar o modelo como serviço Web em contêineres. Consulte [Operationalization Overview](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md) (Visão geral de operacionalização) para obter mais detalhes. É possível usar o prompt de linha de comando no Azure ML Workbench para executar as CLIs de operacionalização do Azure ML.  Também é possível executar as CLIs de operacionalização do Azure ML no Ubuntu Linux seguindo o [guia de instalação](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Substitua qualquer variável de argumento em todos os comandos a seguir por seu valor real. Leva cerca de 40 minutos para concluir esta seção.
> 


Escolha uma cadeia de caracteres exclusiva como o ambiente para operacionalização e usamos a cadeia de caracteres "[exclusiva]" para representar a cadeia de caracteres escolhida.

1. Criar o ambiente para operacionalização e o grupo de recursos.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Observe que optamos por usar o Serviço de Contêiner do Azure como o ambiente usando o `--cluster` no comando `az ml env setup`. Optamos por operacionalizar o modelo de aprendizado de máquina no [Serviço de Contêiner do Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes), pois ele usa o [Kubernetes](https://kubernetes.io/) para automatizar a implantação, dimensionamento e o gerenciamento de aplicativos em contêiner. Esse comando leva cerca de 20 minutos para ser executado. Uso 

        az ml env show -g [unique]rg -n [unique]

   para verificar se a implantação foi concluída com êxito.

   Defina o ambiente de implantação como o que você acabou de criar executando

        az ml env set -g [unique]rg -n [unique]

2. Crie uma conta de gerenciamento de modelos e use-a.

   Crie uma conta de gerenciamento de modelos executando

    az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Use o gerenciamento de modelos para operacionalização executando

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   A conta de gerenciamento de modelos é usada para gerenciar os modelos e os serviços Web. No Portal do Azure, é possível ver que uma nova conta de gerenciamento de modelos é criada e é possível usá-la para ver os modelos, manifestos, imagens do Docker e os serviços criados usando essa conta de gerenciamento de modelos.

3. Baixe e registre os modelos.

   Baixe os modelos no contêiner "fullmodel" para seu computador local no diretório do código. Não baixe o arquivo de dados parquet com o nome "vmlSource.parquet", porque ele não é um arquivo de modelo, mas um resultado de computação intermediário. Também é possível reutilizar os arquivos de modelo que foram incluídos no repositório do git. Acesse [DownloadModelsFromBlob.md](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md) para obter detalhes sobre como baixar os arquivos parquet. 

   Vá para a pasta `Model` na CLI e registre os modelos da seguinte forma:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   A saída de cada comando fornece uma ID de modelo necessária na próxima etapa. Salve-os em um arquivo de texto para uso futuro.

4. Crie um manifesto para o serviço Web.

   O manifesto é a receita usada para criar a imagem do Docker para contêineres de serviço Web. Ele inclui o código para o serviço Web, todos os modelos de aprendizado de máquina e as dependências do ambiente de tempo de execução.  Acesse a pasta `Code` na CLI e execute a linha de comando:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   A saída fornece uma ID de manifesto para a próxima etapa. 

   Permaneça no diretório `Code` e você poderá testar webservice.py executando 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Crie uma imagem do Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   A saída fornece uma ID de imagem para a próxima etapa, pois essa imagem do Docker é usada no ACS. 

6. Implantar o serviço Web no cluster do ACS

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   A saída fornece uma ID de serviço e é necessário usá-la para obter a chave de autorização e a URL do serviço.

7. Chame o serviço Web no código Python para pontuar em minilotes.

   Use o comando a seguir para obter a chave de autorização

         az ml service keys realtime -i $ServiceID 

   e use o seguinte comando para obter a URL de pontuação do serviço

        az ml service usage realtime -i $ServiceID

   Modifique o conteúdo no `./Config/webservice.json` com a URL de pontuação do serviço e a chave de autorização corretas (mantenha o "Portador" no arquivo original e substitua a parte "xxx"). 
   
   Vá para o diretório raiz do seu projeto e teste o serviço Web para a pontuação do minilote usando

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Dimensione o serviço Web. 

   Consulte [How to scale operationalization on your ACS cluster](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md) (Como dimensionar a operacionalização no cluster do ACS) para dimensionar o serviço Web.
 

## <a name="conclusion"></a>Conclusão

Este exemplo destaca como usar o Azure ML Workbench para treinar um modelo de aprendizado de máquina sobre Big Data e operacionalizar o modelo treinado. Em particular, mostramos como:

* Configurar e usar diferentes destinos de computação.

* Executar o histórico de métricas de acompanhamento e diferentes execuções.

* Operacionalização.

Os usuários podem estender o código para explorar a validação cruzada e o ajuste de hiperparâmetro. Para saber mais sobre a validação cruzada e o ajuste de hiperparâmetro, acesse https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning.  
Para saber mais sobre previsão de série temporal, acesse https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting.
