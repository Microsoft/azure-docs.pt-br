---
title: Configurar o firewall de IP para o namespace de retransmissão do Azure
description: Este artigo descreve como usar regras de firewall para permitir conexões de endereços IP específicos para o namespace de Retransmissão do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ad8feed5df49dcc4503226a5fae50195bb9d48aa
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999498"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configurar firewall de IP para um namespace de Retransmissão do Azure
Por padrão, os namespaces de Retransmissão são acessíveis da Internet, desde que a solicitação acompanhe autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esse recurso é útil em cenários nos quais a Retransmissão do Azure deve ser acessível apenas de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usa a Retransmissão com o [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services), é possível criar uma **regra de firewall** para permitir o tráfego apenas de seus endereços IP da infraestrutura local. 


> [!IMPORTANT]
> Esse recurso está atualmente na visualização. 


## <a name="enable-ip-firewall-rules"></a>Habilitar regras de firewall de IP
As regras de firewall de IP são aplicadas no nível do namespace. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

### <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP para um namespace. 

1. Navegue até o **namespace de Retransmissão** no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione a opção **Rede**. Se você selecionar a opção **Todas as redes** na seção **Permitir acesso de**, o namespace de Retransmissão aceitará conexões de qualquer endereço IP. Essa configuração é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0. 

    ![Captura de tela mostra a página rede com a opção todas as redes selecionada.](./media/ip-firewall/all-networks-selected.png)
1. Para restringir o acesso a redes e endereços IP específicos, selecione a opção **Redes selecionadas**. Na seção **Firewall**, siga estas etapas:
    1. Selecione a opção **Adicionar o endereço IP do cliente** para permitir ao IP do cliente atual acesso ao namespace. 
    2. Para **intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereços IPv4 na notação CIDR. 

        ![Firewall – opção "Todas as redes" selecionada](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.


### <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Retransmissão existente.

O modelo usa um parâmetro: **ipMask**, que é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"** , que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "virtualNetworkRules": [],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outros recursos relacionados à segurança de rede, confira [Segurança de rede](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
