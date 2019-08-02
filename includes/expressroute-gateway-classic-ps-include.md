---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171656"
---
> [!NOTE]
> Esses exemplos não se aplicam a configurações de coexistência de S2S/ExpressRoute.
> Para obter mais informações sobre como trabalhar com gateways em uma configuração de coexistência, consulte [Configurar conexões coexistentes.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Adicionar um gateway

Quando você adiciona um gateway para uma rede virtual usando o modelo de recurso clássico, você modifica o arquivo de configuração de rede diretamente antes de criar o gateway. Os valores nos exemplos a seguir devem estar presentes no arquivo para criar um gateway. Se sua rede virtual já tinha um gateway associado a ela, alguns desses valores já estarão presentes. Modifique o exemplo para refletir os valores abaixo.

### <a name="download-the-network-configuration-file"></a>Faça o download do arquivo de configuração de rede

1. Faça o download do arquivo de configuração de rede usando as etapas no artigo do [arquivo de configuração de rede](../articles/virtual-network/virtual-networks-using-network-configuration-file.md). Abra o arquivo usando um editor de texto.
2. Adicione um site de rede local para o arquivo. Você pode usar qualquer prefixo de endereço válido. Você pode adicionar qualquer endereço IP válido para o gateway de VPN. Os valores de endereço nesta seção não são usados para operações de ExpressRoute, mas são necessários para a validação do arquivo. No exemplo, "branch1" é o nome do site. Você pode usar um nome diferente, mas não se esqueça de usar o mesmo valor na seção de Gateway do arquivo.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navegue até o VirtualNetworkSites e modifique os campos.

   * Verifique se sua rede virtual tem uma sub-rede de gateway. Se isso não acontecer, você pode adicionar um neste momento. O nome deve ser "GatewaySubnet".
   * Verifique se a seção de Gateway do arquivo existe. Caso contrário, adicione-a. Isso é necessário para associar a rede virtual com o site de rede local (que representa a rede à qual você está se conectando).
   * Verifique a conexão de tipo = dedicado. Isso é necessário para conexões do ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Salve o arquivo e carregue-o no Azure.

### <a name="create-the-gateway"></a>Criar o gateway

Use o comando a seguir para criar um gateway. Substitua quaisquer valores pelos seus próprios.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verificar se o gateway foi criado

Use o comando abaixo para verificar se o gateway foi criado. Esse comando também recupera a ID do gateway, que você precisa para outras operações.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Há uma série de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

> [!IMPORTANT]
> Esse comando não funciona para o gateway UltraPerformance. Para alterar o gateway para um gateway UltraPerformance, primeiro remova o gateway do ExpressRoute existente e crie um novo gateway UltraPerformance. Para fazer downgrade do gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e crie um novo gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway

Use o comando a seguir para remover um gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```