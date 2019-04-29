---
title: 'Conecte uma rede virtual a vários sites usando o Gateway de VPN e o PowerShell: Clássico | Microsoft Docs'
description: Conecte múltiplos sites locais a uma rede virtual clássica usando um Gateway de VPN.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836070"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Adicione uma conexão Site a Site a uma rede virtual com uma conexão de gateway de VPN existente (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (clássico)](vpn-gateway-multi-site.md)
>
>

Este artigo orienta você a usar o PowerShell para adicionar conexões Site a Site (S2S) a um gateway de VPN que tenha uma conexão existente. Esse tipo de conexão é frequentemente chamado de configuração de "vários sites". As etapas neste artigo se aplicam a redes virtuais criadas usando o modelo de implantação clássico (também conhecido como Gerenciamento de Serviço). Essas etapas não se aplicam a configurações de conexão coexistentes ExpressRoute/Site a Site.

### <a name="deployment-models-and-methods"></a>Modelos e métodos de implantação

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Atualizamos esta tabela conforme novos artigos e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Sobre a conexão

É possível conectar vários sites locais a uma única rede virtual. Isso é especialmente atraente para criar soluções de nuvem híbrida. A criação de uma conexão de múltiplos sites ao gateway de rede virtual do Azure é semelhante à criação de outras conexões site a site. Na verdade, você pode usar um gateway de VPN do Azure existente, desde que o gateway seja dinâmico (baseado em rotas).

Se já houver um gateway estático conectado à sua rede virtual, você poderá alterar o tipo de gateway para dinâmico sem precisar reconstruir a rede virtual a fim de acomodar os vários sites. Antes de alterar o tipo de roteamento, verifique se o gateway de VPN local oferece suporte às configurações de VPN baseada em rota.

![diagrama multissites](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>Considere o seguinte

**Você não poderá usar o portal para fazer alterações nessa rede virtual.** Você precisará fazer alterações no arquivo de configuração de rede em vez de usar o portal. Se você fizer alterações no portal, elas vão substituir as configurações de referência de múltiplos sites para essa rede virtual.

Será fácil usar o arquivo de configuração de rede depois de concluir o procedimento de múltiplos sites. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede,  precisará certificar-se de que todos saibam desta limitação. Isso não significa que você não pode usar o portal. Você pode usá-lo para tudo, exceto fazer alterações de configuração nessa rede virtual específica.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você tem os seguintes itens:

* Hardware de VPN compatível para cada caminho no local. Confira [Sobre dispositivos VPN para conectividade de rede virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que você deseja usar é sabidamente compatível.
* Um endereço IP IPv4 público voltado para o exterior para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Isso é obrigatório.
* Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Instale a versão do Gerenciamento de Serviços (SM) além da versão do Resource Manager. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter mais informações.
* Alguém que seja proficiente na configuração de seu hardware de VPN. Você precisará ter um grande conhecimento de como configurar seu dispositivo VPN ou trabalhar com alguém que tenha.
* Os intervalos de endereços IP que você deseja usar na sua rede virtual (se ainda não tiver criado uma).
* Os intervalos de endereços IP para cada um dos sites de rede local aos quais se conectará. Você precisará certificar-se de que os intervalos de endereço IP para cada um dos sites de rede local aos quais quer se conectar não se sobrepõem. Caso contrário, o portal ou a API REST rejeitará a configuração que está sendo carregada.<br>Por exemplo, se você tiver dois sites de rede local que contenham o intervalo de endereço IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberá para qual site você deseja enviar o pacote porque os intervalos de endereço estão sobrepostos. Para evitar problemas de roteamento, o Azure não permite carregar um arquivo de configuração com sobreposição de intervalos.

## <a name="1-create-a-site-to-site-vpn"></a>1. Criar uma VPN site a site
Se você já tiver uma VPN site a site com um gateway de roteamento dinâmico, ótimo! Continue em [Exporte as definições da configuração de rede virtual](#export). Caso contrário, faça o seguinte:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Se você já tiver uma rede virtual site a site, mas ela tiver um gateway de roteamento estático (baseado em política):
1. Altere o tipo de gateway para roteamento dinâmico. Uma VPN multissites exige um gateway de roteamento dinâmico (também chamado de baseado em rota). Para alterar o tipo de gateway, você precisará primeiro excluir o gateway existente e, em seguida, criar um novo.
2. Configure seu novo gateway e crie seu túnel de VPN. Para obter instruções, consulte [Especificar o tipo VPN e SKU](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Verifique se que você especificou o Tipo de Roteamento como 'Dinâmico'.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Se você não tiver uma rede virtual site a site:
1. Crie sua rede virtual do Site a Site usando estas instruções: [Criar uma rede Virtual com uma Conexão VPN Site a Site](vpn-gateway-site-to-site-create.md).  
2. Configure um gateway de roteamento dinâmico usando estas instruções: [Configurar um Gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Lembre-se de selecionar **roteamento dinâmico** para o tipo de gateway.

## <a name="export"></a>2. Exportar o arquivo de configuração de rede
Baixe o arquivo de configuração de rede do Azure executando o comando a seguir. Você pode alterar o local do arquivo a ser exportado para um local diferente, se necessário.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Abrir o arquivo de configuração de rede
Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de xml que desejar. O arquivo deve ser semelhante ao seguinte:

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Adicionar referências a vários sites
Ao adicionar ou remover informações de referência do site, alterações de configuração serão feitas em ConnectionsToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência de site local faz com que o Azure crie um novo túnel. No exemplo abaixo, a configuração de rede é uma conexão de site único. Quando terminar de fazer suas alterações, salve o arquivo.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Para adicionar referências a sites adicionais (criar uma configuração de vários sites), basta adicionar linhas "LocalNetworkSiteRef", como mostrado no exemplo a seguir:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importar o arquivo de configuração de rede
Importe o arquivo de configuração de rede. Ao importar esse arquivo com as alterações, os novos túneis serão adicionados. Os túneis usarão o gateway dinâmico que você criou anteriormente. Você pode usar o PowerShell para importar o arquivo.

## <a name="6-download-keys"></a>6. Baixar chaves
Depois de adicionar os novos túneis, use o cmdlet do PowerShell 'Get-AzureVNetGatewayKey' para obter as chaves pré-compartilhadas IPsec/IKE para cada túnel.

Por exemplo: 

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Se preferir, você também pode usar a API REST *Obter chave compartilhada de gateway de rede virtual* para obter as chaves pré-compartilhadas.

## <a name="7-verify-your-connections"></a>7. Verificar as conexões
Verifique o status do túnel de múltiplos sites. Depois de baixar as chaves para cada túnel, é recomendável verificar as conexões. Use 'Get-AzureVnetConnection' para obter uma lista dos túneis de rede virtual, conforme mostrado no exemplo a seguir. VNet1 é o nome do VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exemplo de retorno:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Gateways de VPN, confira [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md).
