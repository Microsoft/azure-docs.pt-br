---
title: 'Tutorial: Configurar o emparelhamento para o circuito do ExpressRoute – CLI do Azure'
description: Este tutorial mostra como criar e provisionar o emparelhamento público, privado e da Microsoft de um circuito do ExpressRoute. Este artigo também mostra como verificar o status, atualizar ou excluir emparelhamentos de seu circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2c56e847e3b112d50285cd2c116c8f22efbc507f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715523"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Tutorial: Criar e modificar o emparelhamento de um circuito do ExpressRoute usando a CLI

Esse tutorial mostra como criar e gerenciar a configuração de roteamento/emparelhamento de um circuito do ExpressRoute no modelo de implantação do Resource Manager usando a CLI. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> - Configurar, atualizar e excluir o emparelhamento da Microsoft para um circuito
> - Configurar, atualizar e excluir o emparelhamento privado do Azure para um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Leia as páginas de [pré-requisitos](expressroute-prerequisites.md), [requisitos de roteamento](expressroute-routing.md) e [fluxo de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [Criar um circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito do ExpressRoute precisa estar em um estado provisionado e habilitado para você executar os comandos neste artigo.

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

Você pode configurar o emparelhamento privado e o emparelhamento da Microsoft para um circuito do ExpressRoute. Os emparelhamentos poderão ser configurados em qualquer ordem que você escolher. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 


### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Instale a versão mais recente do CLI do Azure. Use a versão mais recente da CLI (interface de linha de comando) do Azure.

   ```azurecli
   az login
   ```

   Selecione a assinatura para a qual você deseja criar um circuito do ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

1. Criar um circuito do ExpressRoute. Siga as instruções para criar um [circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com a configuração usando as próximas etapas. 

1. Verifique se o circuito do ExpressRoute está provisionado e também habilitado. Use o seguinte exemplo:

   ```azurecli
   az network express-route list
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Configurar o emparelhamento da Microsoft para o circuito. Verifique se você tem as informações a seguir antes de continuar.

   * Uma sub-rede /30 para o link principal. O bloco de endereços precisa ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma sub-rede /30 para o link secundário. O bloco de endereços precisa ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: Forneça uma lista com todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **Opcional –** ASN de cliente: Se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
   * Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
   * **Opcional –** Um hash MD5 se você optar por usar um.

   Execute o exemplo a seguir para configurar o emparelhamento da Microsoft para seu circuito:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> A Microsoft verifica se os "Prefixos públicos publicados" e o "ASN de ponto" (ou "cliente ASN de cliente") especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver recebendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá "AdvertisedPublicPrefixesState" como "Validação necessária" na saída do comando acima. 
> 
> Se você vir a mensagem "Requer validação", colete os documentos que mostram que os prefixos públicos foram atribuídos à organização pela entidade que está listada como proprietária dos prefixos no registro de roteamento e envie esses documentos para validação manual, abrindo um tíquete de suporte. 
> 
>

A saída deverá ser semelhante ao seguinte exemplo:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração. Os prefixos anunciados do circuito estão sendo atualizados de 123.1.0.0/24 para 124.1.0.0/24 no exemplo a seguir:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Para adicionar configurações de IPv6 de emparelhamento da Microsoft a uma configuração de IPv4 existente

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

## <a name="azure-private-peering"></a><a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Instale a versão mais recente do CLI do Azure.
1. 
   ```azurecli
   az login
   ```

   Selecionar a assinatura na qual você deseja criar um circuito do ExpressRoute

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
1. Criar um circuito do ExpressRoute. Siga as instruções para criar um [circuito do ExpressRoute](howto-circuit-cli.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com a configuração usando as próximas etapas.

1. Verifique se o circuito do ExpressRoute está provisionado e também habilitado. Use o seguinte exemplo:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

1. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de prosseguir para as próximas etapas:

   * Um par de sub-redes que não fazem parte de nenhum espaço de endereço reservado para redes virtuais. Uma sub-rede será usada para o link principal enquanto a outra será usada para o link secundário. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela. Você tem três opções para este par de sub-redes:
       * IPv4: duas sub-redes /30.
       * IPv6: duas sub-redes /126.
       * Ambas: duas sub-redes /30 e duas sub-redes /126.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Verifique se você não está usando 65515.
   * **Opcional –** Um hash MD5 se você optar por usar um.

   Use o seguinte exemplo para configurar o emparelhamento privado do Azure para seu circuito:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.
   > 
   > 

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 100 para 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o emparelhamento privado do Azure, você poderá vincular redes virtuais ao circuito. Confira: 

> [!div class="nextstepaction"]
> [Vincular uma rede virtual a um circuito do ExpressRoute](howto-linkvnet-cli.md)
