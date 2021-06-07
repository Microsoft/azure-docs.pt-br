---
title: Usar uma imagem gerenciada para criar um pool de imagens personalizado
description: Crie um pool de imagens personalizadas do lote de uma imagem gerenciada para provisionar nós de computação com o software e os dados do seu aplicativo.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 9baa65c0f1c1844ea10e3d5b4f0b48924912d233
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023870"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Usar uma imagem gerenciada para criar um pool de imagens personalizado

Para criar um pool de imagens personalizado para as VMs (máquinas virtuais) do seu pool do lote, você pode usar uma imagem gerenciada para criar uma [imagem da Galeria de imagens compartilhada](batch-sig-images.md). Também há suporte para o uso de apenas uma imagem gerenciada, mas só para versões da API até o dia 01 de agosto de 2019.

> [!IMPORTANT]
> Na maioria dos casos, você deve criar imagens personalizadas usando a Galeria de Imagens Compartilhadas. Usando a Galeria de Imagens Compartilhadas, você pode provisionar pools mais rapidamente, dimensionar quantidades maiores de VMs e aumentar a confiabilidade ao provisionar VMs. Para saber mais, consulte [Usar a Galeria de Imagens Compartilhadas para criar um pool personalizado](batch-sig-images.md).

Este tópico explica como criar um pool de imagens personalizado usando apenas uma imagem gerenciada.

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do Lote. A imagem deve ser criada de instantâneos de disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados.
  - Use uma imagem personalizada exclusiva para cada pool que criar.
  - Para criar um pool com a imagem usando as APIs de Lote, especifique a **ID de recurso** da imagem, que tem a forma `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`.
  - O recurso de imagem gerenciada deve existir pelo tempo de vida do pool para permitir a expansão e pode ser removido após a exclusão do pool.

- **Autenticação do Azure AD (Azure Active Directory)** . A API do cliente do Lote deve usar a autenticação do Azure Active Directory. O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Preparar uma imagem gerenciada

No Azure, você pode preparar uma imagem gerenciada de:

- Instantâneos de discos de dados e do SO de uma VM do Azure
- Uma VM do Azure generalizada com discos gerenciados
- Um VHD local generalizado carregado para a nuvem

Para dimensionar os pools de lote de forma confiável com uma imagem gerenciada, é recomendável criar a imagem gerenciada usando *apenas* o primeiro método: usando instantâneos de discos da VM. As etapas a seguir mostram como preparar uma VM, gerar um instantâneo e criar uma imagem gerenciada do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem interna do Azure Marketplace com suporte no Lote como a imagem base para sua imagem gerenciada. Somente imagens internas podem ser usadas como uma imagem base. Para obter uma lista de referências de imagens do Azure Marketplace com suporte no Lote do Azure, confira a operação [Listar SKUs do agente de nó](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus).

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenham licenças adicionais e termos de compra como sua imagem de base. Para obter informações sobre essas imagens do Marketplace, consulte as diretrizes para VMs do [Linux](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information) ou do [Windows](../virtual-machines/windows/cli-ps-findimage.md#view-purchase-plan-properties).

- Verifique se a VM é criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
- Não instale extensões do Azure, tais como a extensão de Script personalizado, na VM. Se a imagem contém uma extensão pré-instalada, o Azure pode ter problemas ao implantar a o pool do Lote.
- Ao usar discos de dados anexados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
- Verifique se a imagem do sistema operacional base que você forneceu usa unidade temporária padrão. O agente do nó de Lote no momento espera unidade temporária padrão.
- Verifique se o disco do sistema operacional não está criptografado.
- Quando a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale o software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo da VM

Um instantâneo é uma cópia completa somente leitura de um VHD. Para criar um instantâneo de discos de dados e do SO de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para etapas e opções para criar um instantâneo, veja as diretrizes para VMs [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md).

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use ferramentas de linha de comando do Azure como o comando [az image create](/cli/azure/image). Você pode criar uma imagem especificando um instantâneo do disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-managed-image"></a>Criar um pool a partir de uma imagem gerenciada

Depois de encontrar a ID do recurso da sua imagem gerenciada, crie um pool de imagens personalizadas a partir dessa imagem. As etapas a seguir mostram como criar um pool de imagens personalizadas usando o Serviço de Lote ou o Gerenciamento do Lote.

> [!NOTE]
> Garanta que a identidade que você usa para a autenticação do Azure Active Directory tenha permissões para o recurso de imagem. Consulte [Autenticar soluções do serviço do Lote no Active Directory](batch-aad-auth.md).
>
> O recurso para a imagem gerenciada deve existir durante o tempo de vida do pool. Se o recurso subjacente for excluído, o pool não poderá ser dimensionado.

### <a name="batch-service-net-sdk"></a>SDK do .NET do Serviço de Lote

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>API REST do Gerenciamento do Lote

URI da API REST

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Corpo da solicitação

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Considerações sobre pools grandes

Se você planeja criar um pool com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir as diretrizes anteriores para usar uma imagem criada de um instantâneo de VM.

Observe também as seguintes considerações:

- **Limites de tamanho** – o Lote limita o tamanho do pool a 2.500 nós de computação dedicados, ou 1.000 nós de baixa prioridade, quando você usa uma imagem personalizada.

  Se você usar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar vários pools, os nós de computação totais nos pools não poderão exceder os limites anteriores. Não recomendamos o uso de uma imagem ou do respectivo instantâneo subjacente para mais de um único pool.

  Os limites poderão ser reduzidos se você configurar o pool com [pools NAT de entrada](pool-endpoint-configuration.md).

- **Tempo limite de redimensionamento** – se o pool contém um número fixo de nós (não dimensiona automaticamente), aumente propriedade resizeTimeout do pool para um valor como 20 ou 30 minutos. Se o seu pool não alcançar seu tamanho de destino dentro do período de tempo limite, execute outra [operação de redimensionamento](/rest/api/batchservice/pool/resize).

  Se você planejar um pool com mais de 300 nós de computação, você poderá precisar redimensionar o pool várias vezes para alcançar o tamanho de destino.
  
Usando a [Galeria de Imagens Compartilhadas](batch-sig-images.md), você pode criar pools maiores com suas imagens personalizadas juntamente com mais réplicas de imagem compartilhada. Usando imagens compartilhadas, o tempo necessário para o pool alcançar o estado constante é de até 25% mais rápido e a latência de ociosidade da VM é de até 30% mais curta.

## <a name="considerations-for-using-packer"></a>Considerações para o uso do Packer

A criação de um recurso de imagem gerenciada diretamente com o Packer só pode ser feita com contas do Lote no modo de assinatura de usuário. Para contas do modo de serviço de Lote, primeiro você precisa criar um VHD e, em seguida, importar o VHD para um recurso de imagem gerenciada. Dependendo do modo de alocação de pool (assinatura do usuário ou serviço de Lote), as etapas de criação de um recurso de imagem gerenciada vão variar.

Garanta que o recurso usado para criar a imagem gerenciada exista para os tempos de vida de qualquer pool que referencie a imagem personalizada. A impossibilidade de fazer isso pode resultar em falhas de alocação de pool e/ou em falhas de redimensionamento.

Se a imagem ou o recurso subjacente for removido, você poderá receber um erro semelhante a este: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se você o receber, verifique se o recurso subjacente não foi removido.

Para obter mais informações sobre como usar o Packer para criar uma VM, consulte [Criar uma imagem do Linux com o Packer](../virtual-machines/linux/build-image-with-packer.md) ou [Criar uma imagem do Windows com o Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar a [Galeria de Imagens Compartilhadas](batch-sig-images.md) para criar um pool personalizado.
- Para obter uma visão geral detalhada do Lote, confira [Recursos e fluxo de trabalho do serviço de Lote](batch-service-workflow-features.md).
