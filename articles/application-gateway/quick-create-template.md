---
title: 'Início Rápido: Direcionar o tráfego da Web usando um modelo do Resource Manager'
titleSuffix: Azure Application Gateway
description: Neste guia de início rápido, você aprenderá a usar um modelo do Resource Manager para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: dd100361ba5d4ff175e340ced782999e52c720c4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538446"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Modelo do ARM

Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um Gateway de Aplicativo do Azure. Em seguida, você testará o gateway de aplicativo para verificar se ele funciona corretamente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também é possível concluir este início rápido usando o [portal do Azure](quick-create-portal.md), o [Azure PowerShell](quick-create-powershell.md) ou a [CLI do Azure](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Para simplificar, este modelo cria uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um site no gateway de aplicativo, uma regra de roteamento de solicitação básica e duas máquinas virtuais usadas no pool de back-end.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : um para o gateway de aplicativo e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): para configurar o IIS e as páginas da Web

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo ARM no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um gateway de aplicativo, a infraestrutura de rede e duas máquinas virtuais no pool de back-end que executa o IIS.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Selecione ou crie seu grupo de recursos, digite o nome de usuário e a senha do administrador da máquina virtual.
3. Selecione **Examinar + Criar** e **Criar**.

   A implantação pode levar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

Embora o IIS não seja necessário para criar o gateway de aplicativo, ele é instalado para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público do gateway de aplicativo na página **Visão geral**. ![Registre o endereço IP público do gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) ou selecione **Todos os recursos**, insira *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **Visão geral**.
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador para procurar esse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

   ![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Atualize o navegador várias vezes e você deverá ver conexões com myVM1 e myVM2.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, exclua o grupo de recursos. Isso remove o gateway de aplicativo e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
