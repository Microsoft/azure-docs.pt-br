---
title: Criar e habilitar um plano de Proteção contra DDoS do Azure usando um modelo do ARM (Azure Resource Manager).
description: Saiba como criar e habilitar um plano de Proteção contra DDoS do Azure usando um modelo do ARM (Azure Resource Manager).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092493"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Início rápido: Criar uma Proteção contra DDoS do Azure Standard usando o modelo do ARM

Este início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar um plano de proteção contra DDoS (negação de serviço) distribuído e uma VNet (rede virtual). Em seguida, habilita o plano de proteção para a VNet. Um plano da Proteção contra DDoS do Azure Standard define um conjunto de redes virtuais que têm uma proteção contra DDoS habilitada entre assinaturas. Você pode configurar um plano de proteção contra DDoS para sua organização e vincular redes virtuais de várias assinaturas para o mesmo plano.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

O modelo define dois recursos:

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Implantar o modelo

Neste exemplo, o modelo cria um grupo de recursos, um plano de proteção contra DDoS e uma VNet.

1. Para entrar no Azure e abrir o modelo, selecione o botão **Implantar no Azure**.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Insira os valores para criar um grupo de recursos, o plano de proteção contra DDoS e o nome da VNet.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Modelo de início rápido da DDoS.":::

    - **Assinatura**: nome da assinatura do Azure em que os recursos serão implantados.
    - **Grupo de recursos**: selecione um grupo de recursos existente ou crie um grupo de recursos.
    - **Região**: a região em que o grupo de recursos está implantado, como Leste dos EUA.
    - **Nome do Plano de Proteção contra DDoS**: o nome do novo plano de proteção contra DDoS.
    - **Nome da Rede Virtual**: cria um nome para a nova VNet.
    - **Localização**: função que usa a mesma região que o grupo de recursos para a implantação de recursos.
    - **Prefixo de Endereço da VNet**: use o valor padrão ou insira o endereço da VNet.
    - **Prefixo de Sub-rede**: use o valor padrão ou insira a sub-rede da VNet.
    - **Plano de Proteção contra DDoS Habilitado**: o padrão é `true` para habilitar o plano de proteção contra DDoS.

1. Selecione **Examinar + criar**.
1. Verifique se a validação do modelo foi aprovada e selecione **Criar** para iniciar a implantação.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Para copiar a CLI do Azure ou o comando do Azure PowerShell, selecione o botão **Copiar**. O botão **Experimente** abre o Azure Cloud Shell para executar o comando.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

A saída mostra os novos recursos.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, você poderá excluir os recursos. O comando exclui o grupo de recursos e todos os recursos que ele contém.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Próximas etapas

Para saber como exibir e configurar a telemetria para seu plano de proteção contra DDoS, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Exibir e configurar a telemetria da Proteção contra DDoS](telemetry.md)
