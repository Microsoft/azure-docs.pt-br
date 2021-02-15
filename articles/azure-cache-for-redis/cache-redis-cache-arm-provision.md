---
title: Implantar o cache do Azure para Redis usando o modelo Azure Resource Manager
description: Saiba como usar um modelo de Azure Resource Manager (modelo ARM) para implantar um cache do Azure para o recurso Redis. Os modelos são fornecidos para cenários comuns.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735961"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Início rápido: criar um cache do Azure para Redis usando um modelo ARM

Saiba como criar um modelo de Azure Resource Manager (modelo ARM) que implanta um cache do Azure para Redis. O cache pode ser usado com uma conta de armazenamento existente para manter os dados de diagnóstico. Você também aprende como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Atualmente, as configurações de diagnóstico são compartilhadas por todos os caches na mesma região de uma assinatura. A atualização de um cache na região afeta todos os outros caches na região.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure** . O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure** : Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Uma conta de armazenamento** : para criar uma, confira [criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal). A conta de armazenamento é usada para dados de diagnóstico.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. insights/DiagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

Os modelos do Resource Manager para a nova [camada Premium](cache-overview.md#service-tiers) também estão disponíveis.

* [Criar um Cache do Azure para Redis Premium com clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Criar um Cache Redis do Azure Premium com persistência de dados](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Criar um cache Redis Premium implantado em uma rede virtual](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Para verificar os modelos mais recentes, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/) e pesquise o _cache do Azure para Redis_ .

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Selecione ou insira os valores a seguir:

    * **Assinatura** : selecione uma assinatura do Azure usada para criar o compartilhamento de dados e os outros recursos.
    * **Grupo de recursos** : selecione **Criar** para criar um grupo de recursos ou selecione um existente.
    * **Local** : selecione um local para o grupo de recursos. A conta de armazenamento e o cache Redis devem estar na mesma região. Por padrão, o cache Redis usa o mesmo local que o grupo de recursos. Portanto, especifique o mesmo local que a conta de armazenamento.
    * **Nome do cache Redis** : Insira um nome para o cache Redis.
    * **Conta de armazenamento de diagnóstico existente** : Insira a ID de recurso de uma conta de armazenamento. A sintaxe é `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Use o valor padrão para o restante das configurações.
1. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar** .

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).
1. Abra o cache Redis que você criou.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá a criar um modelo de Azure Resource Manager que implanta um cache do Azure para Redis. Para saber como criar um modelo de Azure Resource Manager que implanta um aplicativo Web do Azure com o cache do Azure para Redis, consulte [criar um aplicativo Web mais o cache do Azure para Redis usando um modelo](./cache-web-app-arm-with-redis-cache-provision.md).
