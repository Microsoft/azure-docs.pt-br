---
title: 'Configurar o BGP em um gateway de VPN do Azure: Resource Manager e CLI | Microsoft Docs'
description: Este artigo mostra como configurar o BGP com um gateways de VPN do Azure utilizando o Azure Resource Manager e a CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Como configurar o BGP em um gateway de VPN do Azure utilizando a CLI

Este artigo ajuda você a habilitar o BGP em uma conexão VPN S2S (Site a Site) entre instalações e uma conexão VNet a VNet (que é uma conexão entre redes virtuais) utilizando o modelo de implantação do Azure Resource Manager e a CLI do Azure.

## <a name="about-bgp"></a>Sobre o BGP

O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. O BGP permite que os gateways de VPN e seus dispositivos VPN locais, chamados de vizinhos ou pares de BGP, troquem "rotas". As rotas informam ambos os gateways sobre a disponibilidade e a acessibilidade dos prefixos para passar pelos gateways ou roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes, propagando as rotas que um gateway de BGP aprende de um par de BGP para todos os outros pares de BGP.

Para obter mais informações sobre os benefícios do BGP e entender os requisitos técnicos e as considerações de uso do BGP, consulte [Visão geral do BGP com gateways de VPN do Azure](vpn-gateway-bgp-overview.md).

Este artigo ajuda-o com as seguintes tarefas:

* [Habilitar o BGP para o gateway de VPN](#enablebgp) (obrigatório)

  É possível completar uma das seguintes seções, ou as duas:

* [Estabelecer uma conexão entre instalações com BGP](#crossprembgp)
* [Estabelecer uma conexão VNet a VNet com BGP](#v2vbgp)

Cada uma dessas três seções é um bloco de construção básico para habilitar o BGP na sua conectividade de rede. Se completar as três seções, a topologia será construída conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

É possível combinar essas seções para compilar uma rede de trânsito com saltos múltiplos mais complexa que atenda às suas necessidades.

## <a name ="enablebgp"></a>Habilitar o BGP para o gateway de VPN

Esta seção é necessária antes de executar qualquer uma das etapas nas outras duas seções de configuração. As etapas de configuração a seguir configuram os parâmetros BGP do gateway de VPN do Azure, conforme mostrado no diagrama seguinte:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

Instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos da CLI, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Etapa 1: Criar e configurar TestVNet1

#### <a name="Login"></a>1. Conecte-se as suas assinaturas

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Criar um grupo de recursos

O exemplo a seguir cria um grupo de recursos denominado TestRG1 no local "eastus". Se já tiver um grupo de recursos na região em que deseja criar a rede virtual, você poderá usá-lo.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Criar TestVNet1

O exemplo a seguir cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, FrontEnd e BackEnd. Ao substituir valores, é importante que você sempre nomeie sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará.

O primeiro comando cria o espaço de endereçamento front-end e a sub-rede FrontEnd. O segundo comando cria um espaço de endereçamento adicional para a sub-rede de BackEnd. Os comandos terceiro e quarto criam a sub-rede BackEnd e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Etapa 2: Criar o gateway de VPN para TestVNet1 com parâmetros de BGP

#### <a name="1-create-the-public-ip-address"></a>1. Criar um endereço IP público

Solicite um endereço IP público. O endereço IP público será alocado ao gateway de VPN que criado para rede virtual.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o gateway de VPN com o número AS

Crie o gateway de rede virtual para TestVNet1. O BGP requer um gateway de VPN baseado em rota. Além disso, será necessário o parâmetro adicional `-Asn` para definir o ASN (número de sistema autônomo) como TestVNet1. A criação de um gateway pode demorar um pouco (45 minutos ou mais). 

Se você executar esse comando usando o parâmetro `--no-wait`, não receberá nenhum feedback ou saída. O parâmetro `--no-wait` permite que o gateway seja criado em segundo plano. Isso não significa que a criação do gateway de VPN ocorrerá imediatamente.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obter o endereço IP do par no nível de protocolo BGP do Azure

Após a criação do gateway, você precisará obter o endereço IP do par no nível de protocolo BGP no gateway de VPN do Azure. Esse endereço é necessário para configurar o gateway de VPN como um par no nível de protocolo BGP para os dispositivos VPN locais.

Execute o seguinte comando e verifique a seção `bgpSettings` na parte superior da saída:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Depois de criar o gateway, você poderá usar este gateway para estabelecer a conexão entre instalações ou conexão VNet a VNet com BGP.

## <a name ="crossprembgp"></a>Estabelecer uma conexão entre instalações com BGP

Para estabelecer uma conexão entre instalações, você precisará criar um gateway de rede local para representar o dispositivo VPN local. Em seguida, conecte o gateway de VPN do Azure com o gateway de rede local. Embora essas etapas sejam semelhantes à criação de outras conexões, elas incluem as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP entre instalações](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Etapa 1: Criar e configurar o gateway de rede local

Este exercício continua a compilar a configuração mostrada no diagrama. Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração. Ao trabalhar com gateways de rede local, lembre-se das seguintes ações:

* O gateway de rede local pode estar no mesmo local e no mesmo grupo de recursos que o gateway de VPN, ou em um local e grupo de recursos diferente. Este exemplo os mostra os gateways em grupos de recursos diferentes em locais diferentes.
* O prefixo mínimo que você precisa declarar para o gateway de rede local é o endereço de host do seu endereço IP do par no nível de protocolo BGP em seu dispositivo VPN. Nesse caso, é um prefixo /32 de 10.52.255.254/32.
* Como lembrete, você deve usar ASNs BGP diferentes entre suas redes locais e a rede virtual do Azure. Se eles forem iguais, você precisará alterar seu ASN VNet se os dispositivos VPN locais já utilizam o ASN para emparelhar com outros vizinhos de BGP.

Antes de prosseguir, conclua a seção [Habilitar o BGP para seu gateway de VPN](#enablebgp) deste exercício, verifique se ainda está conectado à Assinatura 1. Observe que, neste exemplo, você cria um novo grupo de recursos. Além disso, observe os dois parâmetros adicionais para o gateway de rede local: `Asn` e `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2: Conectar o gateway de VNet e o gateway de rede local

Nesta etapa, você cria a conexão de TestVNet1 para Site5. É necessário especificar o parâmetro `--enable-bgp` para habilitar o BGP para essa conexão. 

Neste exemplo, o gateway de rede virtual e o gateway de rede local estão em grupos de recursos diferentes. Quando os gateways estiverem em grupos de recursos diferentes, será necessário especificar toda a ID de recurso dos dois gateways para configurar uma conexão entre redes virtuais.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obter a ID de recurso da VNet1GW

Utilize o resultado do seguinte comando para obter a ID do recurso para VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Na saída, localize a linha `"id":`. Você precisa dos valores entre aspas para criar a conexão na próxima seção.

Saída de exemplo:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Copie os valores após `"id":` em um editor de texto, como o Bloco de Notas, para que você possa colá-los facilmente ao criar sua conexão. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Obter a ID do recurso do Site5

Utilize o seguinte comando para obter a ID do recurso do Site5 a partir da saída:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Criar a conexão entre TestVNet1 e Site5

Nesta etapa, você cria a conexão de TestVNet1 para Site5. Conforme discutido anteriormente, é possível ter conexões BGP e conexões que não são de BGP para o mesmo gateway de VPN do Azure. A menos que o BGP esteja habilitado na propriedade de conexão, o Azure não habilitará o BGP para essa conexão, mesmo que parâmetros BGP já estejam configurados em ambos os gateways. Substitua as IDs da assinatura pela sua.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Para esse exercício, o exemplo a seguir lista os parâmetros para entrar na seção de configuração de BGP do seu dispositivo VPN local:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Depois de alguns minutos, a conexão deve ser estabelecida. A sessão de emparelhamento BGP começa após a conexão IPsec ser estabelecida.

## <a name ="v2vbgp"></a>Estabelecer uma conexão VNet a VNet com BGP

Esta seção adiciona uma conexão de VNet para VNet com BGP, conforme mostrado neste diagrama: 

![BGP de VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As instruções a seguir continuam a partir das etapas anteriores nas seções anteriores. Para criar e configurar o TestVNet1 e o gateway de VPN com BGP, é necessário completar a seção [Habilitar o BGP para o gateway de VPN](#enablebgp).

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Etapa 1: Criar TestVNet2 e o gateway de VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de rede virtual a rede entre assinaturas diferentes. Para saber mais, confira [Configurar uma conexão VNet para VNet](vpn-gateway-howto-vnet-vnet-cli.md). Verifique se você adicionou `-EnableBgp $True` ao criar conexões para habilitar o BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Criar um novo grupo de recursos

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

O primeiro comando cria o espaço de endereçamento front-end e a sub-rede FrontEnd. O segundo comando cria um espaço de endereçamento adicional para a sub-rede de BackEnd. Os comandos terceiro e quarto criam a sub-rede BackEnd e GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Criar um endereço IP público

Solicite um endereço IP público. O endereço IP público será alocado ao gateway de VPN que criado para rede virtual.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Criar o gateway de VPN com o número AS

Crie o gateway de rede virtual para TestVNet2. Você deve substituir o ASN padrão em seus gateways de VPN do Azure. As ASNs para as redes virtuais conectadas devem ser diferentes para habilitar o BGP e roteamento de tráfego.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2: Conectar os gateways de TestVNet1 e TestVNet2

Nesta etapa, você cria a conexão de TestVNet1 para Site5. Para habilitar o BGP para essa conexão, é necessário especificar o parâmetro `--enable-bgp`.

No exemplo a seguir, o gateway de rede virtual e o gateway de rede local estão em diferentes grupos de recursos. Quando os gateways estiverem em grupos de recursos diferentes, será necessário especificar toda a ID de recurso dos dois gateways para configurar uma conexão entre redes virtuais. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Obter a ID de recurso da VNet1GW 

Obtenha a ID de recurso de VNet1GW na saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Obter a ID de recurso da VNet2GW

Obtenha a ID de recurso de VNet2GW na saída do seguinte comando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Criar as conexões

Crie a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 para TestVNet1. Substitua as IDs da assinatura pela sua.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Habilite o BGP para *as duas* conexões.
> 
> 

Após concluir essas etapas, a conexão será estabelecida em alguns minutos. A sessão de emparelhamento do BGP será iniciada quando a conexão VNet a VNet for concluída.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão de sua conexão, você poderá adicionar máquinas virtuais às suas redes virtuais. Para conferir as etapas, consulte [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
