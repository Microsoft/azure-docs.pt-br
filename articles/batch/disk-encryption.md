---
title: Criar um pool com a criptografia de disco habilitada
description: Saiba como usar a configuração de criptografia de disco para criptografar nós com uma chave gerenciada por plataforma.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055307"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Criar um pool com a criptografia de disco habilitada

Ao criar um pool do lote do Azure usando a [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration), você pode criptografar nós de computação no pool com uma chave gerenciada por plataforma, especificando a configuração de criptografia de disco.

Este artigo explica como criar um pool do lote com a criptografia de disco habilitada.

> [!IMPORTANT]
> O suporte para criptografia no host usando uma chave gerenciada por plataforma no lote do Azure está atualmente em visualização pública para as regiões leste dos EUA, oeste dos EUA 2, Sul EUA Central, US Gov-Virgínia e US Gov Arizona.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Por que usar um pool com a configuração de criptografia de disco?

Com um pool do lote, você pode acessar e armazenar dados no sistema operacional e nos discos temporários do nó de computação. Criptografar o disco do lado do servidor com uma chave gerenciada por plataforma protegerá esses dados com baixa sobrecarga e conveniência.

O lote aplicará uma dessas tecnologias de criptografia de disco em nós de computação, com base na configuração de pool e suporte regional.

- [Criptografia de disco gerenciado em repouso com chaves gerenciadas pela plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Criptografia no host usando uma chave gerenciada por plataforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Criptografia de Disco do Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Você não poderá especificar qual método de criptografia será aplicado aos nós no pool. Em vez disso, você fornece os discos de destino que deseja criptografar em seus nós e o lote pode escolher o método de criptografia apropriado, garantindo que os discos especificados sejam criptografados no nó de computação.

> [!IMPORTANT]
> Se você estiver criando seu pool com uma [imagem personalizada](batch-sig-images.md), poderá habilitar a criptografia de disco somente se estiver usando VMs do Windows.

## <a name="azure-portal"></a>Portal do Azure

Ao criar um pool do lote no portal do Azure, selecione **TemporaryDisk** ou **OsAndTemporaryDisk** em configuração de **criptografia de disco**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Captura de tela da opção de configuração de criptografia de disco no portal do Azure.":::

Depois que o pool é criado, você pode ver os destinos de configuração de criptografia de disco na seção de **Propriedades** do pool.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Captura de tela mostrando os destinos de configuração de criptografia de disco na portal do Azure.":::

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como criptografar o sistema operacional e os discos temporários em um pool do lote usando o SDK do .NET do lote, a API REST do lote e o CLI do Azure.

### <a name="batch-net-sdk"></a>SDK do .NET para o Lote

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>API REST do Lote

URL DA API REST:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo da solicitação:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [criptografia do lado do servidor de armazenamento em disco do Azure](../virtual-machines/disk-encryption.md).
- Para obter uma visão geral detalhada do Lote, confira [Recursos e fluxo de trabalho do serviço de Lote](batch-service-workflow-features.md).
