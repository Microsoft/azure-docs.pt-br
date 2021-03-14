---
title: Configurar pontos de extremidade de serviço de rede virtual para o barramento de serviço do Azure
description: Este artigo fornece informações sobre como adicionar um ponto de extremidade de serviço Microsoft. ServiceBus a uma rede virtual.
ms.topic: article
ms.date: 02/12/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 2e00c9429ab3e39f95bc5ce6df072a99e4f02b86
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559575"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Permitir acesso ao namespace do barramento de serviço do Azure de redes virtuais específicas
A integração do barramento de serviço com [pontos de extremidade de serviço de rede virtual (VNet)][vnet-sep] permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Uma vez configurado para ser associado a pelo menos um ponto de extremidade de serviço de sub-rede de rede virtual, o respectivo namespace do barramento de serviço não aceitará mais tráfego de qualquer lugar, mas redes virtuais autorizadas e, opcionalmente, endereços IP específicos da Internet. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

Implementar a integração de redes virtuais pode impedir que outros serviços do Azure interajam com o Barramento de Serviço. Como exceção, você pode permitir o acesso aos recursos do barramento de serviço de determinados serviços confiáveis, mesmo quando os pontos de extremidade de serviço de rede estão habilitados. Para obter uma lista de serviços confiáveis, consulte [serviços confiáveis](#trusted-microsoft-services).

Os serviços da Microsoft a seguir devem estar em uma rede virtual
- Serviço de aplicativo do Azure
- Funções do Azure

As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md). Ao usar pontos de extremidade de serviço de VNet com o barramento de serviço, você não deve habilitar esses pontos de extremidade em aplicativos que misturam namespaces de barramento de serviço de camada Standard e Premium. Porque a camada Standard não oferece suporte a VNets. O ponto de extremidade é restrito somente a namespaces da camada Premium.

> [!IMPORTANT]
> Especifique pelo menos uma regra de IP ou regra de rede virtual para o namespace para permitir o tráfego somente dos endereços IP ou da sub-rede de uma rede virtual especificada. Se não houver nenhuma regra de rede virtual e IP, o namespace poderá ser acessado pela Internet pública (usando a chave de acesso).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Associar Barramento de Serviço a Redes Virtuais

As *regras da rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-lo para **Microsoft. ServiceBus** , conforme explicado na [visão geral do ponto de extremidade de serviço][vnet-sep]. Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

> [!NOTE]
> Lembre-se de que um ponto de extremidade de serviço de rede fornece aplicativos em execução na rede virtual o acesso ao namespace do barramento de serviço. A rede virtual controla a acessibilidade do ponto de extremidade, mas não as operações que podem ser feitas em entidades do barramento de serviço (filas, tópicos ou assinaturas). Use Azure Active Directory (AD do Azure) para autorizar operações que os aplicativos podem executar no namespace e suas entidades. Para obter mais informações, consulte [autenticar e autorizar um aplicativo com o Azure ad para acessar entidades do barramento de serviço](authenticate-application.md).


## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar portal do Azure para adicionar um ponto de extremidade de serviço de rede virtual. Para limitar o acesso, você precisa integrar o ponto de extremidade de serviço de rede virtual para este namespace de hubs de eventos.

1. Navegue até o **namespace do Barramento de Serviço** no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione a opção **rede** em **configurações**.  

    > [!NOTE]
    > Você vê a guia **rede** somente para namespaces **Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Página de rede-padrão" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Se você selecionar a opção **todas as redes** , o namespace do barramento de serviço aceitará conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall – opção "Todas as redes" selecionada](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Para restringir o acesso a redes virtuais específicas, selecione a opção **redes selecionadas** se ela ainda não estiver selecionada.
1. Na seção **rede virtual** da página, selecione **+ Adicionar rede virtual existente**. 

    ![adicionar rede virtual existente](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > Se você selecionar a opção **redes selecionadas** e não adicionar pelo menos uma regra de firewall IP ou uma rede virtual nessa página, o namespace poderá ser acessado pela Internet pública (usando a tecla de acesso).
3. Selecione a rede virtual na lista de redes virtuais e, em seguida, escolha a **sub-rede**. Você precisa habilitar o ponto de extremidade de serviço antes de adicionar a rede virtual à lista. Se o ponto de extremidade de serviço não estiver habilitado, o portal solicitará que você o habilite.
   
   ![selecionar sub-rede](./media/service-endpoints/select-subnet.png)

4. Você deverá ver a seguinte mensagem bem-sucedida depois que o ponto de extremidade de serviço para a sub-rede estiver habilitado para **Microsoft. ServiceBus**. Selecione **Adicionar** na parte inferior da página para adicionar a rede. 

    ![selecionar a sub-rede e habilitar o ponto de extremidade](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Se não for possível habilitar o ponto de extremidade de serviço, você poderá ignorar o ponto de extremidade de serviço de rede virtual ausente usando o modelo do Resource Manager. Essa funcionalidade não está disponível no portal.
6. Selecione **Salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal. O botão **salvar** deve ser desabilitado. 

    ![Salvar rede](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Para obter instruções sobre como permitir o acesso de intervalos ou endereços IP específicos, consulte [permitir o acesso de intervalos ou endereços IP específicos](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
O modelo do Resource Manager de exemplo a seguir adiciona uma regra de rede virtual a um namespace de barramento de serviço existente. Para a regra de rede, ele especifica a ID de uma sub-rede em uma rede virtual. 

A ID é um caminho totalmente qualificado do Resource Manager para a sub-rede da rede virtual. Por exemplo, `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

Ao adicionar regras de rede virtual ou firewalls, defina o valor de `defaultAction` como `Deny` .

Modelo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Filtragem IP do Barramento de Serviço do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
