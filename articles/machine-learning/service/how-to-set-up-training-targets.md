---
title: Criar e usar destinos de computação para treinamento de modelo
titleSuffix: Azure Machine Learning service
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo de machine learning. É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente. Se precisar expandir, passe para um destino de computação em nuvem.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9c97f23c2dfc2b1c0ff794aa20ffb58cd8b8741a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819342"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurar destinos de computação para treinamento de modelo

Com o Serviço do Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente denominados [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-target). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para a implantação de modelo conforme descrito em ["em que local e como implantar seus modelos"](how-to-deploy-and-where.md).

É possível criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, o portal do Azure ou a CLI do Azure. Se você tiver destinos de computação criados por meio de outro serviço (por exemplo, um cluster HDInsight), poderá usá-los anexando-os ao workspace de serviço do Azure Machine Learning.
 
Neste artigo, você aprende a usar vários destinos de computação para treinamento do modelo.  As etapas para todos os destinos de computação seguem o mesmo fluxo de trabalho:
1. Escolha __Criar__ um destino de computação se você ainda não tiver um.
2. Escolha __Anexar__ o destino de computação a seu workspace.
3. Escolha __Configurar__ o destino de computação para que ele contenha as dependências de pacote e ambiente Python exigidas pelo seu script.


>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.6 do SDK do Azure Machine Learning.

## <a name="compute-targets-for-training"></a>Destinos de computação para treinamento

O Serviço do Azure Machine Learning tem suporte variado nos diferentes destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou faz treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:


|Destino de computação para treinamento| Aceleração de GPU | Automatizado<br/> gráfico de ajuste de hiperparâmetro | Automatizado</br> aprendizado de máquina | Pipelines do Azure Machine Learning |
|----|:----:|:----:|:----:|:----:|
|[Computador local](#local)| Talvez | &nbsp; | ✓ | &nbsp; |
|[Computação do Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[VM remota](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Análise Azure Data Lake](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Lote do Azure](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**Todos os destinos de computação podem ser reutilizados para vários trabalhos de treinamento**. Por exemplo, após anexar uma VM remota ao seu workspace, você pode reutilizá-la para vários trabalhos.

> [!NOTE]
> A Computação do Azure Machine Learning pode ser criada como um recurso persistente ou criada dinamicamente quando você solicita uma execução. A criação baseada em execução remove o destino de computação após a conclusão da execução de treinamento, portanto, não é possível reutilizar os destinos de computação criados dessa maneira.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Durante o treinamento, é comum iniciar em seu computador local e depois executar esse script de treinamento em um destino de computação diferentes. Com o Serviço do Azure Machine Learning, você pode executar seu script em vários destinos de computação sem precisar alterar seu script. 

Tudo que você precisa fazer é definir o ambiente para cada destino de computação com uma **configuração de execução**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para esse tipo de computação. 

Saiba mais sobre [enviar experimentos](#submit) no final deste artigo.

### <a name="manage-environment-and-dependencies"></a>Gerenciar o ambiente e as dependências

Quando você cria uma configuração de execução, precisa decidir como gerenciar o ambiente e as dependências no destino de computação. 

#### <a name="system-managed-environment"></a>Ambiente gerenciado pelo sistema

Use um ambiente gerenciado pelo sistema quando quiser que o [Conda](https://conda.io/docs/) gerencie o ambiente do Python e as dependências de script para você. Um ambiente gerenciado pelo sistema será presumido por padrão e é a escolha mais comum. É útil em destinos de computação remota, especialmente quando não é possível configurar o destino. 

Tudo o que você precisa fazer é especificar cada dependência de pacote usando a [classe CondaDependency](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py). Em seguida, o Conda cria um arquivo chamado **conda_dependencies** no diretório **aml_config** em seu workspace com sua lista de dependências do pacote e conjuntos de backup de seu ambiente do Python quando você envia seu teste de treinamento. 

A configuração inicial de um novo ambiente pode levar vários minutos, dependendo do tamanho das dependências necessárias. Desde que a lista de pacotes permaneça inalterada, o tempo de configuração ocorrerá apenas uma vez.
  
O código a seguir mostra um exemplo de um ambiente gerenciado pelo sistema que exige scikit-learn:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Ambiente gerenciado por usuário

Para ambientes gerenciados pelo usuário, você é responsável por configurar o ambiente e por instalar todos os pacotes de que seu script de treinamento precisa no destino de computação. Se o ambiente de treinamento já estiver configurado (como ocorreria em seu computador local), você poderá ignorar a etapa de configuração definindo `user_managed_dependencies` como True. O Conda não verificará seu ambiente nem instalará nada para você.

O código a seguir está um exemplo de configuração de execuções de treinamento para um ambiente gerenciado pelo usuário:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Configurar destinos de computação com o Python

Use as seções a seguir para configurar estes destinos de computação:

* [Computador local](#local)
* [Computação do Azure Machine Learning](#amlcompute)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Computador local

1. **Criar e anexar**: Não é necessário criar nem anexar um destino de computação para usar seu computador local como o ambiente de treinamento.  

1. **Configurar**:  Quando você usa seu computador local como um destino de computação, o código de treinamento é executado em seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes do Python de que você precisa, use o ambiente gerenciado pelo usuário.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="amlcompute"></a>Computação do Azure Machine Learning

A Computação do Machine Learning do Azure é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente computação de único nó a vários nós. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma Rede Virtual do Azure. A computação é executada em um ambiente em contêineres, empacotando as dependências do modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

É possível usar a Computação do Azure Machine Learning para distribuir o processo de treinamento em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


É possível criar o ambiente de computação do Azure Machine Learning sob demanda ao programar uma execução, ou como um recurso persistente.

#### <a name="run-based-creation"></a>Criação baseada em execução

É possível criar a Computação do Azure Machine Learning como um destino de computação em tempo de execução. A computação é criada automaticamente para a sua execução. A computação é excluída automaticamente depois de a execução ser concluída. 

> [!NOTE]
> Para especificar o número máximo de nós a serem usados, você normalmente definiria `node_count` para o número de nós. Atualmente, não há (04/04/2019) um bug que impede que esse trabalho. Como alternativa, use o `amlcompute._cluster_max_node_count` propriedade da configuração de execução. Por exemplo, `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> A criação baseada em execução da computação do Azure Machine Learning atualmente está em Versão Prévia. Não use a criação baseada em execução se você estiver usando o ajuste de hiperparâmetro ou o aprendizado de máquina automatizado. Para usar o ajuste de hiperparâmetro ou aprendizado de máquina automatizados, crie um destino de [computação persistente](#persistent) em vez disso.

1.  **Criar, anexar e configurar**: a criação com base em execução gera todas as etapas necessárias para criar, anexar e configurar o destino de computação com a configuração de execução.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

#### <a id="persistent"></a>Computação persistente

Uma Computação do Azure Machine Learning pode ser reutilizada em trabalhos. Ele pode ser compartilhado com outros usuários no workspace e é mantido entre trabalhos.

1. **Criar e anexar**: Para criar um recurso persistente de Computação do Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. A computação é reduzida automaticamente até zero nó quando não é usada.   VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
    * **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
    * **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.  Veja [AmlCompute classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter detalhes.
    
   Outra opção é criar e anexar a um recurso persistente de Computação do Azure Machine Learning [no portal do Azure](#portal-create).

1. **Configurar**: Crie uma configuração de execução para o destino de computação persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo Serviço do Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.

Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

Use a DSVM (Máquina Virtual de Ciência de Dados) do Azure como a VM do Azure de escolha para esse cenário. Essa VM é uma ciência de dados pré-configuradas e o ambiente de desenvolvimento de inteligência artificial do Azure. A VM oferece uma opção selecionada de ferramentas e estruturas para desenvolvimento do aprendizado de máquina de todo o ciclo de vida. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Criar**: Crie uma DSVM antes de usá-la para treinar seu modelo. Para criar esse recurso, veja [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > O Azure Machine Learning dá suporte apenas a máquinas virtuais que executam o Ubuntu. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer o nome de domínio totalmente qualificado (FQDN), o nome de logon e a senha da máquina virtual. No exemplo, substitua \<fqdn> com o FQDN público do VM ou o endereço IP público. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar a DSVM ao seu workspace [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
    
    Depois que o cluster é criado, conecte-o com o nome do host \<clustername >-ssh.azurehdinsight.net, em que \<clustername> é o nome que você forneceu para o cluster. 

1. **Anexar**: Para anexar a um cluster do HDInsight como um destino de computação, forneça o nome do host, o nome de usuário e a senha para o cluster HDInsight. O exemplo a seguir usa o SDK para anexar um cluster ao seu workspace. No exemplo, substitua \<clustername> pelo nome do seu cluster. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha do cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar o cluster HDInsight ao workspace [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="azbatch"></a>Lote do Azure 

O lote do Azure é usado para executar aplicativos em larga escala paralelas e de alto desempenho HPC (computação) com eficiência na nuvem. AzureBatchStep pode ser usado em um Pipeline de aprendizado de máquina do Azure para enviar trabalhos para um pool de lote do Azure de máquinas.

Para anexar o lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e forneça as seguintes informações:

-   **Nome de computação do lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-   **Nome de conta do lote do Azure**: O nome da conta do lote do Azure
-   **Grupo de recursos**: O grupo de recursos que contém a conta do lote do Azure.

O código a seguir demonstra como anexar o lote do Azure como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Configurar computação no portal do Azure

Você pode acessar quais destinos de computação são associados com seu workspace no portal do Azure.  Você pode usar o portal para:

* [Exibir destinos de computação](#portal-view) anexado ao seu workspace
* [Criar um destino de computação](#portal-create) no workspace
* [Anexar um destino de computação](#portal-reuse) que foi criado fora do workspace

Depois que um destino é criado e anexado ao seu workspace, você o usará em sua configuração de execução com um objeto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Exibir destinos de computação


Para ver os destinos de computação do seu workspace, use as seguintes etapas:

1. Visite o [portal do Azure](https://portal.azure.com) e navegue até o seu workspace. 
1. Em __Aplicativos__, selecione __Computação__.

    ![Guia Exibir computação](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Criar um destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Insira um nome para o destino de computação. 

1. Selecione **Computação do Machine Learning** como o tipo da computação a ser usada para __Treinamento__. 

    >[!NOTE]
    >A Computação do Azure Machine Learning é o único recurso de computação gerenciado que você pode criar no portal do Azure.  Todos os outros recursos de computação podem ser anexados depois de serem criados.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente **Família de VMs**e o **máximo de nós** utilizado para acelerar a computação.  

    ![Preencha o formulário](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Selecione __Criar__.


1. Exiba o status da operação de criação, selecionando o destino de computação na lista:

    ![Selecione um destino de computação para exibir o status da operação criar](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, visualize os detalhes do destino de computação: 

    ![Exibir os detalhes do destino de computação](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Anexar destinos de computação

Para usar destinos de computação criados fora do workspace do Serviço do Azure Machine Learning, é necessário anexá-los. Anexar um destino de computação o disponibiliza para seu workspace.

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use as etapas abaixo para anexar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação. 
1. Insira um nome para o destino de computação. 
1. Selecione o tipo de computação a ser anexado para __Treinamento__:

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados usando o portal do Azure. Os tipos de computação que podem ser anexados para treinamento são:
    >
    > * VM remota
    > * Azure Databricks (para uso em pipelines de aprendizado de máquina)
    > * Azure Data Lake Analytics (para uso em pipelines de aprendizado de máquina)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que as senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH contam com as assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecionar __Anexar__. 
1. Exiba o status da operação de anexação, selecionando o destino de computação na lista.

## <a name="set-up-compute-with-the-cli"></a>Configurar a computação com a CLI

Você pode acessar os destinos de computação associados ao seu workspace usando a [extensão da CLI](reference-azure-machine-learning-cli.md) para o Serviço do Azure Machine Learning.  Você pode usar a CLI para:

* Criar um destino de computação gerenciado
* Atualizar um destino de computação gerenciado
* Anexar um destino de computação não gerenciado

Para obter mais informações, veja [Gerenciamento de recursos](reference-azure-machine-learning-cli.md#resource-management).

## <a id="submit"></a>Enviar execução de treinamento

Depois de criar uma configuração de execução, você pode usá-la para executar seu teste.  O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Enviar a execução.
1. Aguarde a conclusão da execução.

> [!IMPORTANT]
> Quando você envia a execução de treinamento, um instantâneo do diretório que contém seus scripts de treinamento é criado e enviado para o destino de computação. Ele também é armazenado como parte do experimento no seu espaço de trabalho. Se você alterar os arquivos e enviar a execução novamente, apenas os arquivos alterados serão carregados.
>
> Para impedir que arquivos sejam incluídos no instantâneo, crie uma [. gitignore](https://git-scm.com/docs/gitignore) ou `.amlignore` arquivo no diretório e adicione os arquivos para ele. O `.amlignore` arquivo usa a mesma sintaxe e padrões como o [. gitignore](https://git-scm.com/docs/gitignore) arquivo. Se os dois arquivos existem, o `.amlignore` arquivo terá precedência.
> 
> Para obter mais informações, veja [cópias de sombra](concept-azure-machine-learning-architecture.md#snapshot).

### <a name="create-an-experiment"></a>Criar uma experiência

Primeiro, crie um experimento no seu workspace.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Enviar o teste

Envie o experimento com um objeto `ScriptRunConfig`.  Esse objeto inclui:

* **source_directory**: o diretório de origem que contém o script de treinamento
* **script**: identificar o script de treinamento
* **run_config**: a configuração de execução que, por sua vez, define em que local ocorrerá o treinamento.

Por exemplo, para usar a configuração de [destino local](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Alterne o mesmo experimento para ser executado em um destino de computação diferente usando uma configuração de execução diferente, como [destino amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Ou você pode:

* Enviar o teste com um objeto `Estimator` conforme mostrado em [Treinar modelos de ML com estimadores](how-to-train-ml-models.md). 
* Enviar um experimento [usando a extensão da CLI](reference-azure-machine-learning-cli.md#experiments).

## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SKD da [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usar o Serviço do Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
