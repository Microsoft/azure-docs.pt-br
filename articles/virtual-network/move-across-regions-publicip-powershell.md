---
title: Mover a configuração de IP público do Azure para outra região do Azure usando o Azure PowerShell
description: Use o modelo do Azure Resource Manager para mover a configuração de IP público do Azure de uma região do Azure para outra usando o Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938690"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Mover a configuração de IP público do Azure para outra região usando o Azure PowerShell

Há vários cenários em que é possível mover as configurações de IP do Azure existentes de uma região para outra. Por exemplo, talvez você queira criar um IP com a mesma configuração e SKU para teste. Talvez você também queira mover uma configuração de IP para outra região como parte do planejamento de recuperação de desastre.

**Os IPs do Azure são específicos da região e não podem ser movidos de uma região para outra.** No entanto, você pode usar um modelo do Azure Resource Manager para exportar a configuração existente de um IP.  Em seguida, você pode preparar o recurso em outra região, exportando o IP para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região.  Para obter mais informações sobre o Resource Manager e os modelos, confira [Exportar grupos de recursos para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o IP do Azure está na região do Azure da qual você quer mover.

- Os IPs do Azure não podem ser movidos entre regiões.  Você precisará associar o novo IP aos recursos na região de destino.

- Para exportar uma configuração de IP e implantar um modelo para criar um IP em outra região, você precisará da função de colaborador de rede ou superior.
   
- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a, balanceadores de carga, NSGs (grupos de segurança de rede) e redes virtuais.

- Verifique se a assinatura do Azure permite criar IPs na região de destino usada. Contate o suporte para habilitar a cota necessária.

- Certifique-se de que sua assinatura tenha recursos suficientes para dar suporte à adição de IPs para esse processo.  Veja [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o IP para mover a configuração usando um modelo do Resource Manager, bem como mover a configuração de IP para a região de destino usando o Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportar o modelo e implantar de um script

1. Entre em sua assinatura do Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenha a ID do recurso do IP que você deseja mover para a região de destino e coloque-a em uma variável usando [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exporte a rede virtual de origem para um arquivo .json no diretório em que você executa o comando [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. O arquivo baixado será nomeado após o grupo de recursos do qual o recurso foi exportado.  Localize o arquivo que foi exportado do comando chamado **\<resource-group-name>.json** e abra-o em um editor de sua escolha:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Para editar o parâmetro do nome do IP, altere a propriedade **defaultValue** do nome do IP de origem para o nome do IP de destino, verifique se o nome está entre aspas:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Para editar a região de destino em que o IP será movido, altere a propriedade **location** em recursos:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Para obter códigos de localização de região, você pode usar o cmdlet do Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) executando o seguinte comando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Você também pode alterar outros parâmetros no modelo se quiser, e eles podem ser opcionais dependendo dos seus requisitos:

    * **SKU** – você pode alterar a SKU do IP na configuração de Standard para Básico ou de Básico para Standard alterando a propriedade **sku** > **name** no arquivo **\<resource-group-name>.json**:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Para obter mais informações sobre as diferenças entre IPs de SKU Básico e Standard, confira [Criar, alterar ou excluir um endereço IP](./virtual-network-public-ip-address.md).

    * **Método de alocação de IP** e **tempo limite ocioso** – você pode alterar ambas as opções no modelo alterando a propriedade **publicIPAllocationMethod** de **Dinâmico** para **Estático** ou de **Estático** para **Dinâmico**. O tempo limite ocioso pode ser alterado alterando a propriedade **idleTimeoutInMinutes** para o valor desejado.  O padrão é **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Para obter mais informações sobre os métodos de alocação e os valores de tempo limite ocioso, confira [Criar, alterar ou excluir um endereço IP](./virtual-network-public-ip-address.md).


9. Salve o arquivo **\<resource-group-name>.json**.

10. Crie um grupo de recursos na região de destino para o IP de destino a ser implantado usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Implante o arquivo **\<resource-group-name>.json** editado no grupo de recursos criado na etapa anterior usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Para verificar se os recursos foram criados na região de destino, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) e [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Descartar 

Após a implantação, se você quiser iniciar ou descartar o IP no destino, exclua o grupo de recursos que foi criado no destino, e o IP movido será excluído.  Para remover o grupo de recursos, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Limpar

Para confirmar as alterações e concluir a movimentação da rede virtual, exclua a rede virtual de origem ou o grupo de recursos e use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um IP do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
