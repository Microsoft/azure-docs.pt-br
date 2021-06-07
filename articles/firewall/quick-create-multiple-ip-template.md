---
title: 'Início Rápido: Criar um Firewall do Azure com vários endereços IP públicos – modelo do Resource Manager'
description: Neste guia de início rápido, aprenda a usar um modelo do ARM (Azure Resource Manager) para criar um Firewall do Azure com vários endereços IP públicos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 33d110f327a381241fd9dbf55996a6e3f9fd3a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057970"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>Início Rápido: Criar um Firewall do Azure com vários endereços IP públicos – Modelo do ARM

Neste guia de início rápido, você usará um modelo do ARM (Azure Resource Manager) para implantar um Firewall do Azure com vários endereços IP públicos usando um prefixo de endereço IP público. O firewall implantado tem regras de coleção de regras NAT que permitem conexões RDP com duas máquinas virtuais do Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter mais informações sobre o Firewall do Azure com vários endereços IP públicos, confira [Implantar um Firewall do Azure com vários endereços IP públicos usando o Azure PowerShell](deploy-multi-public-ip-powershell.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Examinar o modelo

Este modelo cria um Firewall do Azure com dois endereços IP públicos, juntamente com os recursos necessários para dar suporte ao Firewall do Azure.

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPPrefix**](/azure/templates/microsoft.network/publicipprefixes)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo ARM no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um Firewall do Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. No portal, na página **Criar um Firewall do Azure com vários endereços IP públicos**, digite ou selecione os seguintes valores:
   - Assinatura: selecione entre as assinaturas existentes 
   - Grupo de recursos:  selecione um dos grupos de recursos existentes ou selecione **Criar** e selecione **OK**.
   - Localização: Selecione um local
   - Nome de usuário do administrador: digite o nome de usuário para a conta de usuário administrador 
   - Senha do Administrador: digite uma senha de administrador ou chave

3. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

Na portal do Azure, examine os recursos implantados. Observe os endereços IP público do firewall.  

Use a Conexão de Área de Trabalho Remota para se conectar aos endereços IP públicos do firewall. Conexões com êxito demonstram as regras de NAT de firewall que permitem a conexão com os servidores de back-end.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o firewall, exclua o grupo de recursos. Isso remove o firewall e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)
