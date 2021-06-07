---
title: Criar um certificado do Atestado do Azure usando um modelo do Azure Resource Manager
description: Saiba como criar um certificado do Atestado do Azure usando um modelo do Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144975"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Início Rápido: Criar um provedor do Atestado do Azure com um modelo do ARM

O [Atestado do Microsoft Azure](overview.md) é uma solução usada para atestar TEEs (Ambientes de Execução Confiáveis). Este guia de início rápido tem como foco o processo de implantação de um modelo do ARM (Azure Resource Manager) para a criação de uma política do Atestado do Microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Os recursos do Azure definidos no modelo:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Selecione ou insira os seguintes valores.

    A menos que o valor seja especificado, use o valor padrão para criar o provedor de atestado.

    - **Nome do Provedor de Atestado**: escolha um nome para o provedor do Atestado do Azure.
    - **Localização**: Selecione um local. Por exemplo, **Centro dos EUA**.
    - **Marcas**: Selecione um local. Por exemplo, **Centro dos EUA**.

1. Selecione **Comprar**. Depois que o recurso de atestado for implantado com êxito, você receberá uma notificação.

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Use o portal do Azure para verificar o recurso de atestado.

## <a name="clean-up-resources"></a>Limpar os recursos

Outro Atestado do Azure se baseia neste guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.

Quando você não precisar mais do grupo de recursos, exclua-o, o que excluirá o recurso de atestado. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um recurso de atestado usando um modelo do ARM e validou a implantação. Para saber mais sobre o Atestado do Azure, confira [Visão geral do Atestado do Azure](overview.md).
