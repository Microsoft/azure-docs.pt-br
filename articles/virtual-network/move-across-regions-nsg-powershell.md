---
title: Mover o NSG (grupo de segurança de rede) do Azure para outra região do Azure usando Azure PowerShell
description: Use Azure Resource Manager modelo para mover o grupo de segurança de rede do Azure de uma região do Azure para outra usando Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934995"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Mover o NSG (grupo de segurança de rede) do Azure para outra região usando Azure PowerShell

Há vários cenários em que você deseja mover seu NSGs existente de uma região para outra. Por exemplo, talvez você queira criar um NSG com as mesmas regras de configuração e segurança para teste. Você também pode querer mover um NSG para outra região como parte do planejamento de recuperação de desastre.

Não é possível migrar os grupos de segurança do Azure de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar as regras de segurança e configuração existentes de um NSG.  Em seguida, você pode preparar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo na nova região.  Para obter mais informações sobre o Gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o grupo de segurança de rede do Azure está na região do Azure da qual você deseja mover.

- Os grupos de segurança de rede do Azure não podem ser movidos entre regiões.  Você precisará associar o novo NSG aos recursos na região de destino.

- Para exportar uma configuração do NSG e implantar um modelo para criar um NSG em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie NSGs na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de NSGs para esse processo.  Veja [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o grupo de segurança de rede para a configuração e a regra de segurança mover usando um modelo do Resource Manager e mover as regras de configuração e segurança do NSG para a região de destino usando Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar de um script

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha a ID de recurso do NSG que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte o NSG de origem para um arquivo. JSON no diretório em que você executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando chamado **\<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do NSG, altere a propriedade **DefaultValue** do nome da NSG de origem para o nome do seu NSG de destino, verifique se o nome está entre aspas:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Para editar a região de destino em que as regras de configuração e segurança do NSG serão movidas, altere a propriedade **local** em **recursos**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Para obter códigos de localização de região, você pode usar o cmdlet do Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Você também pode alterar outros parâmetros no **\<resource-group-name> . JSON** se escolher e forem opcionais, dependendo dos seus requisitos:

    * **Regras de segurança** – você pode editar quais regras são implantadas no NSG de destino adicionando ou removendo regras para a seção **securityRules** no arquivo **\<resource-group-name> . JSON** :

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Para concluir a adição ou a remoção das regras no NSG de destino, você também deve editar os tipos de regra personalizada no final do arquivo **\<resource-group-name> . JSON** no formato do exemplo abaixo:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Salve o arquivo **\<resource-group-name>.json**.

10. Crie um grupo de recursos na região de destino para que o NSG de destino seja implantado usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Implante o arquivo **\<resource-group-name>.json** editado no grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Descartar 

Após a implantação, se você quiser reiniciar ou descartar o NSG no destino, exclua o grupo de recursos que foi criado no destino e o NSG movido será excluído.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpar

Para confirmar as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um grupo de segurança de rede do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
