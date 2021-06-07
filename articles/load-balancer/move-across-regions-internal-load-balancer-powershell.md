---
title: Mover o Load Balancer interno do Azure para outra região do Azure usando Azure PowerShell
description: Use Azure Resource Manager modelo para mover o Load Balancer interno do Azure de uma região do Azure para outra usando Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 2c89ad69207a51a92b56d268c685aa2be4118cf1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507578"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Mover o Load Balancer interno do Azure para outra região usando o PowerShell

Há vários cenários em que você deseja mover o balanceador de carga interno existente de uma região para outra. Por exemplo, talvez você queira criar um balanceador de carga interno com a mesma configuração para teste. Você também pode querer mover um balanceador de carga interno para outra região como parte do planejamento de recuperação de desastre.

Os balanceadores de carga internos do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar a configuração existente e a rede virtual de um balanceador de carga interno.  Em seguida, você pode preparar o recurso em outra região exportando o balanceador de carga e a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar os modelos na nova região.  Para obter mais informações sobre o Resource Manager e os modelos, confira [Exportar grupos de recursos para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o balanceador de carga interno do Azure está na região do Azure da qual você deseja mover.

- Os balanceadores de carga internos do Azure não podem ser movidos entre regiões.  Você precisará associar o novo balanceador de carga aos recursos na região de destino.

- Para exportar uma configuração de balanceador de carga interna e implantar um modelo para criar um balanceador de carga interno em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não está limitado a balanceadores de carga, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga internos na região de destino usada. Contate o suporte para habilitar a cota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de balanceadores de carga para esse processo.  Confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o balanceador de carga interno para a movimentação usando um modelo do Resource Manager e mover a configuração do balanceador de carga interno para a região de destino usando Azure PowerShell.  Como parte desse processo, a configuração de rede virtual do balanceador de carga interno deve ser incluída e deve ser feita primeiro antes de mover o balanceador de carga interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de rede virtual e implantar do Azure PowerShell

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Obtenha a ID de recurso da rede virtual que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um arquivo .json no diretório em que você executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando chamado **\<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome da rede virtual, altere a propriedade **DefaultValue** do nome da rede virtual de origem para o nome da sua rede virtual de destino, verifique se o nome está entre aspas:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Para editar a região de destino em que a VNET será movida, altere a propriedade **local** em recursos:

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
  
7. Para obter códigos de localização de região, você pode usar o cmdlet do Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Você também pode alterar outros parâmetros no arquivo **\<resource-group-name> . JSON** se escolher e forem opcionais, dependendo dos seus requisitos:

    * **Espaço de endereço** -o espaço de endereço da VNET pode ser alterado antes de salvar modificando a seção **recursos de**  >  **addressSpace** e alterando a propriedade **addressPrefixes** no arquivo **\<resource-group-name> . JSON** :

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

    * **Sub** -rede-o nome da sub-rede e o espaço de endereço de sub-rede podem ser alterados ou adicionados ao modificando a seção **sub-redes** do arquivo **\<resource-group-name> . JSON** . O nome da sub-rede pode ser alterado alterando a propriedade **Name** . O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **addressPrefix** no arquivo **\<resource-group-name> . JSON** :

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

         No arquivo **\<resource-group-name> . JSON** , para alterar o prefixo do endereço, ele deve ser editado em dois locais, na seção listada acima e na seção **tipo** listada abaixo.  Altere a propriedade **addressPrefix** para corresponder à que está acima:

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

9.  Salve o arquivo **\<resource-group-name>.json**.

10. Criar um grupo de recursos na região de destino para a VNET de destino a ser implantada usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implante o arquivo **\<resource-group-name>.json** editado no grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de balanceador de carga interno e implantar do Azure PowerShell

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha a ID do recurso do balanceador de carga interno que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a configuração do balanceador de carga interno de origem para um arquivo. JSON no diretório em que você executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando chamado **\<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do balanceador de carga interno, altere a propriedade **DefaultValue** do nome do balanceador de carga interno de origem para o nome do balanceador de carga interno de destino, verifique se o nome está entre aspas:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Para editar o valor da rede virtual de destino que foi movida acima, primeiro você deve obter a ID do recurso e, em seguida, copiá-la e colá-la no arquivo **\<resource-group-name> . JSON** .  Para obter a ID, use [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Digite a variável e pressione ENTER para exibir a ID do recurso.  Realce o caminho da ID e copie-o para a área de transferência:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  No arquivo **\<resource-group-name> . JSON** , Cole a **ID do recurso** da variável no lugar do **DEFAULTVALUE** no segundo parâmetro para a ID de rede virtual de destino, certifique-se de colocar o caminho entre aspas:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Para editar a região de destino em que a configuração do balanceador de carga interno será movida, altere a propriedade **local** em **recursos** no arquivo **\<resource-group-name> . JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Para obter códigos de localização de região, você pode usar o cmdlet do Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Você também pode alterar outros parâmetros no modelo se quiser, e eles podem ser opcionais dependendo dos seus requisitos:
    
    * **SKU** -você pode alterar a SKU do balanceador de carga interno na configuração de Standard para básico ou básico para Standard, alterando a propriedade   >  **nome** do SKU no arquivo **\<resource-group-name> . JSON** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter mais informações sobre as diferenças entre os balanceadores de carga do SKU básico e Standard, consulte [visão geral do Azure Standard Load Balancer](./load-balancer-overview.md)

    * **Regras de balanceamento de carga** – você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalancingRules** do arquivo **\<resource-group-name> . JSON** :

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Para obter mais informações sobre regras de balanceamento de carga, consulte [o que é Azure Load Balancer?](./load-balancer-overview.md)

    * **Investigações** – você pode adicionar ou remover uma investigação para o balanceador de carga na configuração adicionando ou removendo entradas para a seção **investigações** do arquivo **\<resource-group-name> . JSON** :

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Para obter mais informações sobre Azure Load Balancer investigações de integridade, consulte [investigações de integridade de Load Balancer](./load-balancer-custom-probe-overview.md)

    * **Regras de NAT de entrada** -você pode adicionar ou remover regras de NAT de entrada para o balanceador de carga adicionando ou removendo entradas para a seção **inboundNatRules** do arquivo **\<resource-group-name> . JSON** :

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Para concluir a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final do arquivo **\<resource-group-name> . JSON** :

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Para obter mais informações sobre regras de NAT de entrada, consulte [o que é Azure Load Balancer?](./load-balancer-overview.md)
    
13. Salve o arquivo **\<resource-group-name>.json**.
    
10. Crie ou um grupo de recursos na região de destino para o balanceador de carga interno de destino a ser implantado usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O grupo de recursos existente acima também pode ser reutilizado como parte desse processo:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implante o arquivo **\<resource-group-name>.json** editado no grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Descartar 

Após a implantação, se você quiser iniciar ou descartar a rede virtual e o balanceador de carga no destino, exclua o grupo de recursos que foi criado no destino e a rede virtual movida e o balanceador de carga serão excluídos.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Limpar

Para confirmar as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) e [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um balanceador de carga interno do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)