---
title: Executar o Linux em nós de computação de máquina virtual
description: Saiba como processar cargas de trabalho de computação paralelas em pools de máquinas virtuais do Linux no lote do Azure.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683693"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Provisionar os nós de computação do Linux em pools do Lote

Você pode usar o Lote do Azure para executar cargas de trabalho de computação paralelas em máquinas virtuais do Linux e do Windows. Este artigo fornece detalhes sobre como criar pools de nós de computação do Linux no serviço do Lote usando as bibliotecas de cliente [Python do Lote](https://pypi.python.org/pypi/azure-batch) e [.NET do Lote](/dotnet/api/microsoft.azure.batch). 

## <a name="virtual-machine-configuration"></a>Configuração de Máquina Virtual

Ao criar um pool de nós de computação no Lote, você tem duas opções das quais pode selecionar o tamanho do nó e o sistema operacional: Configuração de Serviços de Nuvem e Configuração de Máquina Virtual. Os pools de [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) são compostos de VMs do Azure, que podem ser criadas a partir de imagens do Linux ou do Windows. Ao criar um pool com a configuração de máquina virtual, você especifica um [tamanho de nó de computação disponível](../virtual-machines/sizes.md), a referência de imagem de máquina virtual a ser instalada nos nós e o SKU do agente de nó de lote (um programa que é executado em cada nó e fornece uma interface entre o nó e o serviço de lote).

### <a name="virtual-machine-image-reference"></a>Referência da imagem da máquina virtual

O serviço de Lote usa [Conjuntos de Dimensionamento de Máquinas Virtuais](../virtual-machine-scale-sets/overview.md) para fornecer nós de computação na Configuração de máquina virtual. Você pode especificar uma imagem do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)ou [usar a Galeria de imagens compartilhadas para preparar uma imagem personalizada](batch-sig-images.md).

Ao criar uma referência de imagem de máquina virtual, você deve especificar as seguintes propriedades:

| **Propriedade de referência de imagem** | **Exemplo** |
| --- | --- |
| Publicador |Canônico |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Versão |mais recente |

> [!TIP]
> Você pode saber mais sobre essas propriedades e como especificar imagens do Marketplace em [Localizar imagens de VM do Linux no Azure Marketplace com o CLI do Azure](../virtual-machines/linux/cli-ps-findimage.md). Observe que algumas imagens do Marketplace não são atualmente compatíveis com o lote.

### <a name="list-of-virtual-machine-images"></a>imagens da Lista de máquinas virtuais

Nem todas as imagens do Marketplace são compatíveis com os agentes de nó do lote disponíveis no momento. Para listar todas as imagens de máquina virtual do Marketplace com suporte para o serviço de lote e suas SKUs de agente de nó correspondentes, use [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (.net do lote) ou a API correspondente em outro SDK de linguagem.

### <a name="node-agent-sku"></a>SKU do agente do nó

O [agente de nó de lote](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) é um programa executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote. Há diferentes implementações do agente do nó, conhecido como SKUs, para diferentes sistemas operacionais. Essencialmente, ao criar uma Configuração de Máquina Virtual, você primeiro especifica a referência de imagem de máquina virtual e depois especifica o agente do nó a instalar na imagem. Normalmente, cada SKU do agente do nó é compatível com várias imagens de máquina virtual. Aqui estão alguns exemplos de SKUs do agente do nó:

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Crie um pool do Linux: Python no Lote

O snippet de código a seguir mostra um exemplo de como usar a [Biblioteca de cliente do Lote do Microsoft Azure para Python](https://pypi.python.org/pypi/azure-batch) para criar um pool de nós de computação do Ubuntu Server. Para obter mais detalhes sobre o módulo python do lote, veja a [documentação de referência](/python/api/overview/azure/batch).

Esse snippet de código cria explicitamente um [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) e especifica cada uma das propriedades (publicador, oferta, SKU, versão). No código de produção, no entanto, recomendamos que você use o método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para selecionar as combinações de SKU de agente de nó e imagem disponíveis em tempo de execução.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como mencionado anteriormente, é recomendável usar o método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para selecionar dinamicamente das combinações de imagem do agente do nó/Marketplace com suporte no momento (em vez de criar um [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explicitamente). O snippet de código Python a seguir mostra como usar esse método.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Crie um pool do Linux: .NET no Lote

O snippet de código a seguir mostra um exemplo de como usar a biblioteca de cliente [.NET do Lote](https://www.nuget.org/packages/Microsoft.Azure.Batch/) para criar um pool de nós de computação do Ubuntu Server. Para obter mais detalhes sobre o .NET do lote, consulte a [documentação de referência](/dotnet/api/microsoft.azure.batch).

O trecho de código a seguir usa o método [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para selecionar na lista de combinações de SKU de agente de nó e imagem do Marketplace com suporte no momento. Essa técnica é recomendada, pois a lista de combinações com suporte pode mudar de tempos em tempos. Os mais comum é que combinações com suporte sejam adicionadas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Embora o trecho anterior use o método [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para listar e selecionar dinamicamente as combinações de SKU do agente de imagem e de nó com suporte (recomendado), você também pode configurar um [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Conectar-se a nós do Linux usando SSH

Durante o desenvolvimento ou durante a solução de problemas, talvez seja necessário entrar nos nós em seu pool. Diferentemente dos nós de computação do Windows, você não pode usar protocolo RDP (RDP) para se conectar a nós do Linux. Em vez disso, o serviço do Lote habilita acesso do SSH em cada nó para a conexão remota.

O snippet de código Python a seguir cria um usuário em cada nó em um pool, requerido para conexão remota. Ele imprime as informações de conexão SSH (secure shell) para cada nó.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Esse código terá uma saída semelhante ao exemplo a seguir. Nesse caso, o pool contém quatro nós do Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma senha, você pode especificar uma chave pública SSH ao criar um usuário em um nó. No SDK do Python, use o parâmetro **ssh_public_key** em [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). No .NET, use a propriedade [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) .

## <a name="pricing"></a>Preços

O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. O serviço de Lote em si é oferecido sem custos, o que significa que você é cobrado apenas pelos recursos de computação (e os custos associados envolvidos) que as soluções do Lote consomem. Ao escolher a **Configuração da Máquina Virtual**, você é cobrado com base na estrutura de [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

Se você implantar aplicativos para os nós do Lote usando [pacotes de aplicativos](batch-application-packages.md), você também será cobrado pelos recursos de Armazenamento do Azure que consomem seus pacotes de aplicativos.

## <a name="next-steps"></a>Próximas etapas

- Explore os [exemplos de código do Python](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) no [repositório GitHub Azure-batch-Samples](https://github.com/Azure/azure-batch-samples) para ver como executar operações comuns em lote, como pool, trabalho e criação de tarefas. O [LEIAME](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) que acompanha os exemplos do Python apresenta detalhes sobre como instalar os pacotes necessários.
- Saiba mais sobre como usar [VMs de baixa prioridade](batch-low-pri-vms.md) com o lote.
