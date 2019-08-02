---
title: Cargas de trabalho de contêiner - Lote do Azure | Microsoft Docs
description: Saiba como executar aplicativos de imagens de contêiner no Lote do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: edf4ce2be451672ecbd4f732c3110617dc122ca0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323597"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicativos de contêiner no Lote do Azure

O Lote do Azure permite executar e dimensionar um grande número trabalhos de computação em lote no Azure. Tarefas em lote podem executar diretamente em máquinas virtuais (nós) em um pool do Lote, mas também é possível configurar um pool do Lote para executar tarefas em contêineres compatíveis com Docker nos nós. Este artigo mostra como criar um pool de nós de computação que dão suporte a tarefas de contêiner em execução e, em seguida, executar tarefas de contêiner no pool. 

Você deve estar familiarizado com os conceitos de contêiner e como criar um pool do Lote e um trabalho. Os exemplos de código usam SDKs para Python e .NET do Lote. Também é possível usar outras ferramentas e SDKs do Lote, incluindo o portal do Azure para criar pools do Lote habilitados para o contêiner e para executar tarefas de contêiner.

## <a name="why-use-containers"></a>Por que usar contêineres?

O uso de contêineres fornece uma maneira fácil para executar tarefas do Lote sem a necessidade de gerenciar um ambiente e as dependências para executar aplicativos. Os contêineres implantam aplicativos como unidades leves, portáteis e autossuficientes que podem ser executadas em vários ambientes diferentes. Por exemplo, crie e teste um contêiner localmente e, em seguida, carregue a imagem do contêiner em um registro no Azure ou em outro local. O modelo de implantação do contêiner garante que o ambiente de tempo de execução do aplicativo sempre seja instalado e configurado corretamente, independente de onde você hospeda o aplicativo. As tarefas baseadas em contêiner no Lote também podem aproveitar os recursos de tarefas que não são de contêiner, incluindo pacotes de aplicativos e o gerenciamento de arquivos de recurso e arquivos de saída. 

## <a name="prerequisites"></a>Pré-requisitos

* **Versão do SDK**: Os SDKs do Lote começam a dar suporte a imagens de contêiner nas seguintes versões:
    * API REST do Lote versão 2017-09-01.6.0
    * SDK do .NET para o Lote versão 8.0.0
    * SDK do Python para o Lote versão 4.0
    * SDK Java para o Lote versão 3.0
    * SDK Node. js para o Lote versão 3.0

* **Contas**: Em sua assinatura do Azure, você precisa criar uma conta do Lote e, opcionalmente, uma conta do Armazenamento do Microsoft Azure.

* **Uma imagem de máquina virtual com suporte**: Os contêineres só têm suporte em pools criados com a configuração de maquina virtual a partir de imagens detalhadas na seção a seguir, "Imagens de máquina virtual com suporte". Se você fornecer uma imagem personalizada, consulte as considerações na seção a seguir e os requisitos em [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md). 

### <a name="limitations"></a>Limitações

* O Lote dá suporte a RDMA apenas para contêineres em execução em pools Linux

* Para cargas de trabalho de contêiner do Windows, é recomendável escolher um tamanho de VM com vários núcleos para o pool

## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual com suporte

Use uma das imagens a seguir com suporte do Windows ou Linux para criar um conjunto de nós de computação da VM para cargas de trabalho de contêiner. Para obter mais informações sobre imagens do Marketplace compatíveis com Lote, consulte [lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho de contêiner do Windows, o Lote atualmente dá suporte para imagem do **Windows Server 2016 Datacenter com Contêineres** no Azure Marketplace. Apenas imagens de contêiner do Docker têm suporte no Windows.

Também é possível criar imagens personalizadas de VMs executando Docker no Windows.

### <a name="linux-images"></a>Imagens do Linux

Para cargas de trabalho de contêiner do Linux, atualmente o Lote dá suporte para as imagens a seguir do Linux publicadas pelo Lote do Microsoft Azure no Azure Marketplace:

* **CentOS para pools de contêiner do Lote do Azure**

* **CentOS (com drivers RDMA) para pools de contêiner do Lote do Azure**

* **Ubuntu Server para pools de contêiner do Lote do Azure**

* **Ubuntu Server (com drivers RDMA) para pools de contêiner do Lote do Azure**

Há suporte para essas imagens apenas para uso em pools do Lote do Azure. Elas apresentam:

* Um tempo de execução do contêiner [Moby](https://github.com/moby/moby) 

* Drivers de GPU NVIDIA pré-instalados, para agilizar a implantação em VMs da série N do Azure

* Sua escolha de imagens com ou sem os drivers RDMA pré-instalados. Esses drivers permitem que os nós do pool acessem a rede RDMA do Azure quando implantados em tamanhos de VM compatíveis com RDMA. 

Também é possível criar imagens personalizadas de VMs executando Docker em uma das distribuições do Linux compatíveis com Lote. Se você optar por fornecer sua própria imagem personalizada do Linux, confira as instruções em [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

Para suporte de Docker em uma imagem personalizada, instale o [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Considerações adicionais para usar uma imagem personalizada do Linux:

* Para aproveitar o desempenho da GPU dos tamanhos da série N do Azure ao usar uma imagem personalizada, pré-instale os drivers da NVIDIA. Além disso, é necessário instalar o Docker Engine Utility para GPUs NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Para acessar a rede RDMA do Azure, use um tamanho de VM compatível com RDMA. Os drivers RDMA necessários são instalados nas imagens CentOS HPC e Ubuntu com suporte pelo Batch. Outras configurações podem ser necessárias para executar cargas de trabalho de MPI. Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pool do Lote](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuração do contêiner para o pool do Lote

Para permitir que um pool do Lote execute cargas de trabalho de contêiner, é necessário especificar as configurações [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) no objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) do pool. (Este artigo fornece links para a referência de API do .NET do Lote. Configurações correspondentes estão na [API do Python](/python/api/azure.batch) do Lote.)

Crie um pool habilitado para contêiner com ou sem imagens de contêiner pré-buscadas, conforme mostrado nos exemplos a seguir. O processo de pull (ou pré-busca) permite que você carregue previamente imagens de contêiner do Hub do Docker ou de outro registro de contêiner na Internet. Para obter o melhor desempenho, use um [Registro de contêiner do Azure](../container-registry/container-registry-intro.md) na mesma região da conta do Lote.

A vantagem da pré-busca de imagens de contêiner é que quando as tarefas começam a ser executadas, elas não precisam esperar que a imagem de contêiner seja baixada. A configuração do contêiner efetua pull de imagens de contêiner para as VMs quando o pool é criado. As tarefas executadas no pool podem fazer referência à lista de imagens de contêiner e a opções de execução do contêiner.


### <a name="pool-without-prefetched-container-images"></a>Pool sem imagens de contêiner de pré-busca

Para configurar um pool habilitado para contêiner sem imagens de contêiner pré-buscadas, defina os objetos `ContainerConfiguration` e `VirtualMachineConfiguration`, conforme mostrado no exemplo do Python. Este exemplo usa a imagem de pools de contêiner do Ubuntu Server para Lote do Azure do Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Imagens de pré-busca para configuração do contêiner

Para pré-buscar imagens de contêiner no pool, adicione a lista de imagens de contêiner (`container_image_names`, no Python) para `ContainerConfiguration`. 

O exemplo básico do Python a seguir mostra como pré-buscar uma imagem de contêiner padrão do Ubuntu a partir do [Hub do Docker](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


O exemplo C# a seguir pressupõe que você deseja pré-buscar uma imagem do TensorFlow do [Hub do Docker](https://hub.docker.com). Este exemplo inclui uma tarefa inicial que é executada no host de VM em nós do pool. Você pode executar uma tarefa inicial no host, por exemplo, para montar um servidor de arquivos que pode ser acessado por meio dos contêineres.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Imagens de pré-busca de um registro de contêiner privado

Você também pode executar a pré-busca de imagens de contêiner ao fazer a autenticação em um servidor de registro de contêiner privado. No exemplo a seguir, os objetos `ContainerConfiguration` e `VirtualMachineConfiguration` pré-buscam uma imagem privada do TensorFlow de um registro de contêiner do Azure privado. A referência da imagem é a mesma do exemplo anterior.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Configurações de contêiner para a tarefa

Para executar uma tarefa de contêiner em um pool habilitado para contêiner, especifique as configurações específicas do contêiner. As configurações incluem a imagem a ser usada, o registro e as opções de execução do contêiner.

* Use a propriedade `ContainerSettings` das classes de tarefa para definir configurações específicas ao contêiner. Essas configurações são definidas pela classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

* Se você executar tarefas em imagens de contêiner, a [tarefa nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e a [tarefa do gerenciador de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigirão configurações de contêiner. No entanto, [iniciar tarefa](/dotnet/api/microsoft.azure.batch.starttask), [tarefa de preparação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) e [tarefa de liberação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não exigem configurações de contêiner (ou seja, podem ser executados em um contexto de contêiner ou diretamente no nó).

### <a name="container-task-command-line"></a>Linha de Comando da Tarefa do Contêiner

Quando você faz a execução de uma tarefa de contêiner, o Batch usa automaticamente o comando [docker create](https://docs.docker.com/engine/reference/commandline/create/) para criar um contêiner usando a imagem especificada na tarefa. Em seguida, o lote controla a execução da tarefa no contêiner. 

Assim como ocorre com as tarefas em lotes que não são contêineres, você define uma linha de comando para uma tarefa de contêiner. Como o Batch cria automaticamente o contêiner, a linha de comando apenas especifica o comando ou comandos que serão executados no contêiner.

Se a imagem de contêiner para uma tarefa em lote estiver configurada com um script [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example), você poderá definir sua linha de comando para usar o ENTRYPOINT padrão ou substituí-la: 

* Para usar o ENTRYPOINT padrão da imagem de contêiner, configure a linha de comandos da tarefa para a sequência vazia `""`.

* Para substituir o ENTRYPOINT padrão ou se a imagem não tiver um ENTRYPOINT, defina uma linha de comando apropriada para o contêiner, por exemplo, `/app/myapp` ou `/bin/sh -c python myscript.py`.

As [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) opcionais são argumentos adicionais fornecidos ao comando `docker create` que o Batch usa para criar e executar o contêiner. Por exemplo, para definir um diretório de trabalho para o contêiner, defina a opção `--workdir <directory>`. Veja a referência [docker create](https://docs.docker.com/engine/reference/commandline/create/) para opções adicionais.

### <a name="container-task-working-directory"></a>Diretório de trabalho da tarefa do contêiner

Uma tarefa de contêiner em lote é executada em um diretório de trabalho no contêiner que é muito semelhante ao diretório que o Batch configura para uma tarefa regular (não contêiner). Observe que esse diretório de trabalho é diferente do [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir), se configurado na imagem, ou do diretório de trabalho do contêiner padrão (`C:\` em um contêiner do Windows ou `/` em um contêiner do Linux). 

Uma tarefa de lote contêiner:

* Todos os diretórios recursivamente abaixo do `AZ_BATCH_NODE_ROOT_DIR` no nó do host (a raiz dos diretórios do Lote do Microsoft Azure) são mapeados para o contêiner
* Todas as variáveis de ambiente de tarefas são mapeadas no contêiner
* O diretório de trabalho da tarefa `AZ_BATCH_TASK_WORKING_DIR` no nó é definido da mesma forma que para uma tarefa regular e mapeado para o contêiner. 

Esses mapeamentos permitem trabalhar com tarefas de contêiner da mesma maneira que as tarefas que não são contêineres. Por exemplo, instale aplicativos usando pacotes de aplicativos, acesse arquivos de recursos do Armazenamento do Microsoft Azure, use configurações de ambiente de tarefas e persista arquivos de saída de tarefas depois que o contêiner for interrompido.

### <a name="troubleshoot-container-tasks"></a>Solucionar problemas de tarefas de contêiner

Se a tarefa do contêiner não for executada conforme o esperado, talvez seja necessário obter informações sobre a configuração WORKDIR ou ENTRYPOINT da imagem de contêiner. Para ver a configuração, execute o comando [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/). 

Se necessário, ajuste as configurações da tarefa do contêiner com base na imagem:

* Especifique um caminho absoluto na linha de comando da tarefa. Se o ENTRYPOINT padrão da imagem for usado para a linha de comando da tarefa, assegure-se de que um caminho absoluto esteja configurado.

* Nas opções de execução do contêiner da tarefa, altere o diretório de trabalho para corresponder ao WORKDIR na imagem. Por exemplo, definir `--workdir /app`.

## <a name="container-task-examples"></a>Exemplos de tarefas de contêiner

O fragmento Python a seguir mostra uma linha de comando básica em execução em um contêiner criado a partir de uma imagem fictícia extraída do Docker Hub. Aqui, a opção de contêiner `--rm` remove o contêiner após a conclusão da tarefa, e a opção `--workdir` define um diretório de trabalho. A linha de comando substitui o contêiner ENTRYPOINT por um comando shell simples que grava um arquivo pequeno no diretório de trabalho da tarefa no host. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

O exemplo C# a seguir mostra as configurações básicas do contêiner para uma tarefa de nuvem:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Próximas etapas

* Consulte também o kit de ferramentas [Batch Shipyard](https://github.com/Azure/batch-shipyard) para facilitar a implantação de cargas de trabalho do contêiner no Lote do Azure através de [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Para saber mais sobre como instalar e usar o Docker CE no Linux, confira a documentação do [Docker](https://docs.docker.com/engine/installation/).

* Para obter mais informações sobre o uso de imagens personalizadas, consulte [Use uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

* Saiba mais sobre o [projeto Moby](https://mobyproject.org/), uma estrutura para criar sistemas baseados em contêiner.