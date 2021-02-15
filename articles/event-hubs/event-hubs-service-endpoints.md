---
title: Pontos de extremidade de serviço de Rede Virtual - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto de extremidade de serviço do Microsoft. EventHub a uma rede virtual.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: f725c4f4d94cbf7d0463ce49c1d2809444ef6f7a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516666"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Permitir acesso aos namespaces dos hubs de eventos do Azure de redes virtuais específicas 

A integração de Hubs de Eventos com [Pontos de extremidade de serviço de VNet (rede virtual)][vnet-sep] permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace de hubs de eventos não aceitará mais o tráfego de qualquer lugar, mas de sub-redes autorizadas em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de Hubs de Eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens. 

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Hubs de Eventos, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público. Há uma exceção a esse comportamento. Habilitar um ponto de extremidade de serviço, por padrão, habilita a `denyall` regra no [Firewall IP](event-hubs-ip-filtering.md) associado à rede virtual. Você pode adicionar endereços IP específicos no firewall de IP para habilitar o acesso ao ponto de extremidade público do hub de eventos. 

>[!WARNING]
> A habilitação de redes virtuais para seu namespace de hubs de eventos bloqueia solicitações de entrada por padrão, a menos que as solicitações sejam originadas de um serviço operando de redes virtuais permitidas. Solicitações que estão bloqueadas incluem as de outros serviços do Azure, do portal do Azure, de registro em log e serviços de métricas e assim por diante. Como exceção, você pode permitir o acesso a recursos de hubs de eventos de determinados serviços confiáveis mesmo quando as redes virtuais estiverem habilitadas. Para obter uma lista de serviços confiáveis, consulte [serviços confiáveis](#trusted-microsoft-services).

> [!NOTE]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não há suporte na camada **básica** .

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança rígida e segmentada e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentalizados, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação isolados, em que as mensagens são até mesmo gravadas no disco à medida que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Hubs de Eventos podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associar hubs de eventos a redes virtuais

As **regras da rede virtual** são o recurso de segurança do firewall que controla se o namespace de Hubs de Eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Hubs de Eventos a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** na sub-rede de uma rede virtual e habilitá-la para **Microsoft. EventHub** , conforme explicado no artigo [visão geral do ponto de extremidade de serviço][vnet-sep] . Depois de adicionar o ponto de extremidade de serviço, vincule o namespace de hubs de eventos a ele com uma **regra de rede virtual**.

A regra da rede virtual é uma associação do namespace de Hubs de Eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Hubs de Eventos. Os hubs de eventos em si nunca estabelecem conexões de saída, não precisa obter acesso e, portanto, nunca concedem acesso à sua sub-rede habilitando essa regra.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar portal do Azure para adicionar um ponto de extremidade de serviço de rede virtual. Para limitar o acesso, você precisa integrar o ponto de extremidade de serviço de rede virtual para este namespace de hubs de eventos.

1. Navegue até o seu **namespace dos Hubs de Eventos** no [portal do Azure](https://portal.azure.com).
4. Selecione **rede** em **configurações** no menu à esquerda. Você vê a guia **rede** somente para namespaces **padrão** ou **dedicados** . 

    > [!WARNING]
    > Se você selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nessa página, o namespace poderá ser acessado via **Internet pública** (usando a tecla de acesso). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Guia redes – opção redes selecionadas" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Se você selecionar a opção **todas as redes** , o Hub de eventos aceitará conexões de qualquer endereço IP (usando a tecla de acesso). Essa configuração é equivalente a uma regra que aceita o intervalo de endereço IP 0.0.0.0/0. 

    ![Firewall – opção "Todas as redes" selecionada](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a redes específicas, selecione a opção **redes selecionadas** na parte superior da página, se ela ainda não estiver selecionada.
2. Na seção **rede virtual** da página, selecione **+ Adicionar rede virtual existente** _. Selecione _ *+ criar nova rede virtual** se desejar criar uma nova VNet. 

    ![adicionar rede virtual existente](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Selecione a rede virtual na lista de redes virtuais e, em seguida, escolha a **sub-rede**. Você precisa habilitar o ponto de extremidade de serviço antes de adicionar a rede virtual à lista. Se o ponto de extremidade de serviço não estiver habilitado, o portal solicitará que você o habilite.
   
   ![selecionar sub-rede](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Você deverá ver a seguinte mensagem bem-sucedida depois que o ponto de extremidade de serviço para a sub-rede estiver habilitado para **Microsoft. EventHub**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar a sub-rede e habilitar o ponto de extremidade](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não for possível habilitar o ponto de extremidade de serviço, você poderá ignorar o ponto de extremidade de serviço de rede virtual ausente usando o modelo do Resource Manager. Essa funcionalidade não está disponível no portal.
5. Especifique se você deseja **permitir que serviços confiáveis da Microsoft ignorem esse firewall**. Consulte [serviços confiáveis da Microsoft](#trusted-microsoft-services) para obter detalhes. 
6. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do portal.

    ![Salvar rede](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Para restringir o acesso a intervalos ou endereços IP específicos, consulte [permitir o acesso de intervalos ou endereços IP específicos](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
O modelo do Gerenciador de recursos de exemplo a seguir adiciona uma regra de rede virtual a um namespace existente de hubs de eventos. Para a regra de rede, ele especifica a ID de uma sub-rede em uma rede virtual. 

A ID é um caminho totalmente qualificado do Resource Manager para a sub-rede da rede virtual. Por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem IP de Hubs de Eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
