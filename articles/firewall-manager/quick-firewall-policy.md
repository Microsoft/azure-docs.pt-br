---
title: 'Início rápido: Criar um Firewall do Azure e uma política de firewall – modelo do Resource Manager'
description: Neste início rápido, você implantará um Firewall do Azure e uma política de firewall.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 43853d9e0b955167905af4777d533114a1d1f2ba
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529864"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Início rápido: Criar um Firewall do Azure e uma política de firewall – modelo do ARM

Neste início rápido, você usará um modelo do ARM (Azure Resource Manager) para criar um Firewall do Azure e uma política de firewall. A política de firewall tem uma regra de aplicativo que permite conexões com `www.microsoft.com` e uma regra que permite conexões com Windows Update usando a marca de FQDN do **WindowsUpdate**. Uma regra de rede permite conexões UDP para um servidor de horário em 13.86.101.172.

Além disso, os grupos de IP são usados nas regras para definir os endereços IP de **origem**.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter informações sobre o Gerenciador de Firewall do Azure, confira [O que é o Gerenciador de Firewall do Azure?](overview.md).

Para obter informações sobre o Firewall do Azure, confira [O que é o Firewall do Azure?](../firewall/overview.md).

Para obter informações sobre Grupos de IPs, confira [Grupos de IPs no Firewall do Azure](../firewall/ip-groups.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria uma rede virtual do hub, juntamente com os recursos necessários para dar suporte ao cenário.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo ARM no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um Firewall do Azure, uma WAN virtual e um hub virtual, a infraestrutura de rede e duas máquinas virtuais.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. No portal, na página **Criar um Firewall e FirewallPolicy com Regras e Ipgroups**, digite ou selecione os seguintes valores:
   - Assinatura: selecione entre as assinaturas existentes.
   - Grupo de recursos:  selecione um dos grupos de recursos existentes ou selecione **Criar** e selecione **OK**.
   - Região: Selecione uma região.
   - Nome do firewall: digite um nome para o firewall.

3. Selecione **Examinar + criar** e **Criar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Após a conclusão da implantação, você verá os recursos semelhantes a seguir.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Recursos implantados":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o firewall, exclua o grupo de recursos. Isso remove o firewall e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Visão geral da política do Gerenciador de Firewall do Azure](policy-overview.md)
