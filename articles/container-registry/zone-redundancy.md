---
title: Registro com redundância de zona para alta disponibilidade
description: Saiba como habilitar a redundância de zona no registro de contêiner do Azure. Crie um registro de contêiner ou replicação em uma zona de disponibilidade do Azure. A redundância de zona é um recurso da camada de serviço Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203854"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Habilitar a redundância de zona no registro de contêiner do Azure para resiliência e alta disponibilidade

Além da [replicação geográfica](container-registry-geo-replication.md), que replica os dados do registro em uma ou mais regiões do Azure para fornecer disponibilidade e reduzir a latência para operações regionais, o registro de contêiner do Azure dá suporte à *redundância de zona* opcional. A [redundância de zona](../availability-zones/az-overview.md#availability-zones) fornece resiliência e alta disponibilidade a um registro ou a um recurso de replicação (réplica) em uma região específica.

Este artigo mostra como configurar uma réplica ou um registro de contêiner com redundância de zona usando o modelo CLI do Azure, portal do Azure ou Azure Resource Manager. 

A redundância de zona é um recurso de **Visualização** da camada de serviço do registro de contêiner Premium. Para saber mais sobre os limites e as camadas de serviço do registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de visualização

* Atualmente com suporte nas seguintes regiões: leste dos EUA, leste dos EUA 2, oeste dos EUA 2, Europa Setentrional, Europa Ocidental, leste do Japão.
* Atualmente, não há suporte para conversões de região para zonas de disponibilidade. Para habilitar o suporte à zona de disponibilidade em uma região, o registro deve ser criado na região desejada, com o suporte à zona de disponibilidade habilitado, ou uma região replicada deve ser adicionada com o suporte à zona de disponibilidade habilitado.
* A redundância de zona não pode ser desabilitada em uma região.
* [As tarefas ACR](container-registry-tasks-overview.md) ainda não dão suporte a zonas de disponibilidade.

## <a name="about-zone-redundancy"></a>Sobre a redundância de zona

Use as [zonas de disponibilidade](../availability-zones/az-overview.md) do Azure para criar um registro de contêiner do Azure resiliente e de alta disponibilidade em uma região do Azure. Por exemplo, as organizações podem configurar um registro de contêiner do Azure com redundância de zona com outros [recursos do Azure com suporte](../availability-zones/az-region.md) para atender à residência de dados ou outros requisitos de conformidade, fornecendo alta disponibilidade em uma região.

O registro de contêiner do Azure também dá suporte à [replicação geográfica](container-registry-geo-replication.md), que Replica o serviço em várias regiões, permitindo a redundância e a localidade para computar recursos em outros locais. A combinação de zonas de disponibilidade para redundância em uma região e replicação geográfica em várias regiões aumenta a confiabilidade e o desempenho de um registro.

As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. Cada zona tem um ou mais data centers equipados com energia, resfriamento e rede independentes. Quando configurado para redundância de zona, um registro (ou uma réplica de registro em uma região diferente) é replicado em todas as zonas de disponibilidade na região, mantendo-a disponível se houver falhas no datacenter.

## <a name="create-a-zone-redundant-registry---cli"></a>Criar um registro com redundância de zona-CLI

Para usar o CLI do Azure para habilitar a redundância de zona, é necessário CLI do Azure versão 2.17.0 ou posterior ou Azure Cloud Shell. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [AZ Group Create](/cli/azure/group#az_group_create) para criar um grupo de recursos para o registro.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Criar registro habilitado para zona

Execute o comando [AZ ACR Create](/cli/azure/acr#az_acr_create) para criar um registro com redundância de zona na camada de serviço Premium. Escolha uma região que [ofereça suporte a zonas de disponibilidade](../availability-zones/az-region.md) para o registro de contêiner do Azure. No exemplo a seguir, a redundância de zona está habilitada na região *lesteus* . Consulte a `az acr create` ajuda do comando para obter mais opções de registro.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Na saída do comando, observe a `zoneRedundancy` Propriedade do registro. Quando habilitado, o registro tem redundância de zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Criar replicação com redundância de zona

Execute o comando [AZ ACR Replication Create](/cli/azure/acr/replication#az_acr_replication_create) para criar uma réplica de registro com redundância de zona em uma região que [dá suporte a zonas de disponibilidade](../availability-zones/az-region.md) para o registro de contêiner do Azure, como *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Na saída do comando, observe a `zoneRedundancy` propriedade da réplica. Quando habilitado, a réplica tem redundância de zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Criar um registro com redundância de zona-Portal

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.
1. Na guia **noções básicas** , selecione ou crie um grupo de recursos e insira um nome de registro exclusivo. 
1. Em **local**, selecione uma região que ofereça suporte à redundância de zona para o registro de contêiner do Azure, como *leste dos EUA*.
1. Em **SKU**, selecione **Premium**.
1. Em **zonas de disponibilidade**, selecione **habilitado**.
1. Opcionalmente, defina configurações adicionais do registro e, em seguida, selecione **revisar + criar**.
1. Escolha **Criar** para implantar a instância do Registro.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Habilitar a redundância de zona no portal do Azure":::

Para criar uma replicação com redundância de zona:

1. Navegue até o registro de contêiner da camada Premium e selecione **replicações**.
1. No mapa que aparece, selecione um hexágono verde em uma região que ofereça suporte à redundância de zona para o registro de contêiner do Azure, como **oeste dos EUA 2**. Ou selecione **+ Adicionar**.
1. Na janela **criar replicação** , confirme o **local**. Em **zonas de disponibilidade**, selecione **habilitado** e, em seguida, selecione **criar**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Habilitar a replicação com redundância de zona no portal do Azure":::

## <a name="create-a-zone-redundant-registry---template"></a>Criar um modelo de registro com redundância de zona

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [AZ Group Create](/cli/azure/group#az_group_create) para criar um grupo de recursos para o registro em uma região que [dê suporte a zonas de disponibilidade](../availability-zones/az-region.md) para o registro de contêiner do Azure, como *eastus*. Essa região é usada pelo modelo para definir o local do registro.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Implantar o modelo 

Você pode usar o seguinte modelo do Resource Manager para criar um registro replicado geograficamente com redundância de zona. Por padrão, o modelo habilita a redundância de zona no registro e uma réplica regional. 

Copie o conteúdo a seguir em um novo arquivo e salve-o usando um nome de arquivo como `registryZone.json`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Execute o comando [AZ Deployment Group Create](/cli/azure/group/deployment#az_group_deployment_create) a seguir para criar o registro usando o arquivo de modelo anterior. Quando indicado, forneça:

* um nome de registro exclusivo ou implantar o modelo sem parâmetros e ele criará um nome exclusivo para você
* um local para a réplica que dá suporte a zonas de disponibilidade, como *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Na saída do comando, observe a `zoneRedundancy` propriedade para o registro e a réplica. Quando habilitado, cada recurso tem redundância de zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [regiões que dão suporte a zonas de disponibilidade](../availability-zones/az-region.md).
* Saiba mais sobre a criação de [confiabilidade](/azure/architecture/framework/resiliency/overview) no Azure.
