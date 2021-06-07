---
title: Mover o Load Balancer interno do Azure para outra região do Azure usando o portal do Azure
description: Use Azure Resource Manager modelo para mover o Load Balancer interno do Azure de uma região do Azure para outra usando o portal do Azure
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698462"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Mover o Load Balancer interno do Azure para outra região usando o portal do Azure

Há vários cenários em que você deseja mover o balanceador de carga interno existente de uma região para outra. Por exemplo, talvez você queira criar um balanceador de carga interno com a mesma configuração para teste. Você também pode querer mover um balanceador de carga interno para outra região como parte do planejamento de recuperação de desastre.

Os balanceadores de carga internos do Azure não podem ser movidos de uma região para outra. No entanto, você pode usar um modelo de Azure Resource Manager para exportar a configuração existente e a rede virtual de um balanceador de carga interno.  Em seguida, você pode preparar o recurso em outra região exportando o balanceador de carga e a rede virtual para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantar os modelos na nova região.  Para obter mais informações sobre o Resource Manager e modelos, consulte [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o balanceador de carga interno do Azure está na região do Azure da qual você deseja mover.

- Os balanceadores de carga internos do Azure não podem ser movidos entre regiões.  Você precisará associar o novo balanceador de carga aos recursos na região de destino.

- Para exportar uma configuração de balanceador de carga interna e implantar um modelo para criar um balanceador de carga interno em outra região, você precisará da função de colaborador de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não está limitado a balanceadores de carga, grupos de segurança de rede, máquinas virtuais e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga internos na região de destino usada. Contate o suporte para habilitar a cota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição de balanceadores de carga para esse processo.  Confira [assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Preparar e mover
As etapas a seguir mostram como preparar o balanceador de carga interno para a movimentação usando um modelo do Resource Manager e mover a configuração do balanceador de carga interno para a região de destino usando o portal do Azure.  Como parte desse processo, a configuração de rede virtual do balanceador de carga interno deve ser incluída e deve ser feita primeiro antes de mover o balanceador de carga interno.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportar o modelo de rede virtual e implantar do portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com)  >  **grupos de recursos**.
2. Localize o grupo de recursos que contém a rede virtual de origem e clique nele.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo**  >  **Editar parâmetros** para abrir o **parameters.jsno** arquivo no editor online.
6. Para editar o parâmetro do nome da rede virtual, altere a propriedade **Value** em **parâmetros**:

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
7. Altere o valor de nome de rede virtual de origem no editor para um nome de sua escolha para a VNET de destino. Certifique-se de colocar o nome entre aspas.

8. Clique em **salvar** no editor.

9. Clique em **modelo**  >  **Editar modelo** para abrir o **template.jsno** arquivo no editor online.

10. Para editar a região de destino em que a VNET será movida, altere a propriedade **local** em recursos:

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

11. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central**  =  **centralus**.

12. Você também pode alterar outros parâmetros na **template.jsno** arquivo se escolher e forem opcionais, dependendo dos seus requisitos:

    * **Espaço de endereço** -o espaço de endereço da VNET pode ser alterado antes de salvar, modificando a seção **recursos de**  >  **addressSpace** e alterando a propriedade **addressPrefixes** no **template.jsno** arquivo:

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

    * **Sub** -rede-o nome da sub-rede e o espaço de endereço de sub-rede podem ser alterados ou adicionados ao modificando a seção **sub-redes** do **template.jsno** arquivo. O nome da sub-rede pode ser alterado alterando a propriedade **Name** . O espaço de endereço de sub-rede pode ser alterado alterando a propriedade **addressPrefix** no **template.jsno** arquivo:

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

         No **template.jsno** arquivo, para alterar o prefixo do endereço, ele deve ser editado em dois locais, na seção listada acima e na seção **tipo** listada abaixo.  Altere a propriedade **addressPrefix** para corresponder à que está acima:

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

13. Clique em **salvar** no editor online.

14. Clique em  >  **assinatura** básica para escolher a assinatura na qual a VNET de destino será implantada.

15. Clique em  >  **grupo de recursos** básico para escolher o grupo de recursos em que a VNET de destino será implantada.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para a VNET de destino.  Verifique se o nome não é o mesmo que o grupo de recursos de origem da VNET existente.

16. Verifique se o  >  **local** básico está definido como o local de destino onde você deseja que a VNET seja implantada.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar a rede virtual de destino.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportar o modelo de balanceador de carga interno e implantar do Azure PowerShell

1. Faça logon no [portal do Azure](https://portal.azure.com)  >  **grupos de recursos**.
2. Localize o grupo de recursos que contém o balanceador de carga interno de origem e clique nele.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **implantar** na folha **Exportar modelo** .
5. Clique em **modelo**  >  **Editar parâmetros** para abrir o **parameters.jsno** arquivo no editor online.

6. Para editar o parâmetro do nome do balanceador de carga interno, altere a propriedade **DefaultValue** do nome do balanceador de carga interno de origem para o nome do balanceador de carga interno de destino, verifique se o nome está entre aspas:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Para editar o valor da rede virtual de destino que foi movida acima, primeiro você deve obter a ID do recurso e, em seguida, copiá-la e colá-la na **parameters.jsno** arquivo. Para obter a ID:

    1. Faça logon no [portal do Azure](https://portal.azure.com)  >  **grupos de recursos** em outra guia ou janela do navegador.
    2. Localize o grupo de recursos de destino que contém a rede virtual movida das etapas acima e clique nela.
    3. Selecione **configurações** de >  >  **Propriedades**.
    4. Na folha à direita, realce a **ID do recurso** e copie-a para a área de transferência.  Como alternativa, você pode clicar no botão **copiar para a área de transferência** à direita do caminho da **ID de recurso** .
    5. Cole a ID do recurso na propriedade **DefaultValue** no editor de **Editar parâmetros** aberto na outra janela do navegador ou na guia:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Clique em **salvar** no editor online.

7. Clique em **modelo**  >  **Editar modelo** para abrir o **template.jsno** arquivo no editor online.
8. Para editar a região de destino em que a configuração do balanceador de carga interno será movida, altere a propriedade **local** em **recursos** na **template.jsno** arquivo:

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

9.  Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).  O código de uma região é o nome da região sem espaços, **EUA Central**  =  **centralus**.

10. Você também pode alterar outros parâmetros no modelo se quiser, e eles podem ser opcionais dependendo dos seus requisitos:

    * **SKU** -você pode alterar a SKU do balanceador de carga interno na configuração de Standard para básico ou básico para Standard, alterando a propriedade   >  **nome** do SKU na **template.jsno** arquivo:

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

    * **Regras de balanceamento de carga** – você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalancingRules** do **template.jsno** arquivo:

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

    * **Investigações** – você pode adicionar ou remover uma investigação para o balanceador de carga na configuração adicionando ou removendo entradas na seção **investigações** do **template.jsno** arquivo:

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

    * **Regras de NAT de entrada** -você pode adicionar ou remover regras de NAT de entrada para o balanceador de carga adicionando ou removendo entradas para a seção **inboundNatRules** do **template.jsno** arquivo:

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
        Para concluir a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final da **template.jsno** arquivo:

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

12. Clique em **salvar** no editor online.

13. Clique em  >  **assinatura** básica para escolher a assinatura na qual o balanceador de carga interno de destino será implantado.

15. Clique em  >  **grupo de recursos** básico para escolher o grupo de recursos em que o balanceador de carga de destino será implantado.  Você pode clicar em **criar novo** para criar um novo grupo de recursos para o balanceador de carga interno de destino ou escolher o grupo de recursos existente que foi criado acima para a rede virtual.  Verifique se o nome não é o mesmo que o grupo de recursos de origem do balanceador de carga interno de origem existente.

16. Verifique se o  >  **local** básico está definido como o local de destino onde você deseja que o balanceador de carga interno seja implantado.

17. Verifique em **configurações** que o nome corresponde ao nome que você inseriu no editor de parâmetros acima.  Verifique se as IDs de recurso estão preenchidas para todas as redes virtuais na configuração.

18. Marque a caixa em **termos e condições**.

19. Clique no botão **comprar** para implantar a rede virtual de destino.

## <a name="discard"></a>Descartar

Se você quiser descartar a rede virtual de destino e o balanceador de carga interno, exclua o grupo de recursos que contém a rede virtual de destino e o balanceador de carga interno.  Para fazer isso, selecione o grupo de recursos do seu painel no portal e selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação da rede virtual e do balanceador de carga interno, exclua a rede virtual de origem e o balanceador de carga interno ou grupo de recursos. Para fazer isso, selecione a rede virtual e o balanceador de carga interno ou grupo de recursos do seu painel no portal e selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um balanceador de carga interno do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)