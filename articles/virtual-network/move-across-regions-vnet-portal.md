---
title: Mova uma rede virtual do Azure para outra região do Azure usando o portal do Azure.
description: Mova uma rede virtual do Azure de uma região do Azure para outra usando um modelo do Resource Manager e o portal do Azure.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223301"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Mover uma rede virtual do Azure para outra região usando o portal do Azure

Há vários cenários para mover uma rede virtual do Azure existente de uma região para outra. Por exemplo, talvez você queira criar uma rede virtual com a mesma configuração para teste e disponibilidade como sua rede virtual existente. Ou talvez você queira mover uma rede virtual de produção para outra região como parte de seu planejamento de recuperação de desastre.

Você pode usar um modelo de Azure Resource Manager para concluir a movimentação da rede virtual para outra região. Você faz isso exportando a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região. Para obter mais informações sobre modelos do Resource Manager, consulte [início rápido: criar e implantar modelos de Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se sua rede virtual está na região do Azure da qual você deseja mover.

- Para exportar uma rede virtual e implantar um modelo para criar uma rede virtual em outra região, você precisa ter a função de colaborador de rede ou superior.

- Os emparelhamentos de rede virtual não serão recriados, e eles falharão se ainda estiverem presentes no modelo. Antes de exportar o modelo, você precisa remover os pares de rede virtual. Em seguida, você pode reestabelecer esses itens após a movimentação da rede virtual.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, NSGs (grupos de segurança de rede) e IPs públicos.

- Verifique se sua assinatura do Azure permite que você crie redes virtuais na região de destino. Para habilitar a cota necessária, contate o suporte.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de redes virtuais para esse processo. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Preparar para a movimentação
Nesta seção, você prepara a rede virtual para a movimentação usando um modelo do Resource Manager. Em seguida, mova a rede virtual para a região de destino usando o portal do Azure.

Para exportar a rede virtual e implantar a rede virtual de destino usando o portal do Azure, faça o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com)e, em seguida, selecione **grupos de recursos**.
1. Localize o grupo de recursos que contém a rede virtual de origem e, em seguida, selecione-o.
1. Selecione **configurações**  >  **Exportar modelo**.
1. No painel **Exportar modelo** , selecione **implantar**.
1. Para abrir o *parameters.jsno* arquivo em seu editor online, selecione **modelo**  >  **Editar parâmetros**.
1. Para editar o parâmetro do nome da rede virtual, altere a propriedade **Value** em **parâmetros**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. No editor, altere o valor nome da rede virtual de origem no editor para um nome que você deseja para a rede virtual de destino. Certifique-se de colocar o nome entre aspas.

1. Selecione **salvar** no editor.

1. Para abrir o *template.jsno* arquivo no editor online, selecione **modelo**  >  **Editar modelo**.

1. No editor online, para editar a região de destino onde a rede virtual será movida, altere a propriedade **local** em **recursos**:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região, sem espaços (por exemplo, **EUA Central**  =  **centralus**).

1. Adicional Você também pode alterar outros parâmetros no modelo, dependendo de seus requisitos:

    * **Espaço de endereço**: antes de salvar o arquivo, você pode alterar o espaço de endereço da rede virtual modificando a seção **recursos** de  >  **addressSpace** e alterando a propriedade **addressPrefixes** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Sub-rede**: você pode alterar ou adicionar o nome da sub-rede e o espaço de endereço da sub-rede alterando a seção **sub-redes** do modelo. Você pode alterar o nome da sub-rede alterando a propriedade **Name** . E você pode alterar o espaço de endereço de sub-rede alterando a propriedade **addressPrefix** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Para alterar o prefixo de endereço no *template.jsno* arquivo, edite-o em dois locais: no código da seção anterior e na seção **tipo** do código a seguir. Altere a propriedade **addressPrefix** no código a seguir para corresponder à propriedade **addressPrefix** no código da seção anterior.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. No editor online, selecione **salvar**.

1. Para escolher a assinatura na qual a rede virtual de destino será implantada, selecione   >  **assinatura** básica.

1. Para escolher o grupo de recursos em que a rede virtual de destino será implantada, selecione   >  **grupo de recursos** básicos. 

    Se você precisar criar um novo grupo de recursos para a rede virtual de destino, selecione **criar novo**. Certifique-se de que o nome não seja o mesmo que o nome do grupo de recursos de origem na rede virtual existente.

1. Verifique se   >  **local** básico está definido como o local de destino onde você deseja que a rede virtual seja implantada.

1. Em **configurações**, verifique se o nome corresponde ao nome que você inseriu anteriormente no editor de parâmetros.

1. Marque a caixa de seleção **termos e condições** .

1. Para implantar a rede virtual de destino, selecione **comprar**.

## <a name="delete-the-target-virtual-network"></a>Excluir a rede virtual de destino

Para descartar a rede virtual de destino, você exclui o grupo de recursos que contém a rede virtual de destino. Para fazer isso:
1. No painel portal do Azure, selecione o grupo de recursos.
1. Na parte superior do painel **visão geral** , selecione **excluir**.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos. Para fazer isso:
1. No painel portal do Azure, selecione a rede virtual ou o grupo de recursos.
1. Na parte superior de cada painel, selecione **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu uma rede virtual do Azure de uma região para outra usando o portal do Azure e, em seguida, limpou os recursos de origem desnecessários. Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover máquinas virtuais do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)