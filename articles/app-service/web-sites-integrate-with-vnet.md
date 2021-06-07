---
title: Integrar um aplicativo à Rede Virtual do Azure
description: Integre um aplicativo ao Serviço de Aplicativo do Azure com as redes virtuais do Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4666f03e529c568177ff56a2db84e226bc906132
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182903"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrar seu aplicativo a uma rede virtual do Azure

Este artigo descreve o recurso de Integração VNET do Serviço de Aplicativo do Azure e como configurá-lo com os aplicativos no [Serviço de Aplicativo do Azure](./overview.md). Com as VNETs ([Redes Virtuais do Azure][VNETOverview]) você pode colocar muitos dos seus recursos do Azure em uma rede não roteável para a Internet. O recurso integração VNet permite que seus aplicativos acessem recursos no ou por meio de uma VNet. A integração VNet não permite que seus aplicativos sejam acessados em particular.

O serviço de Azure App tem duas variações no recurso de integração VNet:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Habilitar a Integração VNET

1. Acesse a interface do usuário da **Rede** no portal do Serviço de Aplicativo. Em **Integração VNET**, selecione **Clique aqui para configurar**.

1. Selecione **Adicionar VNet**.

   ![Selecionar a Integração VNET][1]

1. A lista suspensa contém todas as redes virtuais do Azure Resource Manager na sua assinatura na mesma região. Abaixo dela está uma lista das redes virtuais do Resource Manager em todas as outras regiões. Selecione a VNET à qual deseja fazer a integração.

   ![Selecionar a VNET][2]

   * Se a VNET estiver na mesma região, crie uma sub-rede ou selecione uma sub-rede preexistente vazia.
   * Para selecionar uma VNET em outra região, você precisará ter um gateway de VNET provisionado com o ponto a site habilitado.
   * Para fazer a integração a uma VNET clássica, em vez de selecionar a lista suspensa **Rede Virtual**, selecione **Clique aqui para se conectar a uma VNET Clássica**. Selecione a rede virtual clássica desejada. A VNET de destino já precisará ter um gateway de rede virtual provisionado com o ponto a site habilitado.

    ![Selecionar a VNET Clássica][3]

Durante a integração, o aplicativo é reiniciado. Quando a integração for concluída, você verá detalhes sobre a VNET à qual está integrado.

## <a name="regional-vnet-integration"></a>Integração VNET regional

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Como funciona a Integração VNET regional

Os aplicativos do Serviço de Aplicativo são hospedados em funções de trabalho. Os planos de preços Básico e superiores são planos de hospedagem dedicados, em que não há cargas de trabalho de outros clientes em execução nos mesmos trabalhos. A Integração VNET regional funciona por meio da montagem de interfaces virtuais com endereços na sub-rede delegada. Como o endereço do remetente está na sua VNET, ele pode acessar a maioria dos itens na VNET ou por meio dela, assim como uma VM na VNET. A implementação de rede é diferente da execução de uma VM na VNET. É por isso que alguns recursos de rede ainda não estão disponíveis para esse recurso.

![Como funciona a Integração VNET regional][5]

Quando a Integração VNET regional está habilitada, o aplicativo faz chamadas de saída à Internet por meio dos mesmos canais como de costume. Os endereços de saída listados no portal de propriedades do aplicativo são os endereços ainda usados pelo seu aplicativo. O que é alterado para o aplicativo são as chamadas aos serviços protegidos pelo ponto de extremidade de serviço ou os endereços RFC 1918 que são incluídos na VNET. Se WEBSITE_VNET_ROUTE_ALL for definido como 1, todo o tráfego de saída poderá ser enviado para a VNET.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` Não tem suporte atualmente em contêineres do Windows.
> 

O recurso dá suporte a apenas uma interface virtual por trabalho. Uma interface virtual por trabalho significa uma Integração VNET regional por Plano do Serviço de Aplicativo. Todos os aplicativos no mesmo Plano do Serviço de Aplicativo podem usar a mesma Integração VNET. Se você precisar que um aplicativo se conecte a uma VNET adicional, precisará criar outro Plano do Serviço de Aplicativo. A interface virtual usada não é um recurso ao qual os clientes têm acesso direto.

Devido à natureza de como essa tecnologia opera, o tráfego usado com a Integração VNET não é exibido no Observador de Rede do Azure nem nos logs de fluxo do NSG.

## <a name="gateway-required-vnet-integration"></a>Integração VNET exigida pelo gateway

A Integração VNET exigida pelo gateway dá suporte à conexão com uma VNET em outra região ou com uma rede virtual clássica. Integração VNET exigida pelo gateway:

* Permite que um aplicativo se conecte somente a uma VNET por vez.
* Permite a integração de até cinco VNETs em um Plano do Serviço de Aplicativo.
* Permite que a mesma VNET seja usada por vários aplicativos em um Plano do Serviço de Aplicativo sem afetar o número total que pode ser usado por um Plano do Serviço de Aplicativo. Se você tiver seis aplicativos que usam a mesma VNET no mesmo Plano do Serviço de Aplicativo, isso contará como uma VNET sendo usada.
* Dá suporte a um SLA de 99,9% devido ao SLA no gateway.
* Permite que os seus aplicativos usem o DNS com o qual a VNET está configurada.
* Exige um gateway baseado em rota da Rede Virtual configurado com uma VPN ponto a site SSTP antes que possa ser conectado a um aplicativo.

Não é possível usar a Integração VNET exigida pelo gateway:

* Com uma VNET conectada ao Azure ExpressRoute.
* De um aplicativo Linux.
* De um [contêiner do Windows](quickstart-custom-container.md).
* Para acessar os recursos protegidos pelo ponto de extremidade de serviço.
* Com um gateway de coexistência que dê suporte ao ExpressRoute e a VPNs ponto a site ou site a site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Configurar um gateway na rede virtual do Azure ###

Para criar um gateway:

1. [Crie uma sub-rede de gateway][creategatewaysubnet] na VNET.

1. [Crie o gateway de VPN][creategateway]. Selecione um tipo VPN baseado em rota.

1. [Defina os endereços de ponto a site][setp2saddresses]. Se o gateway não estiver no SKU básico, então o IKEV2 precisará ser desabilitado na configuração ponto a site e o SSTP deverá ser selecionado. O espaço de endereço de ponto a site precisa estar nos blocos de endereços RFC 1918 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Se você criar o gateway para uso com a Integração VNET do Serviço de Aplicativo, não precisará carregar um certificado. A criação do gateway pode levar 30 minutos. Você não poderá integrar seu aplicativo à VNET até que o gateway seja provisionado.

### <a name="how-gateway-required-vnet-integration-works"></a>Como funciona a Integração VNET exigida pelo gateway

O recurso Integração VNET exigida pelo gateway se baseia na tecnologia de VPN ponto a site. As VPNs ponto a site limitam o acesso à rede à máquina virtual que hospeda o aplicativo. Os aplicativos são restritos apenas ao envio de tráfego para a Internet por meio de Conexões Híbridas ou da Integração VNET. Quando seu aplicativo é configurado com o portal para usar a Integração VNET exigida pelo gateway, uma negociação complexa é gerenciada em seu nome para criar e atribuir certificados no gateway e no lado do aplicativo. O resultado é que os trabalhos usados para hospedar os aplicativos podem se conectar diretamente ao gateway de rede virtual na VNET selecionada.

![Como funciona a Integração VNET exigida pelo gateway][6]

### <a name="access-on-premises-resources"></a>Acessar recursos locais

Aplicativos podem acessar recursos locais ao integrarem-se com VNets que têm conexões site a site. Se você usar a Integração VNET exigida pelo gateway, atualize as rotas de gateway de VPN locais com os blocos de endereços ponto a site. Quando o VPN site a site é configurado pela primeira vez, os scripts usados para configurá-lo devem configurar as rotas adequadamente. Se adicionar os endereços ponto a site depois de criar uma VPN site a site, você precisará atualizar as rotas manualmente. Os detalhes sobre como fazer isso variam de acordo com o gateway e não são descritos aqui. Não é possível ter o BGP configurado com uma conexão VPN site a site.

Nenhuma configuração adicional é necessária para que o recurso de Integração VNET regional acesse a VNET até os recursos locais. Você apenas precisa conectar sua VNET aos recursos locais usando o ExpressRoute ou uma VPN site a site.

> [!NOTE]
> O recurso de Integração VNET exigida pelo gateway não integra um aplicativo a uma VNET que tenha um gateway do ExpressRoute. Mesmo se o gateway do ExpressRoute estiver configurado no [modo de coexistência][VPNERCoex], a Integração VNET não funcionará. Caso precise acessar recursos por meio de uma conexão do ExpressRoute, use o recurso de Integração VNET regional ou um [Ambiente do Serviço de Aplicativo][ASE], que é executado na sua VNET.
>
>

### <a name="peering"></a>Emparelhamento

Se você usar o emparelhamento com a Integração VNET regional, não precisará fazer nenhuma configuração adicional.

Se você usar a Integração VNET exigida pelo gateway com o emparelhamento, precisará configurar alguns itens adicionais. Para configurar o emparelhamento para funcionar com o aplicativo:

1. Adicione que uma conexão de emparelhamento na rede virtual à qual o aplicativo se conecta. Ao adicionar a conexão de emparelhamento, habilite **Permitir acesso à rede virtual** e selecione **Permitir tráfego encaminhado** e **Permitir trânsito de gateway**.
1. Adicione uma conexão de emparelhamento na VNET que está sendo emparelhada à rede virtual à qual você está conectado. Ao adicionar a conexão de emparelhamento à VNET de destino, habilite **Permitir acesso à rede virtual** e selecione **Permitir tráfego encaminhado** e **Permitir gateways remotos**.
1. Acesse a interface do usuário **Plano do Serviço de Aplicativo** > **Rede** > **Integração VNet** no portal. Selecione a VNet à qual o aplicativo se conecta. Na seção de roteamento, adicione o intervalo de endereços da VNET que está emparelhada com a VNET à qual o aplicativo está conectado.

## <a name="manage-vnet-integration"></a>Gerenciar a Integração VNET

A conexão e a desconexão com uma VNET estão em um nível de aplicativo. As operações que podem afetar a Integração VNET entre vários aplicativos estão no nível do Plano do Serviço de Aplicativo. No aplicativo > **Rede** > **portal da Integração VNET**, obtenha detalhes sobre a sua VNET. Veja informações semelhantes no nível do Plano do Serviço de Aplicativo no portal **Plano do Serviço de Aplicativo** > **Rede** > **Integração VNet**.

A única operação que você pode executar na exibição de aplicativo da instância da Integração VNET é desconectar seu aplicativo da VNET à qual ele está atualmente conectado. Para desconectar o aplicativo de uma VNet, selecione **Desconectar**. Seu aplicativo é reiniciado quando você se desconecta de uma VNET. Desconectar-se não altera a VNet. A sub-rede ou o gateway não é removido. Caso deseje excluir sua VNET, primeiro desconecte seu aplicativo da VNET e exclua os recursos dele, como os gateways.

A interface do usuário da Integração VNET do Plano do Serviço de Aplicativo mostra todas as integrações VNET usadas pelos aplicativos no seu Plano do Serviço de Aplicativo. Para ver detalhes sobre cada VNET, selecione a VNET de seu interesse. Há duas ações que você pode executar aqui para a Integração VNET exigida pelo gateway:

* **Sincronizar rede**: a operação de rede de sincronização é usada somente para o recurso de Integração VNET dependente de gateway. A execução de uma operação de sincronização de rede verifica se os certificados e as informações de rede são em sincronia. Se você adicionar ou alterar o DNS da VNET, execute uma operação de sincronização de rede. Essa operação reinicia todos os aplicativos que usam essa VNET. Essa operação não funcionará se você estiver usando um aplicativo e uma VNET que pertença a assinaturas diferentes.
* **Adicionar rotas**: a adição de rotas conduz o tráfego de saída para a VNET.

O IP privado atribuído à instância é exposto por meio da variável de ambiente, **WEBSITE_PRIVATE_IP**. A interface do usuário do console do kudu também mostra a lista de variáveis de ambiente disponíveis para o aplicativo Web. Esse IP é atribuído do intervalo de endereços da sub-rede integrada. Para integração de VNet regional, o valor de WEBSITE_PRIVATE_IP é um IP do intervalo de endereços da sub-rede delegada e para integração VNet exigida pelo gateway, o valor é um IP do intervalo de endereços do pool de endereço ponto a site configurado no gateway de rede virtual. Esse é o IP que será usado pelo aplicativo Web para se conectar aos recursos por meio da rede virtual. 

> [!NOTE]
> O valor de WEBSITE_PRIVATE_IP é associado à alteração. No entanto, ele será um IP dentro do intervalo de endereços da sub-rede de integração ou do intervalo de endereços de ponto a site, portanto, você precisará permitir o acesso de todo o intervalo de endereços.
>

### <a name="gateway-required-vnet-integration-routing"></a>Roteamento da Integração VNET exigida pelo gateway
As rotas definidas na VNET são usadas para direcionar o tráfego para a VNET do aplicativo. Para enviar o tráfego de saída adicional para a VNET, adicione esses blocos de endereço aqui. Essa funcionalidade só funciona com a Integração VNET exigida pelo gateway. As tabelas de rotas não afetam o tráfego do aplicativo quando você usa a Integração VNET exigida pelo gateway como exigem com a Integração VNET regional.

### <a name="gateway-required-vnet-integration-certificates"></a>Certificados da Integração VNET exigida pelo gateway
Quando a Integração VNET exigida pelo gateway está habilitada, há uma troca de certificados necessária para garantir a segurança da conexão. Juntamente com os certificados, há a configuração do DNS, as rotas e outros itens semelhantes que descrevem a rede.

Se os certificados ou as informações de rede forem alterados, selecione **Sincronizar Rede**. Ao selecionar **Sincronizar Rede**, você causa uma breve interrupção na conectividade entre o aplicativo e a VNET. Embora seu aplicativo não seja reiniciado, a perda de conectividade pode fazer com que seu site não funcione corretamente.

## <a name="pricing-details"></a>Detalhes de preço
O recurso de Integração VNET regional não tem nenhum custo adicional para uso além dos custos do tipo de preço do Plano do Serviço de Aplicativo.

Três custos estão relacionados ao uso do recurso de Integração VNET exigido pelo gateway:

* **Cobranças de tipo de preço do plano do serviço de aplicativo**: seus aplicativos precisam estar em um plano do serviço de aplicativo Standard, Premium, PremiumV2 ou PremiumV3. Para obter mais informações sobre esses custos, confira [Preços do Serviço de Aplicativo][ASPricing].
* **Custos de transferência de dados**: há um custo de saída de dados, mesmo se a VNET está no mesmo datacenter. Esses custos são descritos em [Detalhes de preços da transferência de dados][DataPricing].
* **Custos do gateway de VPN**: há um custo para o gateway de rede virtual necessário para a VPN ponto a site. Para obter mais informações, confira [Preços do gateway de VPN][VNETPricing].

## <a name="troubleshooting"></a>Solução de problemas

> [!NOTE]
> Não há suporte para integração VNET em cenários de Docker Compose no serviço de aplicativo.
> Azure Functions restrições de acesso serão ignoradas se houver um ponto de extremidade privado presente.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automação

O suporte à CLI está disponível para a Integração VNET regional. Para acessar os comandos a seguir, [instale a CLI do Azure][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

O suporte do PowerShell para integração VNet regional também está disponível, mas você deve criar um recurso genérico com uma matriz de propriedades do ResourceId da sub-rede

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Para a Integração VNET exigida pelo gateway, você pode integrar o Serviço de Aplicativo a uma rede virtual do Azure usando o PowerShell. Para obter um script pronto para uso, confira [Conectar um aplicativo no Serviço de Aplicativo do Azure a uma rede virtual do Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
