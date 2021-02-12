---
title: Mover o NSG (grupo de segurança de rede) do Azure para outra região do Azure usando o portal do Azure
description: Use Azure Resource Manager modelo para mover o grupo de segurança de rede do Azure de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217270"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Mover o NSG (grupo de segurança de rede) do Azure para outra região usando o portal do Azure

Há vários cenários em que você deseja mover seu NSGs existente de uma região para outra. Por exemplo, talvez você queira criar um NSG com as mesmas regras de configuração e segurança para teste. Você também pode querer mover um NSG para outra região como parte do planejamento de recuperação de desastre.

Não é possível migrar os grupos de segurança do Azure de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar as regras de segurança e configuração existentes de um NSG.  Em seguida, você pode preparar o recurso em outra região exportando o NSG para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar o modelo na nova região.  Para obter mais informações sobre o Resource Manager e modelos, consulte [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o grupo de segurança de rede do Azure está na região do Azure da qual você deseja mover.

- Os grupos de segurança de rede do Azure não podem ser movidos entre regiões.  Você precisará associar o novo NSG aos recursos na região de destino.

- Para exportar uma configuração do NSG e implantar um modelo para criar um NSG em outra região, você precisará da função de colaborador de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite que você crie NSGs na região de destino que é usada. Contate o suporte para habilitar a cota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de NSGs para esse processo.  Veja [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o grupo de segurança de rede para a configuração e a regra de segurança mover usando um modelo do Resource Manager e mover as regras de configuração e segurança do NSG para a região de destino usando o Portal.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportar o modelo e implantar por meio do portal

1. Faça logon no [portal do Azure](https://portal.azure.com)  >  **grupos de recursos**.
2. Localize o grupo de recursos que contém o NSG de origem e clique nele.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo**  >  **Editar parâmetros** para abrir o **parameters.jsno** arquivo no editor online.
6. Para editar o parâmetro do nome NSG, altere a propriedade **Value** em **parâmetros**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Altere o valor NSG de origem no editor para um nome de sua escolha para o NSG de destino. Certifique-se de colocar o nome entre aspas.

8.  Clique em **salvar** no editor.

9.  Clique em **modelo**  >  **Editar modelo** para abrir o **template.jsno** arquivo no editor online.

10. Para editar a região de destino em que as regras de configuração e segurança do NSG serão movidas, altere a propriedade **local** em **recursos** no editor online:

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
           ]

    ```

11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central**  =  **centralus**.

12. Você também pode alterar outros parâmetros no modelo se quiser, e eles podem ser opcionais dependendo dos seus requisitos:

    * **Regras de segurança** – você pode editar quais regras são implantadas no NSG de destino adicionando ou removendo regras para a seção **securityRules** na **template.jsno** arquivo:

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
                    },
                ]
            }
        ```

      Para concluir a adição ou a remoção das regras no NSG de destino, você também deve editar os tipos de regra personalizada no final da **template.jsno** arquivo no formato do exemplo abaixo:

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

13. Clique em **salvar** no editor online.

14. Clique em  >  **assinatura** básica para escolher a assinatura na qual o NSG de destino será implantado.

15. Clique em  >  **grupo de recursos** básico para escolher o grupo de recursos no qual o NSG de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o NSG de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do NSG existente.

16. Verifique se o  >  **local** básico está definido como o local de destino onde você deseja que o NSG seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar o grupo de segurança de rede de destino.

## <a name="discard"></a>Descartar

Se você quiser descartar o NSG de destino, exclua o grupo de recursos que contém o NSG de destino.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação do NSG, exclua o NSG de origem ou o grupo de recursos. Para fazer isso, selecione o grupo de segurança de rede ou grupo de recursos no painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um grupo de segurança de rede do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)