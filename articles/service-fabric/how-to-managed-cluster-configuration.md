---
title: Configurar seu Service Fabric cluster gerenciado (versão prévia)
description: Saiba como configurar seu Service Fabric cluster gerenciado para atualizações automáticas do sistema operacional, regras NSG e muito mais.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732625"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Opções de configuração do Service Fabric cluster gerenciado (versão prévia)

Além de selecionar o [Service Fabric SKU de cluster gerenciado](overview-managed-cluster.md#service-fabric-managed-cluster-skus) ao criar o cluster, há várias outras maneiras de configurá-lo. Na visualização atual, você pode:

* Configurar [Opções de rede](how-to-managed-cluster-networking.md) para o cluster
* Adicionar uma [extensão do conjunto de dimensionamento de máquinas virtuais](how-to-managed-cluster-vmss-extension.md) a um tipo de nó
* Configurar [identidade gerenciada](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) em seus tipos de nó
* Habilitar [atualizações automáticas do so](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) para seus nós
* Habilitar o [sistema operacional e a criptografia de disco de dados](how-to-enable-managed-cluster-disk-encryption.md) em seus nós

## <a name="enable-automatic-os-image-upgrades"></a>Habilitar atualizações automáticas de imagem de so

Você pode optar por habilitar atualizações automáticas de imagem do sistema operacional para as máquinas virtuais que executam seus nós de cluster gerenciado. Embora os recursos do conjunto de dimensionamento de máquinas virtuais sejam gerenciados em seu nome com Service Fabric clusters gerenciados, é sua opção de habilitar atualizações automáticas de imagem do sistema operacional para seus nós de cluster. Assim como os clusters de [Service Fabric clássicos](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) , os nós de cluster gerenciados não são atualizados por padrão, a fim de evitar interrupções não intencionais no cluster.

Para habilitar atualizações automáticas do so:

* Use a `2021-01-01-preview` versão (ou posterior) dos recursos *Microsoft. managedclusters/* e *Microsoft. infabric/managedclusters/NodeTypes*
* Defina a propriedade do cluster `enableAutoOSUpgrade` como *true*
* Definir a propriedade de recurso de nodeTypes do cluster `vmImageVersion` como *mais recente*

Por exemplo:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

Uma vez habilitado, Service Fabric começará a consultar e controlar as versões de imagem do sistema operacional no cluster gerenciado. Se uma nova versão do sistema operacional estiver disponível, os tipos de nó de cluster (conjuntos de dimensionamento de máquinas virtuais) serão atualizados, um de cada vez. Service Fabric atualizações de tempo de execução são executadas somente após a confirmação de que nenhuma atualização de imagem do sistema operacional do nó do cluster está em andamento.

Se uma atualização falhar, Service Fabric tentará novamente após 24 horas, para um máximo de três repetições. Semelhante às atualizações de Service Fabric clássicas (não gerenciadas), os aplicativos ou nós não íntegros podem bloquear a atualização da imagem do sistema operacional.

Para obter mais informações sobre atualizações de imagem, consulte [atualizações automáticas de imagem de so com conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Próximas etapas

[Visão geral de clusters gerenciados Service Fabric](overview-managed-cluster.md)

[Modelos de cluster do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates)
