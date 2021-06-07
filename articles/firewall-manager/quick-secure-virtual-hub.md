---
title: 'Início Rápido: Proteger o hub virtual usando o Gerenciador de Firewall do Azure – Modelo do Resource Manager'
description: Neste guia de início rápido, saiba como proteger o seu hub virtual usando o Gerenciador de Firewall do Azure.
services: firewall-manager
author: vhorne
ms.author: victorh
ms.date: 08/28/2020
ms.topic: quickstart
ms.service: firewall-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 88b961f269c89a1e19c0fd4419ffdc33706423de
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529646"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Início Rápido: Proteger seu hub virtual usando o Gerenciador de Firewall do Azure – modelo ARM

Neste guia de início rápido, você usará um modelo ARM (modelo do Azure Resource Manager) para proteger seu hub virtual por meio do Gerenciador de Firewall do Azure. O firewall implantado tem uma regra de aplicativo que permite conexões com `www.microsoft.com`. Duas máquinas virtuais do Windows Server 2019 são implantadas para testar o firewall. Um servidor de salto é usado para se conectar ao servidor da carga de trabalho. No servidor da carga de trabalho, você só pode se conectar a `www.microsoft.com`.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter mais informações sobre o Gerenciador de Firewall do Azure, confira [O que é o Gerenciador de Firewall do Azure?](overview.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria um hub virtual seguro usando o Gerenciador de Firewall do Azure, juntamente com os recursos necessários para dar suporte ao cenário.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo ARM no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um Firewall do Azure, uma WAN virtual e um hub virtual, a infraestrutura de rede e duas máquinas virtuais.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. No portal, na página **Hubs virtuais protegidos**, digite ou selecione os seguintes valores:
   - Assinatura: selecione entre as assinaturas existentes 
   - Grupo de recursos:  selecione um dos grupos de recursos existentes ou selecione **Criar** e selecione **OK**.
   - Localização: Selecione um local
   - Nome de usuário do administrador: digite o nome de usuário para a conta de usuário administrador 
   - Senha do Administrador: digite uma senha de administrador ou chave

3. Selecione **Examinar + criar** e **Criar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

Agora, teste as regras de firewall para confirmar se elas funcionam conforme o esperado.

1. No portal do Azure, revise as configurações de rede da máquina virtual **Workload-Srv** e anote o endereço IP privado.
2. Conecte uma área de trabalho remota à máquina virtual **Jump-Srv** e entre. De lá, abra uma conexão de área de trabalho remota para o endereço IP privado **Workload-Srv**.

3. Abra o Internet Explorer e navegue até `www.microsoft.com`.
4. Selecione **OK** > **Fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver a página inicial da Microsoft.

5. Navegue até `www.google.com`.

   Você deve ser bloqueado pelo firewall.

Agora que você verificou se as regras de firewall estão funcionando:

- Você pode navegar para o FQDN permitido, mas não para os outros.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o firewall, exclua o grupo de recursos. Isso remove o firewall e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os provedores de parceiros de segurança](trusted-security-partners.md)
