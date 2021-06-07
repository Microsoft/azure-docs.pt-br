---
title: 'Início Rápido: Criar um WAF v2 do Azure no Gateway de Aplicativo – modelo do Azure Resource Manager'
titleSuffix: Azure Application Gateway
description: Saiba como usar um modelo de início rápido do ARM (modelo do Azure Resource Manager) para criar um Firewall do Aplicativo Web v2 no Gateway de Aplicativo do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091314"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Início Rápido: Criar um WAF v2 do Azure no Gateway de Aplicativo usando um modelo do ARM

Neste início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um Firewall do Aplicativo Web v2 do Azure no Gateway de Aplicativo.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria um Firewall do Aplicativo Web v2 simples no Gateway de Aplicativo do Azure. Isso inclui um endereço IP de front-end do IP público, configurações de HTTP, uma regra com um ouvinte básico na porta 80 e um pool de back-end. Uma política de WAF com uma regra personalizada é criada para bloquear o tráfego para o pool de back-end com base em um tipo de correspondência de endereço IP.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : um para o gateway de aplicativo e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): para configurar o IIS e as páginas da Web

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo ARM no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um gateway de aplicativo, a infraestrutura de rede e duas máquinas virtuais no pool de back-end que executa o IIS.

   [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selecione ou crie o grupo de recursos.
3. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

Embora o IIS não seja necessário para criar o gateway de aplicativo, ele é instalado em servidores de back-end para verificar se o Azure criou um WAF v2 no gateway de aplicativo com êxito.

Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público do gateway de aplicativo na página **Visão geral**. ![Registre o endereço IP público do gateway de aplicativo](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) ou selecione **Todos os recursos**, insira *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **Visão geral**.
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador para procurar esse endereço IP.
3. Verifique a resposta. Uma resposta **403 Proibido** verifica se o WAF foi criado com êxito e está bloqueando as conexões com o pool de back-end.
4. Altere a regra personalizada para **Permitir o tráfego**.
  Execute o seguinte script do Azure PowerShell, substituindo o nome do grupo de recursos:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Atualize o navegador várias vezes e você deverá ver conexões com myVM1 e myVM2.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, exclua o grupo de recursos. Isso remove o gateway de aplicativo e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Criar um gateway de aplicativo com um Firewall do Aplicativo Web usando o portal do Azure](application-gateway-web-application-firewall-portal.md)