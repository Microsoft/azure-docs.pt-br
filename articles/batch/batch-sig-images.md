---
title: Usar a Galeria de imagens compartilhadas para criar um pool de imagens personalizado
description: Os pools de imagens personalizadas são uma maneira eficiente de configurar nós de computação para executar suas cargas de trabalho do lote.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: a9ff30f52ae19e3d6a7bc58ca81eabeb91d21146
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024057"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Usar a Galeria de imagens compartilhadas para criar um pool de imagens personalizado

Ao criar um pool no Lote do Azure usando a Configuração de Máquina Virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace com suporte ou criar uma imagem personalizada com uma [imagem da Galeria de imagens compartilhada](../virtual-machines/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Benefícios da Galeria de Imagens Compartilhadas

Ao usar a Galeria de Imagens Compartilhadas para sua imagem personalizada, você tem controle sobre a configuração e o tipo do sistema operacional, bem como o tipo de discos de dados. Sua imagem compartilhada pode incluir aplicativos e dados de referência que ficam disponíveis em todos os nós do pool do lote assim que eles são provisionados.

Além disso, você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Ao usar uma versão da imagem para criar uma VM, a versão da imagem é usada para criar novos discos para a VM.

Usar uma imagem compartilhada economiza tempo ao preparar os nós de computação do seu pool para executar sua carga de trabalho em lotes. É possível usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, no entanto, normalmente, é mais eficiente usar uma imagem compartilhada. Além disso, você pode especificar várias réplicas para a imagem compartilhada para que, ao criar pools com várias VMs (mais de 600 VMs), economize tempo na criação do pool.

Usar uma imagem compartilhada configurada para o seu cenário pode fornecer várias vantagens:

- **Use as mesmas imagens em todas as regiões.** Você pode criar réplicas de imagens compartilhadas em diferentes regiões para que todos os seus pools usem a mesma imagem.
- **Configure o SO (sistema operacional)** . Você pode personalizar a configuração do disco do sistema operacional da imagem.
- **Pré-instalação aplicativos.** A pré-instalação de aplicativos no disco do SO é mais eficiente e menos propenso a erros do que instalar aplicativos depois de provisionar os nós de computação com uma tarefa inicial.
- **Copie grandes quantidades de dados de uma só vez.** Torne os dados estáticos parte da imagem compartilhada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza dados para cada nó do pool.
- **Expanda os pools para tamanhos maiores.** Com a Galeria de Imagens Compartilhadas, você pode criar pools maiores com suas imagens personalizadas juntamente com mais réplicas de imagem compartilhada.
- **Melhor desempenho do que usar apenas uma imagem gerenciada como uma imagem personalizada.** Para um pool de imagens personalizadas de imagem compartilhada, o tempo para alcançar o estado estacionário é de até 25% mais rápido e a latência de ociosidade de VM é de até 30% menor.
- **Agrupamento e controle de versão de imagem para facilitar o gerenciamento.** A definição de agrupamento de imagens contém informações sobre o motivo pelo qual a imagem foi criada, para qual sistema operacional e informações sobre como usar a imagem. O agrupamento de imagens permite um gerenciamento mais fácil das imagens. Para saber mais, confira [Definições de imagens](../virtual-machines/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Você precisa autenticar usando o Azure AD. Se você usar a chave de autenticação compartilhada, receberá um erro de autenticação.  

- **Uma conta do Lote do Azure.** Para criar uma conta do Lote, confira os guias de início rápido do Lote usando o [portal do Azure](quick-create-portal.md) ou a [CLI do Azure](quick-create-cli.md).

- **Uma imagem da Galeria de Imagens Compartilhadas**. Para criar uma imagem compartilhada, você precisa ter ou criar um recurso de imagem gerenciada. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados.

> [!NOTE]
> Se a imagem compartilhada não estiver na mesma assinatura que a conta do lote, você deverá [registrar o provedor de recursos Microsoft.Batch](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) para essa assinatura. As duas assinaturas devem estar no mesmo locatário do Azure AD.
>
> A imagem pode estar em uma região diferente, contanto que ela tenha réplicas na mesma região que a sua conta do lote.

Se você usar um aplicativo do Azure AD para criar um pool de imagens personalizado com uma imagem da Galeria de imagens compartilhada, esse aplicativo deverá ter recebido uma [função interna do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) que fornece acesso à imagem compartilhada. Você pode conceder esse acesso no portal do Azure navegando até a imagem compartilhada, selecionando **iam (controle de acesso)** e adicionando uma atribuição de função para o aplicativo.

## <a name="prepare-a-shared-image"></a>Preparar uma imagem compartilhada

No Azure, você pode preparar uma imagem compartilhada de uma imagem gerenciada, que pode ser criada a partir de:

- Instantâneos de discos de dados e do SO de uma VM do Azure
- Uma VM do Azure generalizada com discos gerenciados
- Um VHD local generalizado carregado para a nuvem

> [!NOTE]
> O lote dá suporte apenas a imagens compartilhadas generalizadas; uma imagem compartilhada especializada não pode ser usada para criar um pool.

As etapas a seguir mostram como preparar uma VM, gerar um instantâneo e criar uma imagem do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem interna do Azure Marketplace com suporte no Lote como a imagem base para sua imagem gerenciada. Somente imagens internas podem ser usadas como uma imagem base. Para obter uma lista de referências de imagens do Azure Marketplace com suporte no Lote do Azure, confira a operação [Listar SKUs do agente de nó](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenham licenças adicionais e termos de compra como sua imagem de base. Para obter informações sobre essas imagens do Marketplace, consulte as diretrizes para VMs do [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) ou do [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties).

Siga estas diretrizes ao criar VMs:

- Verifique se a VM é criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
- Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
- Ao usar discos de dados anexados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
- Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
- Verifique se o disco do sistema operacional não está criptografado.
- Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.
- Para o provisionamento mais rápido de pool, use a [configuração de cache de disco ReadWrite](../virtual-machines/premium-storage-performance.md#disk-caching) para o disco do sistema operacional da VM.

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image). Crie uma imagem especificando um instantâneo do disco do SO e, opcionalmente, um ou mais instantâneos de disco de dados.

### <a name="create-a-shared-image-gallery"></a>Criar uma Galeria de Imagens Compartilhadas

Após criar a imagem gerenciada com êxito, você precisará criar uma Galeria de Imagens Compartilhadas para disponibilizar sua imagem personalizada. Para saber como criar uma Galeria de Imagens Compartilhadas para suas imagens, confira [Criar uma Galeria de Imagens Compartilhadas com a CLI do Azure](../virtual-machines/shared-images-cli.md) ou [Criar uma Galeria de Imagens Compartilhadas usando o portal do Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Criar um pool de uma imagem compartilhada usando a CLI do Azure

Para criar um pool de sua imagem compartilhada usando a CLI do Azure, use o comando `az batch pool create`. Especifique a ID da imagem compartilhada no campo `--image`. Verifique se o tipo de SO e a SKU correspondem às versões especificadas por `--node-agent-sku-id`

> [!NOTE]
> Você precisa autenticar usando o Azure AD. Se você usar a chave de autenticação compartilhada, receberá um erro de autenticação.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Criar um pool de uma imagem compartilhada usando C#

Como alternativa, você pode criar um pool de uma imagem compartilhada usando o SDK C#.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Criar um pool de uma imagem compartilhada usando Python

Você também pode criar um pool de uma imagem compartilhada usando o SDK do Python: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Criar um pool de uma imagem compartilhada usando o portal do Azure

Usar as etapas a seguir para criar um pool de uma imagem compartilhada no portal do Azure.

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Vá para **Contas do Lote** e selecione sua conta.
1. Selecione **Pools** e, em seguida, **Adicionar** para criar um pool.
1. Na seção **Tipo de Imagem**, selecione **Galeria de Imagens Compartilhadas**.
1. Conclua as seções restantes com informações sobre sua imagem gerenciada.
1. Selecione **OK**.

![Crie um pool de uma imagem compartilhada com o portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas ou milhares de VMs ou mais usando uma imagem compartilhada, use as diretrizes a seguir.

- **Números de réplica da Galeria de Imagens Compartilhadas.**  Para cada pool com até 300 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando um pool com 3000 VMs, deverá manter pelo menos 10 réplicas da imagem. Sempre sugerimos manter mais réplicas do que os requisitos mínimos para obter melhor desempenho.

- **Tempo limite de redimensionamento.** Se o pool contiver um número fixo de nós (se não tiver dimensionamento automático), aumente a propriedade `resizeTimeout` do pool, dependendo do tamanho do pool. Para cada 1000 VMs, o tempo limite de redimensionamento recomendado é de pelo menos 15 minutos. Por exemplo, o tempo limite de redimensionamento recomendado para um pool com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral detalhada do Lote, confira [Recursos e fluxo de trabalho do serviço de Lote](batch-service-workflow-features.md).
- Saiba mais sobre a [Galeria de Imagens Compartilhadas](../virtual-machines/shared-image-galleries.md).