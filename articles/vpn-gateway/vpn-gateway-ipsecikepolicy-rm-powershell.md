---
title: "Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet: Azure Resource Manager: PowerShell | Microsoft Docs"
description: "Este artigo orienta na configuração da política IPsec/IKE para conexões S2S ou VNet-para-VNet  com gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: yushwang
ms.openlocfilehash: edeaec04c040d0cbe419f357541915b56c2c33b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet

Este artigo orienta as etapas para configurar a política IPsec/IKE para VPN Site a Site ou conexões VNet para VNet usando o modelo de implantação do Resource Manager e o PowerShell.

## <a name="about"></a>Sobre os parâmetros de política IKE e IPsec para os gateways de VPN do Azure
O padrão de protocolo IKE e IPsec suporta uma ampla gama de algoritmos criptográficos em várias combinações. Consulte [Sobre os requisitos de criptografia e gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir entre locais e a conectividade VNet opara VNet atender a seus requisitos de conformidade ou de segurança.

Este artigo fornece instruções para criar e configurar uma política de IPsec/IKE e se aplicam a uma conexão nova ou existente:

* [Parte 1 - Fluxo de trabalho para criar e definir a política de IPsec/IKE](#workflow)
* [Parte 2 - Suporte para algoritmos de criptografia e restrições de chave](#params)
* [Parte 3 - Criar uma nova conexão de VPN S2S com a política de IPsec/IKE](#crossprem)
* [Parte 4 - Criar uma nova conexão de VNet para VNet com a política de IPsec/IKE](#vnet2vnet)
* [Parte 5 - Gerenciar (criar, adicionar, remover) política IPsec/IKE para uma conexão](#managepolicy)

> [!IMPORTANT]
> 1. Observe que a política IPsec/IKE só funciona nas seguintes SKUs de gateway:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (baseado em rotas)
>    * ***Standard*** e ***HighPerformance*** (baseado em rotas)
> 2. Você só pode especificar ***uma*** combinação de políticas para uma determinada conexão.
> 3. Você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). A especificação de política parcial não é permitida.
> 4. Consulte as especificações do fornecedor do dispositivo VPN para garantir que a política tem suporte em seus dispositivos VPN local. S2S ou conexões VNet para VNet não podem estabelecer se as políticas são incompatíveis.

## <a name ="workflow"></a>Parte 1 - Fluxo de trabalho para criar e definir a política de IPsec/IKE
Esta seção descreve o fluxo de trabalho para criar e atualizar a política de IPsec/IKE em uma conexão VPN S2S ou VNet para VNet:
1. Criar uma rede virtual e um gateway de VPN
2. Criar um gateway de rede local para a conexão cruzada local ou outra rede virtual e o gateway de conexão VNet para VNet
3. Criar uma política de IPsec/IKE com parâmetros e algoritmos selecionados
4. Criar uma conexão (IPsec ou VNet para VNet) com a política de IPsec/IKE
5. Adicionar/atualizar/remover uma política de IPsec/IKE para uma conexão existente

As instruções neste artigo ajudam você a instalar e configurar políticas de IPsec/IKE, conforme mostrado no diagrama:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Parte 2 - Suporte para algoritmos de criptografia e restrições de chave

A tabela a seguir lista os algoritmos de criptografia compatíveis e restrições de chave configuráveis pelos clientes:

| **IPsec/IKEv2**  | **Opções**    |
| ---  | --- 
| Criptografia IKEv2 | AES256, AES192, AES128, DES3, DES  
| Integridade do IKEv2  | SHA384, SHA256, SHA1, MD5  |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nenhum |
| Criptografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, nenhum 
| Tempo de vida da QM SA   | (**Opcional**: os valores padrão serão usados se não for especificados)<br>Segundos (inteiro; **mínimo de 300**/padrão de 27000 segundos)<br>KBytes (inteiro; **mín. de 1024** /padrão de 102400000 KBytes)   |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors** ($True/$False; **Optional**, padrão$False, se não especificado)    |
|  |  |

> [!IMPORTANT]
> 1. **A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros que você especifica na política de IPsec/IKE do Azure:**
>    * Algoritmo de criptografia IKE (Modo Principal/Fase 1)
>    * Algoritmo de integridade IKE (Modo Principal/Fase 1)
>    * Grupo DH (Modo Principal/Fase 1)
>    * Algoritmo de criptografia IPsec (Modo Rápido/Fase 2)
>    * Algoritmo de integridade IPsec (Modo Rápido/Fase 2)
>    * Grupo PFS (Modo Rápido/Fase 2)
>    * Seletor de Tráfego (se UsePolicyBasedTrafficSelectors for usada)
>    * Os tempos de vida da SA são apenas especificações locais, portanto não precisam ser correspondentes.
>
> 2. **Se GCMAES for usado como para o algoritmo de Criptografia IPsec, você deverá selecionar o mesmo algoritmo GCMAES e o comprimento de chave para integridade IPsec, por exemplo, usando GCMAES128 para ambos**
> 3. Na tabela acima:
>    * IKEv2 corresponde ao Modo Principal ou Fase 1
>    * IPsec corresponde ao Modo Rápido ou Fase 2
>    * O Grupo DH especifica o Grupo Diffie-Hellmen usado no Modo Principal ou Fase 1
>    * O Grupo PFS especificou o Grupo Diffie-Hellmen usado no Modo Rápido ou Fase 2
> 4. O tempo de vida da SA de modo principal IKEv2 é fixo em 28.800 segundos nos gateways de VPN do Azure
> 5. Configurar "UsePolicyBasedTrafficSelectors" como $True em uma conexão configurará o gateway de VPN do Azure para conectar-se ao firewall VPN com base em política localmente. Caso habilite PolicyBasedTrafficSelectors, você precisa garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede local) de/para prefixos de rede virtual 'do Azure, em vez de "qualquer para qualquer". Por exemplo, se os prefixos da rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos da rede virtual são 192.168.0.0/16 e 172.16.0.0/16, você precisa especificar os seguintes seletores de tráfego:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Consulte [Conectar dispositivos VPN baseados em várias políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre os seletores de tráfego baseados em políticas.

A tabela abaixo lista os Grupos Diffie-Hellman correspondentes que têm suporte na política personalizada:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP284       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte a [RFC3526](https://tools.ietf.org/html/rfc3526) e a [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name ="crossprem"></a>Parte 3 - Criar uma nova conexão de VPN S2S com a política de IPsec/IKE

Esta seção orienta você pelas etapas de criação de uma conexão VPN S2S com uma política de IPsec/IKE. As etapas a seguir criarão a conexão, conforme mostrado no diagrama:

![política de S2S](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Consulte [Criar uma conexão VPN S2S](vpn-gateway-create-site-to-site-rm-powershell.md) para obter instruções passo a passo mais detalhadas para criar uma conexão VPN S2S.

### <a name="before"></a>Antes de começar

* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets do PowerShell do Azure Resource Manager. Consulte [Visão geral do Azure PowerShell](/powershell/azure/overview) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="createvnet1"></a>Etapa 1 - Criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. Declare as variáveis

Para este exercício, começaremos declarando nossa variáveis. Certifique-se de substituir os valores pelos seus próprios ao configurar para a produção.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conectar à sua assinatura do Azure e criar um novo grupo de recursos

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Criar a rede virtual, o gateway de VPN e o gateway de rede local

O exemplo a seguir cria a rede virtual, TestVNet1, com três sub-redes e o gateway de VPN. Ao substituir valores, é importante você sempre nomear sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>Parte 2 - Criar uma nova conexão de VPN S2S com a política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo a seguir cria uma política de IPsec/IKE com os parâmetros e os algoritmos seguintes:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS Nenhum, Tempo de Vida da SA de 7200 segundos e 102400000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 7200 -SADataSizeKilobytes 102400000
```

Se você usar GCMAES para IPsec, deverá usar o mesmo algoritmo GCMAES e comprimento de chave tanto para criptografia quanto para integridade IPsec, por exemplo:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: **GCMAES256, GCMAES256**, PFS Nenhum, Tempo de Vida da SA de 7200 segundos e 102400000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256 -PfsGroup None -SALifeTimeSeconds 7200 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Criar a nova conexão de VPN S2S com a política de IPsec/IKE

Criar uma conexão VPN S2S e aplicar a política de IPsec/IKE criada anteriormente.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcionalmente, você pode adicionar "-UsePolicyBasedTrafficSelectors $True" ao criar conexão cmdlet para habilitar o gateway de VPN do Azure para se conectar a dispositivos VPN baseado em políticas locais, conforme descrito acima.

> [!IMPORTANT]
> Quando uma política de IPsec/IKE é especificada em uma conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos de criptografia especificados e as restrições de chave naquela conexão determinada. Garanta que o dispositivo VPN local para a conexão use ou aceite a combinação de política exata, caso contrário, o túnel VPN S2S não será estabelecido.


## <a name ="vnet2vnet"></a>Parte 4 - Criar uma nova conexão de VNet para VNet com a política de IPsec/IKE

As etapas de criação de uma conexão VNet para VNet com uma política de IPsec/IKE são semelhantes de uma conexão VPN S2S. Os scripts de exemplo a seguir criam a conexão, conforme mostrado no diagrama:

![política de V2V](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Consulte [Criar uma conexão VNet para VNet](vpn-gateway-vnet-vnet-rm-ps.md) para obter etapas mais detalhadas para criar uma conexão VNet para VNet. Você deve concluir a [Parte 3](#crossprem) para criar e configurar TestVNet1 e o Gateway de VPN.

### <a name="createvnet2"></a>Etapa 1 - Criar a segunda rede virtual e um gateway de VPN

#### <a name="1-declare-your-variables"></a>1. Declare as variáveis

Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Criar a segunda rede virtual e o gateway de VPN no novo grupo de recursos

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Etapa 2 - Criar uma conexão VNet para VNet com a política de IPsec/IKE

Semelhante à conexão VPN S2S, criar uma política de IPsec/IKE e em seguida aplicar à política para a nova conexão.

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

O script de exemplo a seguir cria uma política de IPsec/IKE diferente com os parâmetros e os algoritmos seguintes:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, tempo de vida do SA 7200 segundos e 4096KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 4096
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Criar conexões VNet para VNet com a política de IPsec/IKE

Crie uma conexão VNet para VNet e apliaque a política de IPsec/IKE criada por você. Neste exemplo, ambos os gateways estão na mesma assinatura. Portanto, é possível criar e configurar ambas conexões com a mesma política de IPsec/IKE na mesma sessão do PowerShell.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Quando uma política de IPsec/IKE é especificada em uma conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos de criptografia especificados e as restrições de chave naquela conexão determinada. Garanta que as políticas de IPsec para as duas conexões sejam as mesmas, caso contrário, a conexão VNet para VNet não será estabelecida.

Depois de concluir essas etapas, a conexão é estabelecida em alguns minutos e você terá a seguinte topologia de rede conforme mostrado no início:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Parte 5 - Atualizar política de IPsec/IKE para uma conexão

A última seção mostra como gerenciar a política de IPsec/IKE para uma conexão existente S2S ou VNet para VNet. O exercício a seguir percorre as seguintes operações em uma conexão:

1. Mostrar a política de IPsec/IKE de uma conexão
2. Adicionar ou atualizar a política de IPsec/IKE para uma conexão
3. Remover a política de IPsec/IKE de uma conexão

As mesmas etapas se aplicam a conexões S2S e VNet para VNet.

> [!IMPORTANT]
> A política de IPsec/IKE tem suporte somente nos gateways de VPN baseados en rota *Standard* e *HighPerformance*. Ele não funciona no SKU de gateway básico ou o gateway de VPN baseado em políticas.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrar a política de IPsec/IKE de uma conexão

O exemplo a seguir mostra como obter a política de IPsec/IKE configurada em uma conexão. Os scripts também continuam de exercícios acima.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

O último comando lista a atual política de IPsec/IKE configurada sobre a conexão, se houver alguma. A saída de exemplo a seguir é para a conexão:

```powershell
SALifeTimeSeconds   : 3600
SADataSizeKilobytes : 2048
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Se não houver nenhuma política de IPsec/IKE configurado, o comando (PS > $connection6.policy) receberá retorno vazio. Isso não significa que IPsec/IKE não está configurado na conexão, mas que não há nenhuma política de IPsec/IKE personalizada. A conexão real usa a política padrão negociada entre o dispositivo VPN no local e o gateway de VPN do Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Adicionar e atualizar uma política de IPsec/IKE para uma conexão

As etapas para adicionar uma política nova ou atualizar uma política existente em uma conexão são as mesmas: criar uma nova política, aplicar a nova política para a conexão.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup None -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Para habilitar o "UsePolicyBasedTrafficSelectors" ao se conectar a um dispositivo VPN com base na política local, adicione o parâmetro "-UsePolicyBaseTrafficSelectors" ao cmdlet, ou defina-o como $False para desabilitar a opção:

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Você pode obter a conexão novamente para verificar se a política está atualizada.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Você deve ver a saída da última linha, conforme mostrado no exemplo abaixo:

```powershell
SALifeTimeSeconds   : 3600
SADataSizeKilobytes : 2048
IpsecEncryption     : GCMAES128
IpsecIntegrity      : GCMAES128
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14--
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Remover uma política de IPsec/IKE de uma conexão

Após a remoção da política personalizada de uma conexão, o gateway de VPN do Azure reverterá para a [lista padrão de propostas de IPsec/IKE](vpn-gateway-about-vpn-devices.md) e renegociará novamente com o seu dispositivo VPN local.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Você pode usar o mesmo script para verificar se a política foi removida da conexão.

## <a name="next-steps"></a>Próximas etapas

Consulte [Conectar dispositivos VPN baseados em várias políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os seletores de tráfego baseado em políticas.

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.