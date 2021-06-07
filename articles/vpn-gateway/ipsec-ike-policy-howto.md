---
title: 'Política de IPsec/IKE para VPN S2S & conexões VNet para VNet: portal do Azure'
titleSuffix: Azure VPN Gateway
description: Configure a política de IPsec/IKE para conexões S2S ou VNet para VNet com gateways de VPN do Azure usando Azure Resource Manager e portal do Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: 2b298185866d16da02fe8d3b3fdb41f0b0b1f726
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878537"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Configurar a política de IPsec/IKE para conexões VPN S2S ou VNet para VNet: portal do Azure

Este artigo orienta você pelas etapas para configurar a política de IPsec/IKE para conexões VPN site a site de gateway de VPN ou VNet para VNet usando o portal do Azure. As seções a seguir ajudam a criar e configurar uma política de IPsec/IKE e aplicar a política a uma conexão nova ou existente.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Sobre parâmetros de política IKE e IPsec

O padrão de protocolo IKE e IPsec suporta uma ampla gama de algoritmos criptográficos em várias combinações. Consulte [sobre os requisitos de criptografia e os gateways de VPN do Azure](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir a conectividade entre locais e VNet a vnet para atender aos seus requisitos de conformidade ou de segurança.

Este artigo fornece instruções para criar e configurar uma política de IPsec/IKE e aplicá-la a uma conexão de gateway de VPN nova ou existente.

### <a name="considerations"></a>Considerações

* A política de IPsec/IKE só funciona nas seguintes SKUs de gateway:
  * ***VpnGw1 ~ 5 e VpnGw1AZ ~ 5AZ***
  * ***Standard** _ e _ *_HighPerformance_**
* Você só pode especificar ***uma*** combinação de políticas para uma determinada conexão.
* Você deve especificar todos os algoritmos e parâmetros para IKE (modo principal) e IPsec (modo rápido). A especificação de política parcial não é permitida.
* Consulte as especificações do fornecedor do dispositivo VPN para garantir que a política tem suporte em seus dispositivos VPN local. S2S ou conexões VNet para VNet não podem estabelecer se as políticas são incompatíveis.

## <a name="workflow"></a><a name ="workflow"></a>Fluxo de trabalho

Esta seção descreve o fluxo de trabalho para criar e atualizar a política de IPsec/IKE em uma conexão VPN S2S ou VNet para VNet:

1. Crie uma rede virtual e um gateway de VPN.
2. Crie um gateway de rede local para conexão entre locais ou outra rede virtual e gateway para conexão VNet a VNet.
3. Crie uma conexão (IPsec ou VNet2VNet).
4. Configurar/atualizar/remover a política de IPsec/IKE nos recursos de conexão.

As instruções neste artigo ajudam você a configurar e configurar as políticas de IPsec/IKE, conforme mostrado no diagrama:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagrama de política de IPsec/IKE" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Algoritmos de criptografia com suporte & forças de chave

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmos e chaves

A tabela a seguir lista os algoritmos de criptografia compatíveis e restrições de chave configuráveis pelos clientes:

| **IPsec/IKE**    | **Opções**    |
| ---              | ---            |
| Criptografia IKE   | AES256, AES192, AES128, DES3, DES                  |
| Integridade do IKE    | SHA384, SHA256, SHA1, MD5                          |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nenhum |
| Criptografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, nenhum   |
| Tempo de vida da QM SA   | (**Opcional**: os valores padrão serão usados se não for especificados)<br>Segundos (inteiro; **mínimo de 300**/padrão de 27000 segundos)<br>KBytes (inteiro; **mín. de 1024** /padrão de 102400000 KBytes)    |
| Seletor de tráfego | UsePolicyBasedTrafficSelectors** ($True/$False; **Optional**, padrão$False, se não especificado)    |
| Tempo limite de DPD      | Segundos (número inteiro: mín. 9/máx. 3600; padrão 45 segundos) |
|  |  |

#### <a name="important-requirements"></a>Requisitos importantes

* A configuração do dispositivo VPN local deve corresponder ou conter os seguintes algoritmos e parâmetros que você especifica na política de IPsec/IKE do Azure:
  * Algoritmo de criptografia IKE (Modo Principal/Fase 1)
  * Algoritmo de integridade IKE (Modo Principal/Fase 1)
  * Grupo DH (Modo Principal/Fase 1)
  * Algoritmo de criptografia IPsec (Modo Rápido/Fase 2)
  * Algoritmo de integridade IPsec (Modo Rápido/Fase 2)
  * Grupo PFS (modo rápido/fase 2) > * seletor de tráfego (se UsePolicyBasedTrafficSelectors for usado)
  * Os tempos de vida da SA são apenas especificações locais, portanto não precisam ser correspondentes.

* Se GCMAES for usado como para o algoritmo de criptografia IPsec, você deverá selecionar o mesmo algoritmo GCMAES e o comprimento da chave para a integridade do IPsec; por exemplo, usando GCMAES128 para ambos.

* Na [tabela de algoritmos e chaves](#table1) acima:
  * O IKE corresponde ao modo principal ou à fase 1
  * IPsec corresponde ao Modo Rápido ou Fase 2
  * O Grupo DH especifica o Grupo Diffie-Hellmen usado no Modo Principal ou Fase 1
  * O Grupo PFS especificou o Grupo Diffie-Hellmen usado no Modo Rápido ou Fase 2

* O tempo de vida da SA do modo principal IKE é fixo em 28.800 segundos nos gateways de VPN do Azure.

* Se você definir **UsePolicyBasedTrafficSelectors** como $true em uma conexão, ele configurará o gateway de VPN do Azure para se conectar ao firewall de VPN baseado em políticas no local. Caso habilite PolicyBasedTrafficSelectors, você precisa garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações de prefixos de rede local (gateway de rede local) de/para prefixos de rede virtual 'do Azure, em vez de "qualquer para qualquer". Por exemplo, se os prefixos da rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos da rede virtual são 192.168.0.0/16 e 172.16.0.0/16, você precisa especificar os seguintes seletores de tráfego:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Consulte [Conectar dispositivos VPN baseados em várias políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre os seletores de tráfego baseados em políticas.

* Tempo limite de DPD-o valor padrão é 45 segundos em gateways de VPN do Azure. Definir o tempo limite como períodos mais curtos fará com que o IKE seja reinicializado mais agressivamente, fazendo com que a conexão pareça estar desconectada em algumas instâncias. Isso pode não ser desejável se suas localizações locais estiverem distantes da região do Azure onde o gateway de VPN reside ou se a condição de link físico puder causar perda de pacotes. A recomendação geral é definir o tempo limite entre **30 a 45** segundos.

### <a name="diffie-hellman-groups"></a>Grupos de Diffie-Hellman

A tabela abaixo lista os Grupos Diffie-Hellman correspondentes que têm suporte na política personalizada:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte a [RFC3526](https://tools.ietf.org/html/rfc3526) e a [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>VPN S2S com política de IPsec/IKE

Esta seção orienta você pelas etapas para criar uma conexão VPN site a site com uma política de IPsec/IKE. As etapas a seguir criam a conexão, conforme mostrado no diagrama a seguir:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Política site a site" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Etapa 1 - Criar a rede virtual, o gateway de VPN e o gateway de rede local

Crie os recursos a seguir, conforme mostrado nas capturas de tela abaixo. Para obter as etapas, consulte [criar uma conexão VPN site a site](./tutorial-site-to-site-portal.md).

* **Rede virtual:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="VNET":::

* **Gateway de VPN:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Gateway":::

* **Gateway de rede local:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Site":::

* **Conexão:** VNet1 Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Conexão":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Etapa 2-configurar a política de IPsec/IKE na conexão VPN S2S

Nesta seção, configure uma política de IPsec/IKE com os seguintes algoritmos e parâmetros:

* IKE: AES256, SHA384, DHGroup24, DPD tempo limite de 45 segundos
* IPsec: AES256, SHA256, PFS nenhum, tempo de vida de SA de 30000 segundos e 102400000 KB

1. Navegue até o recurso de conexão, **VNet1toSite6**, no portal do Azure. Selecione a página de **configuração** e selecione política de IPSec/IKE **personalizada** para mostrar todas as opções de configuração. A captura de tela abaixo mostra a configuração de acordo com a lista:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Site 6":::

1. Se você usar GCMAES para IPsec, deverá usar o mesmo algoritmo GCMAES e comprimento de chave tanto para criptografia quanto para integridade IPsec. Por exemplo, a captura de tela abaixo especifica GCMAES128 para criptografia IPsec e integridade IPsec:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="GCMAES para IPsec":::

1. Opcionalmente, você pode selecionar **habilitar** para a opção **usar seletores de tráfego com base em política** para habilitar o gateway de VPN do Azure para se conectar a dispositivos VPN baseados em políticas localmente, conforme descrito acima.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Seletor de tráfego baseado em política":::

1. Depois que todas as opções forem selecionadas, selecione **salvar** para confirmar as alterações no recurso de conexão. A política será imposta em cerca de um minuto.

> [!IMPORTANT]
>
> * Quando uma política de IPsec/IKE é especificada em uma conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos de criptografia especificados e as restrições de chave naquela conexão determinada. Garanta que o dispositivo VPN local para a conexão use ou aceite a combinação de política exata, caso contrário, o túnel VPN S2S não será estabelecido.
>
> * **Seletor de tráfego baseado em políticas** e opções de **tempo limite DPD** podem ser especificados com a política **padrão** , sem a política de IPSec/IKE personalizada, conforme mostrado na captura de tela acima.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet a VNet com política de IPsec/IKE

As etapas para criar uma conexão de VNet para VNet com uma política de IPsec/IKE são semelhantes às de uma conexão VPN S2S.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Diagrama de política de VNet para VNet" border="false":::

1. Use as etapas no artigo [criar uma conexão vnet a vnet](vpn-gateway-vnet-vnet-rm-ps.md) para criar sua conexão de VNet para vnet.

2. Depois de concluir as etapas, você verá duas conexões de VNet para VNet, conforme mostrado na captura de tela abaixo do recurso VNet2GW:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Conexões de rede virtual a rede virtual":::

3. Navegue até o recurso de conexão e vá para a página de **configuração** no Portal. Selecione **personalizado** na **política de IPSec/IKE** para mostrar as opções de política personalizadas. Selecione os algoritmos criptográficos com os comprimentos de chave correspondentes.

   A captura de tela mostra uma política de IPsec/IKE diferente com os seguintes algoritmos e parâmetros:
   * IKE: AES128, SHA1, DHGroup14, DPD tempo limite de 45 segundos
   * IPsec: GCMAES128, GCMAES128, PFS14, tempo de vida do SA 14400 segundos e 102400000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Política de Conexão":::

4. Selecione **salvar** para aplicar as alterações de política no recurso de conexão.

5. Aplique a mesma política ao outro recurso de conexão, VNet2toVNet1. Se você não fizer isso, o túnel VPN IPsec/IKE não se conectará devido à incompatibilidade de política.

   > [!IMPORTANT]
   > Quando uma política de IPsec/IKE é especificada em uma conexão, o gateway de VPN do Azure só irá enviar ou aceitar a proposta de IPsec/IKE com algoritmos de criptografia especificados e as restrições de chave naquela conexão determinada. Garanta que as políticas de IPsec para as duas conexões sejam as mesmas, caso contrário, a conexão VNet para VNet não será estabelecida.

6. Depois de concluir essas etapas, a conexão será estabelecida em alguns minutos e você terá a seguinte topologia de rede:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagrama de política de IPsec/IKE" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Para remover a política de IPsec/IKE personalizada de uma conexão

1. Para remover uma política personalizada de uma conexão, navegue até o recurso de conexão e acesse a página de **configuração** para ver a política atual.

2. Selecione **padrão** na opção de **política de IPSec/IKE** . Isso removerá todas as políticas personalizadas especificadas anteriormente na conexão e restaurará as configurações padrão de IPsec/IKE nesta conexão:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Excluir política":::

3. Selecione **salvar** para remover a política personalizada e restaurar as configurações padrão de IPSec/IKE na conexão.

## <a name="next-steps"></a>Próximas etapas

Consulte [Conectar dispositivos VPN baseados em várias políticas locais](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os seletores de tráfego baseado em políticas.