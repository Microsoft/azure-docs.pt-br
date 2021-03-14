---
title: Regras de firewall dos Hubs de Eventos do Azure| Microsoft Docs
description: Use as Regras de firewall para permitir conexões de endereços IP específicos com os Hubs de Eventos do Azure.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: ca5995c3e1b9923d925ddc4deae299c28261d18a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560848"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Permitir acesso aos namespaces dos hubs de eventos do Azure de intervalos ou endereços IP específicos
Por padrão, os namespaces dos Hubs de Eventos são acessíveis da Internet, desde que a solicitação acompanhe autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esse recurso é útil em cenários nos quais os Hubs de Eventos do Azure devem ser acessíveis apenas de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usa os Hubs de Eventos com o [Azure ExpressRoute][express-route], é possível criar uma **regra de firewall** para permitir o tráfego apenas de seus endereços IP da infraestrutura local. 

>[!WARNING]
> Ativar as regras de firewall para seu namespace de hubs de eventos bloqueia solicitações de entrada por padrão, a menos que as solicitações sejam originadas de um serviço operando de endereços IP públicos permitidos. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante. Como exceção, você pode permitir o acesso a recursos de hubs de eventos de determinados serviços confiáveis mesmo quando a filtragem de IP está habilitada. Para obter uma lista de serviços confiáveis, consulte [serviços confiáveis da Microsoft](#trusted-microsoft-services).

> [!IMPORTANT]
> Especifique pelo menos uma regra de IP ou regra de rede virtual para o namespace para permitir o tráfego somente dos endereços IP ou da sub-rede de uma rede virtual especificada. Se não houver nenhuma regra de rede virtual e IP, o namespace poderá ser acessado pela Internet pública (usando a chave de acesso).  


## <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall de IP são aplicadas no nível do namespace dos Hubs de Eventos. Portanto, as regras se aplicam a todas as conexões de clientes usando qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace dos hubs de eventos é rejeitada como não autorizada. A resposta não menciona a regra de IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP para um namespace dos Hubs de Eventos. 

1. Navegue até o seu **namespace dos Hubs de Eventos** no [portal do Azure](https://portal.azure.com).
4. Selecione **rede** em **configurações** no menu à esquerda. Você vê a guia **rede** somente para namespaces **padrão** ou **dedicados** . 
    
    > [!WARNING]
    > Se você selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nessa página, o namespace poderá ser acessado via **Internet pública** (usando a tecla de acesso).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Guia redes – opção redes selecionadas" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se você selecionar a opção **todas as redes** , o Hub de eventos aceitará conexões de qualquer endereço IP (usando a tecla de acesso). Essa configuração é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0. 

    ![Captura de tela que mostra a página "firewall e redes virtuais" com a opção "todas as redes" selecionada.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a endereços IP específicos, confirme se a opção **redes selecionadas** está selecionada. Na seção **Firewall**, siga estas etapas:
    1. Selecione a opção **Adicionar o endereço IP do cliente** para permitir ao IP do cliente atual acesso ao namespace. 
    2. Para **intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereços IPv4 na notação CIDR. 

    >[!WARNING]
    > Se você selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nessa página, o namespace poderá ser acessado pela Internet pública (usando a tecla de acesso).
1. Especifique se você deseja **permitir que serviços confiáveis da Microsoft ignorem esse firewall**. Consulte [serviços confiáveis da Microsoft](#trusted-microsoft-services) para obter detalhes. 

      ![Firewall – opção "Todas as redes" selecionada](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.

    > [!NOTE]
    > Para restringir o acesso a redes virtuais específicas, consulte [permitir o acesso de redes específicas](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

> [!IMPORTANT]
> As regras de firewall têm suporte nas camadas **Standard** e **Dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Hubs de Eventos existente.

**ipMask** no modelo é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

Ao adicionar regras de rede virtual ou firewalls, defina o valor de `defaultAction` como `Deny` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
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
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
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
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Se não houver nenhuma regra de rede virtual e IP, todo o tráfego fluirá para o namespace mesmo se você definir `defaultAction` como `deny` .  O namespace pode ser acessado pela Internet pública (usando a chave de acesso). Especifique pelo menos uma regra de IP ou regra de rede virtual para o namespace para permitir o tráfego somente dos endereços IP ou da sub-rede de uma rede virtual especificada.  

## <a name="next-steps"></a>Próximas etapas

Para restringir o acesso a Hubs de Eventos para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para Hubs de Eventos][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
