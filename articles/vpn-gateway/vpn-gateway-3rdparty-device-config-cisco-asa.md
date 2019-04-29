---
title: Exemplo de configuração para conexão de dispositivos Cisco ASA a gateways de VPN do Azure | Microsoft Docs
description: Este artigo apresenta um exemplo de configuração para conectar dispositivos Cisco ASA a gateways de VPN do Azure.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 10/19/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: e575fac61a1c5d9351391d39d200b87e34ff26cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648731"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Configuração de exemplo: Dispositivo Cisco ASA (IKEv2/sem BGP)
Este artigo apresenta exemplos de configuração para conectar dispositivos Cisco ASA (Adaptive Security Appliance) a gateways de VPN do Azure. O exemplo se aplica a dispositivos Cisco ASA que estão executando IKEv2 sem BGP (Border Gateway Protocol). 

## <a name="device-at-a-glance"></a>Visão rápida do dispositivo

|                        |                                   |
| ---                    | ---                               |
| Fornecedor do dispositivo          | Cisco                             |
| Modelo do dispositivo           | ASA                               |
| Versão de destino         | 8.4 e posterior                     |
| Modelo testado           | ASA 5505                          |
| Versão testada         | 9.2                               |
| Versão do IKE            | IKEv2                             |
| BGP                    | Não                                 |
| Tipo de gateway VPN do Azure | Gateway VPN baseado em rota           |
|                        |                                   |

> [!NOTE]
> A configuração de exemplo conecta um dispositivo Cisco ASA um gateway VPN **baseado em rota** do Azure. A conexão usa uma política de IPsec/IKE personalizada com a opção **UsePolicyBasedTrafficSelectors**, conforme descrito [neste artigo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> O exemplo exige que os dispositivos ASA usem a política **IKEv2** com configurações baseadas em lista de acesso, não baseadas em VTI. Consulte as especificações do fornecedor do seu dispositivo VPN para garantir que a política IKEv2 tenha suporte em seus dispositivos VPN locais.


## <a name="vpn-device-requirements"></a>Requisitos do dispositivo VPN
Gateways VPN do Azure usam conjuntos padrão de protocolo IPsec/IKE para estabelecer túneis VPN S2S (Site a Site). Para obter os parâmetros detalhados de protocolo IPsec/IKE e os algoritmos de criptografia padrão para gateways VPN do Azure, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) .

> [!NOTE]
> Opcionalmente, você pode especificar uma combinação exata de algoritmos de criptografia e forças de chave para uma conexão específica, conforme descrito em [Sobre os requisitos de criptografia](vpn-gateway-about-compliance-crypto.md). Se você especificar uma combinação exata de algoritmos e restrições de chave, use as especificações correspondentes em seus dispositivos VPN.

## <a name="single-vpn-tunnel"></a>Túnel VPN único
Essa configuração é composta por um único túnel VPN S2S entre um gateway de VPN do Azure e um dispositivo VPN local. Opcionalmente, você pode configurar o BGP por meio do túnel VPN.

![Túnel VPN S2S único](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Para obter instruções sobre como compilar as configurações do Azure, confira [Configuração de túnel VPN único](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informações de gateway de VPN e de rede virtual
Esta seção lista os parâmetros para o exemplo.

| **Parâmetro**                | **Valor**                    |
| ---                          | ---                          |
| Prefixos de endereço de rede virtual        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP do gateway de VPN do Azure         | Azure_Gateway_Public_IP      |
| Prefixos de endereço local | 10.51.0.0/16<br>10.52.0.0/16 |
| IP do dispositivo VPN local    | OnPrem_Device_Public_IP     |
| * Rede virtual de ASN de BGP                | 65010                        |
| *IP do par do BGP do Azure           | 10.12.255.30                 |
| * ASN de BGP local         | 65050                        |
| * IP de par no BGP local     | 10.52.255.254                |
|                              |                              |

\\* Parâmetro opcional apenas para BGP.

### <a name="ipsecike-policy-and-parameters"></a>Parâmetros e política de IPsec/IKE
A tabela a seguir lista os algoritmos e os parâmetros de IPsec/IKE usados no exemplo. Consulte as especificações de seu dispositivo VPN para verificar os algoritmos suporte para seus modelos de dispositivo de VPN e as versões de firmware.

| **IPsec/IKEv2**  | **Valor**                            |
| ---              | ---                                  |
| Criptografia IKEv2 | AES256                               |
| Integridade do IKEv2  | SHA384                               |
| Grupo DH         | DHGroup24                            |
| * Criptografia IPsec | AES256                               |
| * Integridade do IPsec  | SHA1                                 |
| Grupo PFS        | PFS24                                |
| Tempo de vida da QM SA   | 7.200 segundos                         |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors $True |
| Chave Pré-Compartilhada   | PreSharedKey                         |
|                  |                                      |

\*Em alguns dispositivos, a Integridade do IPsec deverá ser um valor nulo quando o algoritmo de Criptografia IPsec for AES-GCM.

### <a name="asa-device-support"></a>Suporte a dispositivo ASA

* O suporte ao IKEv2 exige ASA versão 8.4 e posterior.

* O suporte para Grupo DH e Grupo PFS além do Grupo 5 exige ASA versão 9.x.

* O suporte para Criptografia de IPsec com AES-GCM e Integridade de IPsec com SHA-256, SHA-384 e SHA-512, exige ASA versão 9.x. Esse requisito de suporte se aplica a dispositivos ASA mais recentes. No momento da publicação, modelos ASA 5505, 5510, 5520, 5540, 5550 e 5580 não dão suporte a esses algoritmos. Consulte as especificações de seu dispositivo VPN para verificar os algoritmos suporte para seus modelos de dispositivo de VPN e as versões de firmware.


### <a name="sample-device-configuration"></a>Exemplo de configuração de dispositivo
O script fornece um exemplo que tem base na configuração e nos parâmetros descritos nas seções anteriores. A configuração de túnel VPN S2S consiste nas seguintes partes:

1. Interfaces e rotas
2. Listas de acesso
3. Política e parâmetros IKE (fase 1 ou modo principal)
4. Política e parâmetros IPsec (fase 2 ou modo rápido)
5. Outros parâmetros, como fixação de MSS TCP

> [!IMPORTANT]
> Conclua as etapas a seguir antes de usar o exemplo de Conclua as etapas a seguir antes de usar o script de exemplo. Substitua os valores de espaço reservado no script pelas configurações de dispositivo para sua configuração.

* Especifique a configuração de interface para as interfaces interna e externa.
* Identifique as rotas para suas redes internas/privadas e externas/públicas.
* Assegure que todos os nomes e números de política sejam exclusivos em seu dispositivo.
* Assegure que os algoritmos de criptografia tenham suporte em seu dispositivo.
* Substitua os seguintes **valores de espaço reservado** por valores reais para sua configuração:
  - Nome da interface externa: **externa**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Nomes do gateway de rede local e da rede virtual: **VNetName** e **LNGName**
  - **Prefixos** de endereço de rede local e de rede virtual
  - **Máscaras de rede** adequadas

#### <a name="sample-script"></a>Script de exemplo

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Comandos de depuração simples

Use estes comandos ASA para fins de depuração:

* Mostra a associação de segurança IPsec ou IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Entre no modo de depuração:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Os comandos `debug` podem gerar uma saída significativa no console.

* Mostra as configurações atuais no dispositivo:
    ```
    show run
    ```
    Use subcomandos `show` para listar partes específicas da configuração do dispositivo, por exemplo:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Próximas etapas
Para configurar conexões de VNet a VNet e ativo-ativo entre locais, consulte [Configurar gateways VPN ativo-ativo](vpn-gateway-activeactive-rm-powershell.md).
<!--Update_Description: wording update -->