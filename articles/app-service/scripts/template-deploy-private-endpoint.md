---
title: Usar um modelo do Azure Resource Manager para implantar um ponto de extremidade privado para um aplicativo Web
description: Saiba como usar o modelo ARM para implantar um ponto de extremidade privado para o Aplicativo Web.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652026"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Criar um aplicativo do Serviço de Aplicativo e implantar um ponto de extremidade privado usando um modelo do Azure Resource Manager

Neste início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um aplicativo Web e expô-lo com um ponto de extremidade privado.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Pré-requisito

Você precisa de uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Este modelo cria um ponto de extremidade privado para um aplicativo Web do Azure.

### <a name="review-the-template"></a>Examinar o modelo

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Implantar o modelo

Veja como implantar o modelo do Azure Resource Manager no Azure:

1. Para entrar no Azure e abrir o modelo, selecione este link:  [Implantar ao Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). O modelo cria a rede virtual, o Aplicativo Web, o ponto de extremidade privado e a zona DNS privada.
2. Selecione ou crie o grupo de recursos.
3. Digite o nome do aplicativo Web, do Plano do Serviço de Aplicativo do Azure e do ponto de extremidade privado.
5. Leia a instrução sobre os termos e condições. Se concordar, selecione **Concordo com os termos e condições declarados acima** > **Comprar**. A implantação pode levar vários minutos para ser concluída.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o ponto de extremidade privado, exclua o grupo de recursos. Isso remove o ponto de extremidade privado e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

- Você pode encontrar mais modelos do Azure Resource Manager para aplicativos Web do Serviço de Aplicativo do Azure nos [exemplos de modelo ARM](../samples-resource-manager-templates.md).
