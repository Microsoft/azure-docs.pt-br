---
title: 'Conectar a rede virtual a outra rede virtual: CLI do Azure | Microsoft Docs'
description: Este artigo mostra como conectar redes virtuais em conjunto usando o Azure Resource Manager e a CLI do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: ff859bd9dbbf30c461cdba8409c77b04ff97b1f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Configurar uma conexão gateway de VPN de Vnet pra VNet usando a CLI do Azure

Este artigo mostra como criar uma conexão de gateway de VPN entre redes virtuais. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes. Ao conectar-se a redes virtuais a partir de assinaturas diferentes, as assinaturas não precisam ser associadas ao mesmo locatário do Active Directory. 

As etapas neste artigo se aplicam ao modelo de implantação do Resource Manager e usa a CLI do Azure. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Se suas VNets estiverem na mesma região, convém considerar conectá-las usando o emparelhamento VNet. O emparelhamento Vnet não usa um gateway de VPN. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Sobre as conexões](./media/vpn-gateway-howto-vnet-vnet-cli/aboutconnections.png)

### <a name="why"></a>Por que conectar as redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

* **Redundância geográfica entre regiões e presença geográfica**

  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Balanceador de Carga e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.
* **Aplicativos multicamadas regionais com limite administrativo ou de isolamento**

  * Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Para saber mais sobre conexões de Rede Virtual a Rede Virtual, consulte as [Perguntas frequentes sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

### <a name="which-set-of-steps-should-i-use"></a>Qual conjunto de etapas devo usar?

Neste artigo, você verá dois conjuntos de etapas diferentes. Um conjunto de etapas para [VNets que residem na mesma assinatura](#samesub), e outro para [VNets que residem em assinaturas diferentes](#difsub).

## <a name="samesub"></a>Conectar VNets que estão na mesma assinatura

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

### <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para saber mais sobre como instalar os comandos do CLI, veja [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli).

### <a name="Plan"></a>Planejar os intervalos de endereços IP

Nas etapas a seguir, criaremos duas redes virtuais juntamente com as respectivas configurações e sub-redes de gateway. Em seguida, criaremos uma conexão VPN entre as duas VNets. É importante planejar os intervalos de endereços IP para sua configuração de rede. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma. Nestes exemplos, não incluímos um servidor DNS. Se você deseja resolução de nomes para suas redes virtuais, confira [a Resolução de nomes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Usamos os seguintes valores nos exemplos:

**Valores para TestVNet1:**

* Nome da rede virtual: TestVNet1
* Grupo de recursos: TestRG1
* Local: Leste dos EUA
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* IP público: VNet1GWIP
* VpnType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5
* ConnectionType: VNet2VNet

**Valores para TestVNet4:**

* Nome da rede virtual: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de recursos: TestRG4
* Local: Oeste dos EUA
* GatewayName: VNet4GW
* IP público: VNet4GWIP
* VpnType: RouteBased
* Conexão: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="Connect"></a>Etapa 1: conectar-se à sua assinatura

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>Etapa 2: Criar e configurar o TestVNet1

1. Crie um grupos de recursos.

  ```azurecli
  az group create -n TestRG1  -l eastus
  ```
2. Crie TestVNet1 e as sub-redes de TestVNet1. O exemplo a seguir cria uma rede virtual chamada TestVNet1 e uma sub-rede chamada FrontEnd.

  ```azurecli
  az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
  ```
3. Crie outro espaço de endereço para a sub-rede de back-end. Observe que nesta etapa, especificamos o espaço de endereço criado anteriormente e outro espaço de endereço que desejamos adicionar. Isso ocorre porque o comando [az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_update) substitui as configurações anteriores. Não deixe de especificar todos os prefixos de endereço ao usar o comando.

  ```azurecli
  az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
  ```
4. Crie a sub-rede de back-end.
  
  ```azurecli
  az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
  ```
5. Crie a sub-rede de gateway. Verifique se a sub-rede de gateway se chama 'GatewaySubnet'. Esse nome é obrigatório. Neste exemplo, a sub-rede de gateway está usando um /27. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro.

  ```azurecli 
  az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
  ```
6. Solicite um endereço IP público para ser alocado ao gateway que você criará para sua VNet. Observe que o AllocationMethod é dinâmico. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente ao gateway.

  ```azurecli
  az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
  ```
7. Crie o gateway de rede virtual para TestVNet1. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. Se você executar esse comando usando o parâmetro '--no-wait', você não receberá nenhum feedback ou saída. O parâmetro '--no-wait' permite que o gateway seja criado em segundo plano. Isso não significa que a criação do gateway de VPN é concluída imediatamente. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway utilizado.

  ```azurecli
  az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="TestVNet4"></a>Etapa 3: criar e configurar TestVNet4

1. Crie um grupos de recursos.

  ```azurecli
  az group create -n TestRG4  -l westus
  ```
2. Crie a TestVNet4.

  ```azurecli
  az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
  ```

3. Crie sub-redes adicionais para TestVNet4.

  ```azurecli
  az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
  az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
  ```
4. Crie a sub-rede de gateway.

  ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
  ```
5. Solicite um endereço IP público.

  ```azurecli
  az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
  ```
6. Crie o gateway de rede virtual TestVNet4.

  ```azurecli
  az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="createconnect"></a>Etapa 4: criar as conexões

Agora, você tem duas redes virtuais com gateways de VPN. A próxima etapa é criar conexões de gateway de VPN entre os gateways de rede virtual. Se você usou os exemplos acima, seus gateways de rede virtual estão em grupos de recursos diferentes. Quando os gateways estiverem em diferentes grupos de recursos, você precisará identificar e especificar as IDs de recurso para cada gateway ao fazer uma conexão. Se suas redes virtuais estão no mesmo grupo de recursos, você poderá usar o [segundo conjunto de instruções](#samerg) porque não precisará especificar as IDs de recurso.

### <a name="diffrg"></a>Para conectar as VNets que residem em diferentes grupos de recursos

1. Obtenha a ID de recurso de VNet1GW na saída do seguinte comando:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  Na saída, localize a linha "id:". Os valores entre aspas são necessários para criar a conexão na próxima seção. Copie esses valores para um editor de texto, como o bloco de notas, para que você possa colá-los facilmente quando criar sua conexão.

  Saída de exemplo:

  ```
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
  "enableBgp": false, 
  "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
  "ipConfigurations":
  ```

  Copie os valores após **"id":** entre aspas.

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
 ```

2. Obtenha a ID de recurso de VNet4GW e copie os valores em um editor de texto.

  ```azurecli
  az network vnet-gateway show -n VNet4GW -g TestRG4
  ```

3. Crie a conexão de TestVNet1 a TestVNet4. Nesta etapa, você criará a conexão de TestVNet1 para TestVNet4. Existe uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. A criação de uma conexão leva alguns minutos para ser concluída.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
  ```
4. Crie a conexão de TestVNet4 a TestVNet1. Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet4 para TestVNet1. Verifique se que as chaves compartilhadas correspondem. A conexão leva alguns minutos para ser estabelecida.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
  ```
5. Verifique as conexões. Confira [Verificar a conexão](#verify).

### <a name="samerg"></a>Para conectar redes virtuais que residem no mesmo grupo de recursos

1. Crie a conexão de TestVNet1 a TestVNet4. Nesta etapa, você criará a conexão de TestVNet1 para TestVNet4. Observe que os grupos de recursos são os mesmos nos exemplos. Você também verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. No entanto, a chave compartilhada deve corresponder às duas conexões. A criação de uma conexão leva alguns minutos para ser concluída.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
  ```
2. Crie a conexão de TestVNet4 a TestVNet1. Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet4 para TestVNet1. Verifique se que as chaves compartilhadas correspondem. A conexão leva alguns minutos para ser estabelecida.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
  ```
3. Verifique as conexões. Confira [Verificar a conexão](#verify).

## <a name="difsub"></a>Conectar as VNets que estão em assinaturas diferentes

![Diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)

Nesse cenário, conectamos TestVNet1 e TestVNet5. As redes virtuais residem em assinaturas diferentes. As assinaturas não precisam ser associadas ao mesmo locatário do Active Directory. As etapas para essa configuração adicionam outra conexão de VNet para VNet para se conectar TestVNet1 a TestVNet5.

### <a name="TestVNet1diff"></a>Etapa 5: criar e configurar o TestVNet1

Essas instruções continuam das etapas nas seções anteriores. Você deve concluir a [Etapa 1](#Connect) e a [Etapa 2](#TestVNet1) para criar e configurar o TestVNet1 e o Gateway de VPN para TestVNet1. Para essa configuração, você não precisa criar a TestVNet4 da seção anterior, mas, se criá-la, ela não entrará em conflito com estas etapas. Depois de concluir as Etapas 1 e 2, continue com a Etapa 6 (abaixo).

### <a name="verifyranges"></a>Etapa 6: verificar os intervalos de endereços IP

Ao criar conexões adicionais, é importante verificar se o espaço de endereço IP da nova rede virtual não coincide com nenhum dos outros intervalos de VNet de gateway de rede local. Para este exercício, você pode usar os seguintes valores para TestVNet5:

**Valores para TestVNet5:**

* Nome da rede virtual: TestVNet5
* Grupo de recursos: TestRG5
* Local: Leste do Japão
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* IP público: VNet5GWIP
* VpnType: RouteBased
* Connection: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="TestVNet5"></a>Etapa 7: criar e configurar TestVNet5

Esta etapa deve ser feita no contexto da nova assinatura, Assinatura 5. Esta parte pode ser executada pelo administrador em uma organização diferente que possui a assinatura. Para alternar entre assinaturas, use 'az account list --all' a fim de listar as assinaturas disponíveis para sua conta e use 'az account set --subscription<subscriptionID>' a fim de alternar para a assinatura que você deseja usar.

1. Verifique se você está conectado à Assinatura 5 e crie um grupo de recursos.

  ```azurecli
  az group create -n TestRG5  -l japaneast
  ```
2. Crie a TestVNet5.

  ```azurecli
  az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
  ```

3. Adicione sub-redes.

  ```azurecli
  az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
  az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
  ```

4. Adicione a sub-rede de gateway.

  ```azurecli
  az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
  ```

5. Solicite um endereço IP público.

  ```azurecli
  az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
  ```
6. Criar o gateway TestVNet5

  ```azurecli
  az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="connections5"></a>Etapa 8: criar as conexões

Dividimos esta etapa em duas sessões do CLI marcadas como **[Assinatura 1]** e **[Assinatura 5]** porque os gateways estão em assinaturas diferentes. Para alternar entre assinaturas, use 'az account list --all' a fim de listar as assinaturas disponíveis para sua conta e use 'az account set --subscription<subscriptionID>' a fim de alternar para a assinatura que você deseja usar.

1. **[Assinatura 1]**  Faça login e conecte-se à Assinatura 1. Execute o seguinte comando para obter o nome e a ID do Gateway da saída:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  Copie a saída para "id:". Envie a ID e o nome do gateway de rede virtual (VNet1GW) para o administrador da Assinatura 5 por email ou outro método.

  Saída de exemplo:

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
  ```

2. **[Assinatura 5]**  Faça login e conecte-se à Assinatura 5. Execute o seguinte comando para obter o nome e a ID do Gateway da saída:

  ```azurecli
  az network vnet-gateway show -n VNet5GW -g TestRG5
  ```

  Copie a saída para "id:". Envie a ID e o nome do gateway de rede virtual (VNet5GW) para o administrador da Assinatura 1 por email ou outro método.

3. **[Assinatura 1]** Nesta etapa, você criará a conexão de TestVNet1 para TestVNet5. Você pode usar seus próprios valores para a chave compartilhada. No entanto, a chave compartilhada deve corresponder às duas conexões. Criar uma conexão pode levar alguns minutos para ser concluída. Conecte-se à Assinatura 1.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```

4. **[Assinatura 5]** Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet5 para TestVNet1. Verifique se as chaves compartilhadas correspondem e se você se conectou à Assinatura 5.

  ```azurecli
  az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```

## <a name="verify"></a>Verificar as conexões
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections v2v cli](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>Perguntas frequentes sobre Rede Virtual para Rede Virtual
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, confira a [Documentação sobre Máquinas Virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
