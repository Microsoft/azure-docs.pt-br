---
title: Mover um balanceador de carga externo do Azure para outra região do Azure usando o portal do Azure
description: Use um modelo de Azure Resource Manager para mover um balanceador de carga externo de uma região do Azure para outra usando o portal do Azure.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94693742"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Mover um balanceador de carga externo para outra região usando o portal do Azure

Há vários cenários em que você deseja mover um balanceador de carga externo de uma região para outra. Por exemplo, talvez você queira criar outro balanceador externo de carga com a mesma configuração para teste. Você também pode querer mover um balanceador externo de carga para outra região como parte do planejamento de recuperação de desastre.

Em um sentido literal, você não pode mover um balanceador de carga externo do Azure de uma região para outra. Mas você pode usar um modelo de Azure Resource Manager para exportar a configuração existente e o endereço IP público de um balanceador de carga externo. Em seguida, você pode preparar o recurso em outra região exportando o balanceador de carga e o IP público para um modelo, modificando os parâmetros para corresponder à região de destino e, em seguida, implantando o modelo na nova região. Para obter mais informações sobre o Gerenciador de recursos e modelos, consulte [Exportar grupos de recursos para modelos](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se o balanceador de carga externo do Azure está na região do Azure da qual você deseja mover.

- Os balanceadores de carga externos do Azure não podem ser movidos entre regiões. Você precisará associar o novo balanceador de carga aos recursos na região de destino.

- Para exportar uma configuração de balanceador de carga externa e implantar um modelo para criar um balanceador de carga externo em outra região, você precisará receber a função de colaborador de rede ou superior.

- Identifique o layout de rede de origem e todos os recursos que você está usando atualmente. Esse layout inclui, mas não se limita a balanceadores de carga, grupos de segurança de rede, IPs públicos e redes virtuais.

- Verifique se sua assinatura do Azure permite criar balanceadores de carga externos na região de destino. Contate o suporte para habilitar a cota necessária.

- Verifique se sua assinatura tem recursos suficientes para dar suporte à adição dos balanceadores de carga. Veja [Assinatura do Azure e limites, cotas e restrições de serviço](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Preparar e mover
Os procedimentos a seguir mostram como preparar o balanceador externo de carga para a movimentação usando um modelo do Resource Manager e mover a configuração do balanceador de carga externo para a região de destino usando o portal do Azure. Primeiro, você deve exportar a configuração de IP público do balanceador de carga externo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportar o modelo de IP público e implantar o IP público do portal

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém o IP público de origem e selecione-o.
3. Selecione **configurações**  >  **Exportar modelo**.
4. Selecione **implantar** em **Exportar modelo**.
5. Selecione **modelo**  >  **Editar parâmetros** para abrir o parameters.jsno arquivo no editor online.
8. Para editar o parâmetro do nome do IP público, altere a propriedade **Value** em **parâmetros** do nome IP público de origem para o nome do seu IP público de destino. Coloque o nome entre aspas.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Selecione **salvar** no editor.

9.  Selecione **modelo**  >  **Editar modelo** para abrir o template.jsno arquivo no editor online.

10. Para editar a região de destino para a qual o IP público será movido, altere a propriedade **local** em **recursos**:

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
  
    Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços. Por exemplo, o código para EUA Central é **centralus**.
    
12. Você também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo de seus requisitos:

    * **SKU**. Você pode alterar a SKU do IP público na configuração de Standard para básico ou de básico para Standard alterando a propriedade **Name** em **SKU** no template.jsno arquivo:

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

        Para obter informações sobre as diferenças entre IPs públicos do SKU básico e Standard, consulte [criar, alterar ou excluir um endereço IP público](../virtual-network/virtual-network-public-ip-address.md).

    * **Método de alocação de IP público** e **tempo limite de ociosidade**. Você pode alterar o método de alocação de IP público alterando a propriedade **publicIPAllocationMethod** de **dinâmico** para **estático** ou de **estática** para **dinâmica**. Você pode alterar o tempo limite de ociosidade alterando a propriedade **idleTimeoutInMinutes** para o valor desejado. O padrão é **4**.

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

        ```

        Para obter informações sobre os métodos de alocação e os valores de tempo limite de ociosidade, consulte [criar, alterar ou excluir um endereço IP público](../virtual-network/virtual-network-public-ip-address.md).

 
13. Selecione **salvar** no editor online.

14. Selecione   >  **assinatura** básica para escolher a assinatura na qual o IP público de destino será implantado.

15. Selecione   >  **grupo de recursos** básicos para escolher o grupo de recursos no qual o IP público de destino será implantado. Você pode selecionar **criar novo** para criar um novo grupo de recursos para o IP público de destino. Verifique se o nome não é o mesmo que o grupo de recursos de origem do IP público de origem existente.

16. Verifique se   >  **local** básico está definido como o local de destino onde você deseja que o IP público seja implantado.

17. Em **configurações**, verifique se o nome corresponde ao nome que você inseriu anteriormente no editor de parâmetros.

18. Marque a caixa de seleção **termos e condições** .

19. Selecione **comprar** para implantar o IP público de destino.

20. Se você tiver outro IP público que está sendo usado para NAT de saída para o balanceador de carga que está sendo movido, repita as etapas anteriores para exportar e implantar o segundo IP público de saída na região de destino.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportar o modelo de balanceador de carga externo e implantar o balanceador de carga do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.
2. Localize o grupo de recursos que contém o balanceador de carga externo de origem e selecione-o.
3. Selecione **configurações**  >  **Exportar modelo**.
4. Selecione **implantar** em **Exportar modelo**.
5. Selecione **modelo**  >  **Editar parâmetros** para abrir o parameters.jsno arquivo no editor online.

5. Para editar o parâmetro do nome do balanceador de carga externo, altere a propriedade **Value** do nome do balanceador de carga externo de origem para o nome do balanceador de carga externo de destino. Coloque o nome entre aspas.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Para editar o valor do IP público de destino que você moveu nas etapas anteriores, primeiro você deve obter a ID do recurso e, em seguida, colá-la na parameters.jsno arquivo. Para obter a ID:

    1. Em outra guia ou janela do navegador, entre no [portal do Azure](https://portal.azure.com) e selecione **grupos de recursos**.
    2. Localize o grupo de recursos de destino que contém o IP público que você moveu nas etapas anteriores. Selecione-a.
    3. Selecione **Configurações** > **Propriedades**.
    4. Na folha à direita, realce a **ID do recurso** e copie-a para a área de transferência. Como alternativa, você pode selecionar **copiar para a área de transferência** à direita do caminho da **ID de recurso** .
    5. Cole a ID do recurso na propriedade **valor** no editor **Editar parâmetros** que está aberto na outra janela do navegador ou na guia:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Selecione **salvar** no editor online.


7.  Se você tiver configurado o NAT de saída e as regras de saída para o balanceador de carga, você verá uma terceira entrada nesse arquivo para a ID externa do IP público de saída. Repita as etapas anteriores na **região de destino** para obter a ID para o IP público de saída. Cole essa ID na parameters.jsno arquivo:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Selecione **modelo**  >  **Editar modelo** para abrir o template.jsno arquivo no editor online.
9.  Para editar a região de destino para a qual a configuração do balanceador de carga externo será movida, altere a propriedade **local** em **recursos** na template.jsno arquivo:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Para obter códigos de localização de região, confira [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços. Por exemplo, o código para EUA Central é **centralus**.

11. Você também pode alterar outros parâmetros no modelo se quiser ou precisar, dependendo de seus requisitos:

    * **SKU**. Você pode alterar a SKU do balanceador externo de carga na configuração de Standard para básico ou de básico para Standard alterando a propriedade **Name** em **SKU** no template.jsno arquivo:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Para obter informações sobre as diferenças entre os balanceadores de carga do SKU básico e Standard, consulte [visão geral do Azure Standard Load Balancer](./load-balancer-overview.md).

    * **Regras de balanceamento de carga**. Você pode adicionar ou remover regras de balanceamento de carga na configuração adicionando ou removendo entradas na seção **loadBalancingRules** do template.jsno arquivo:

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
       Para obter informações sobre regras de balanceamento de carga, consulte [o que é Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Investigações**. Você pode adicionar ou remover uma investigação para o balanceador de carga na configuração adicionando ou removendo entradas na seção **investigações** do template.jsno arquivo:

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
       Para obter mais informações, consulte [Load Balancer investigações de integridade](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Regras de NAT de entrada**. Você pode adicionar ou remover regras NAT de entrada para o balanceador de carga adicionando ou removendo entradas na seção **inboundNatRules** do template.jsno arquivo:

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
        Para concluir a adição ou remoção de uma regra NAT de entrada, a regra deve estar presente ou removida como uma propriedade de **tipo** no final da template.jsno arquivo:

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
        Para obter informações sobre regras de NAT de entrada, consulte [o que é Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Regras de saída**. Você pode adicionar ou remover regras de saída na configuração editando a propriedade **outboundRules** no template.jsno arquivo:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Para obter mais informações, consulte [Load Balancer regras de saída](./load-balancer-outbound-connections.md#outboundrules).

12. Selecione **salvar** no editor online.

13. Selecione   >  **assinatura** básica para escolher a assinatura na qual o balanceador de carga externo de destino será implantado.

15. Selecione   >  **grupo de recursos** básicos para escolher o grupo de recursos no qual o balanceador de carga de destino será implantado. Você pode selecionar **criar novo** para criar um novo grupo de recursos para o balanceador de carga externo de destino. Ou você pode escolher o grupo de recursos existente que você criou anteriormente para o IP público. Verifique se o nome não é o mesmo que o grupo de recursos de origem do balanceador externo de carga de origem existente.

16. Verifique se   >  **local** básico está definido como o local de destino onde você deseja que o balanceador de carga externo seja implantado.

17. Em **configurações**, verifique se o nome corresponde ao nome que você inseriu anteriormente no editor de parâmetros. Verifique se as IDs de recurso são populadas para qualquer IPs público na configuração.

18. Marque a caixa de seleção **termos e condições** .

19. Selecione **comprar** para implantar o IP público de destino.

## <a name="discard"></a>Descartar

Se você quiser descartar o IP público de destino e o balanceador de carga externo, exclua o grupo de recursos que os contém. Para fazer isso, selecione o grupo de recursos no painel no portal e, em seguida, selecione **excluir** na parte superior da página Visão geral.

## <a name="clean-up"></a>Limpeza

Para confirmar as alterações e concluir a movimentação do IP público e do balanceador de carga externo, exclua o IP público de origem e o balanceador de carga externo ou grupo de recursos. Para fazer isso, selecione esse grupo de recursos no painel no portal e, em seguida, selecione **excluir** na parte superior de cada página.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um balanceador de carga externo do Azure de uma região para outra e limpou os recursos de origem. Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover as VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)