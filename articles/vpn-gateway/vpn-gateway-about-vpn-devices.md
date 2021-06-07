---
title: 'Gateway de VPN do Azure: sobre dispositivos VPN para conexões'
description: Este artigo discute dispositivos VPN e os parâmetros de IPsec para conexões de Gateway de VPN S2S entre locais. São fornecidos links para exemplos e instruções de configuração.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 4c6bd62e96d85305036626a8672c39ff1b9f6b26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201086"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Sobre dispositivos VPN e os parâmetros IPsec/IKE para conexões do Gateway de VPN site a site

Um dispositivo VPN é necessário para configurar uma conexão de VPN Site a Site (S2S) entre locais usando um gateway de VPN. As conexões Site a Site podem ser usadas para criar uma solução híbrida, ou sempre que você quiser uma conexão segura entre suas redes locais e virtuais. Este artigo fornece uma lista dispositivos de VPN validados e uma lista de parâmetros IPsec/IKE para gateways de VPN.

> [!IMPORTANT]
> Consulte [Problemas conhecidos de compatibilidade de dispositivos](#known) se você estiver com problemas de conectividade entre os dispositivos VPN locais e os gateways de VPN.
>

### <a name="items-to-note-when-viewing-the-tables"></a>Itens a ser observados ao exibir as tabelas:

* Houve uma mudança de terminologia para os gateways de VPN do Azure. Somente os nomes mudaram. Não há nenhuma alteração de funcionalidade.
  * Roteamento estático = PolicyBased
  * Roteamento dinâmico = RouteBased
* As especificações de gateway de VPN de Alto Desempenho e de gateway de VPN RouteBased são as mesmas, salvo indicação em contrário. Por exemplo, os dispositivos VPN validados compatíveis com os gateways de VPN RouteBased também são compatíveis com o gateway de VPN de Alto Desempenho.

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>Dispositivos VPN validados e guias de configuração do dispositivo

Validamos um conjunto de dispositivos VPN padrão em parceria com fornecedores de dispositivos. Todos os dispositivos nas famílias de dispositivos na lista abaixo devem funcionar com os gateways de VPN. Consulte [Sobre configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype) para entender o uso do tipo de VPN (PolicyBased ou RouteBased) para a solução de Gateway de VPN que você deseja configurar.

Para ajudar a configurar seu dispositivo VPN, consulte os links que correspondem à família de dispositivos apropriada. Os links para as instruções de configuração são fornecidos em uma base de melhor esforço. Para obter suporte ao dispositivo VPN, entre em contato com o fabricante do dispositivo.

|**Fornecedor**          |**Família de dispositivos**     |**Versão mínima do SO** |**Instruções de configuração PolicyBased** |**Instruções de configuração RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Não compatível  |[Guia de configuração](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |Série AR de roteadores VPN |Série AR 5.4.7 e superior               | [Guia de configuração](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[Guia de configuração](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | Roteador CloudEOS | vEOS 4.24.0 FX | (não testado) | [Guia de configuração](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Firewall do Barracuda CloudGen |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Guia de configuração](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[Guia de configuração](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Ponto de Verificação |Gateway de segurança |R80.10 |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Guia de configuração](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Com suporte |[Guia de configuração *](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Com suporte |Com suporte |
| Cisco | CSR | RouteBased: IOS-XE 16,10 | (não testado) | [Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Com suporte |Com suporte |
| Cisco |Meraki (MX) | 15.12 MX v |Não compatível | [Guia de configuração](https://documentation.meraki.com/MX/Site-to-site_VPN/Configuring_Site_to_Site_VPN_tunnels_to_Azure_VPN_Gateway) |
| Cisco | vEdge (so Viptela) | 18.4.0 (modo ativo/passivo)<br><br>19,2 (modo ativo/ativo) | Não compatível |  [Configuração manual (ativa/passiva)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[Configuração de onrampa na nuvem (ativa/ativa)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 e acima |[Guia de configuração](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Não compatível |
| F5 |Série BIG-IP |12.0 |[Guia de configuração](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Guia de configuração](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (não testado) |[Guia de configuração](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone Networks | Firewalls da Next-Gen (NGFW) | 5.5 R7  | (não testado) | [Guia de configuração](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Guia de configuração](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Não compatível |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Com suporte |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |Série J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Com suporte |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Com suporte |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Com suporte |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Com suporte |[Script de configuração](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Serviço de Roteamento e Acesso Remoto |Windows Server 2012 |Não compatível |Com suporte |
| AG de sistemas abertos |Gateway de Segurança de Controle de Missão |N/D |[Guia de configuração](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |Não compatível |
| Redes Palo Alto |Todos os dispositivos executando PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 ou posterior<br>RouteBased: 7.1.4 |Com suporte |[Guia de configuração](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (desenvolvedor) | VyOS | VyOS 1.2.2 | (não testado) | [Guia de configuração ](https://docs.vyos.io/en/latest/configexamples/azure-vpn-bgp.html)|
| ShareTech | Próxima geração de UTM (série NU) | 9.0.1.3 | Não compatível | [Guia de configuração](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Série TZ, série NSA<br>Série SuperMassive<br>Série NSA E-Class |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Não compatível |[Guia de configuração](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | Firewall XG de última geração | XG v17 | (não testado) | [Guia de configuração](https://community.sophos.com/kb/127546)<br><br>[Guia de configuração – Várias SAs](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM 1.1.5/VpnPlusServer-1.2.0 | (não testado) | [Guia de configuração](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | (não testado) | [BGP em IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI em IKEv2/IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0. T3 Build-13  | (não testado) | Guia de configuração |
| WatchGuard |Tudo |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Guia de configuração](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |Série ZyWALL USG<br>Série ZyWALL ATP<br>Série de VPN ZyWALL | ZLD v 4.32 + | (não testado) | [VTI em IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2648/)<br><br>[BGP em IKEv2/IPsec](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*) Adição de suporte a IKEv2 para do Cisco ASA versões 8.4+; pode se conectar ao gateway de VPN do Azure usando a política de IPsec/IKE personalizada com a opção "UsePolicyBasedTrafficSelectors". Consulte este [artigo de instruções](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\*\*) Os roteadores da Série ISR 7200 só oferecem suporte a VPNs PolicyBased.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Fazer o download de scripts de configuração do dispositivo VPN do Azure

Para determinados dispositivos, você pode fazer o download de scripts de configuração diretamente do Azure. Para saber mais e fazer download de instruções, confira [Fazer o download dos scripts de configuração de dispositivo de VPN](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Dispositivos com scripts de configuração disponíveis

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>Dispositivos VPN não validados

Se você não vir seu dispositivo listado na tabela de dispositivos VPN Validados, ele ainda pode funcionar com uma conexão Site a Site. Entre em contato com o fabricante do dispositivo para obter instruções adicionais de configuração e suporte.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>Edição de exemplos de configuração de dispositivo

Depois de baixar o exemplo de configuração de dispositivo VPN fornecido, você precisará substituir alguns dos valores para refletir as configurações do seu ambiente.

### <a name="to-edit-a-sample"></a>Para editar um exemplo:

1. Abra o exemplo usando o Bloco de Notas.
2. Pesquise e substitua todas as cadeias de caracteres de <*texto*> por valores condizentes com seu ambiente. Inclua < e >. Quando um nome é especificado, o nome que você selecionar deve ser exclusivo. Se um comando não funcionar, consulte a documentação do fabricante do dispositivo.

| **Texto de exemplo** | **Alterar para** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |O nome que você escolheu para este objeto. Exemplo: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |O nome que você escolheu para este objeto. Exemplo: myAzureNetwork |
| &lt;RP_AccessList&gt; |O nome que você escolheu para este objeto. Exemplo: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |O nome que você escolheu para este objeto. Exemplo: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |O nome que você escolheu para este objeto. Exemplo: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Especifique o intervalo. Exemplo: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Especificar máscara de sub-rede. Exemplo: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Especifique o intervalo local. Exemplo: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Especifique a máscara de sub-rede local. Exemplo: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Informações específicas à sua rede virtual e que estão localizadas no Portal de Gerenciamento como **Endereço IP do Gateway**. |
| &lt;SP_PresharedKey&gt; |Essas informações são específicas da sua rede virtual e estão localizadas no Portal de Gerenciamento como Gerenciar Chave. |

## <a name="default-ipsecike-parameters"></a><a name="ipsec"></a>Parâmetros padrão de IPsec/IKE

As tabelas a seguir contêm as combinações de algoritmos e parâmetros que os gateways de VPN do Azure usam na configuração padrão (**políticas padrão**). Para gateways de VPN baseados em rota criados usando o modelo de implantação do Azure Resource Manager, você pode especificar uma política personalizada em cada conexão individual. Confira [Configurar política de IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter instruções detalhadas.

Além disso, você deve fixe TCP **MSS** em **1350**. Ou, se os dispositivos VPN não derem suporte à fixação de MSS, como alternativa, você poderá definir o **MTU** na interface de túnel como **1400** bytes em vez disso.

Nas tabelas a seguir:

* SA = Associação de segurança
* Fase 1 de IKE também é chamada de "Modo Principal"
* Fase 2 de IKE também é chamada de "Modo Rápido"

### <a name="ike-phase-1-main-mode-parameters"></a>Parâmetros da Fase 1 de IKE (Modo Principal)

| **Propriedade**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Versão IKE           |IKEv1              |IKEv1 e IKEv2    |
| Grupo Diffie-Hellman  |Grupo 2 (1024 bits) |Grupo 2 (1024 bits) |
| Método de autenticação |Chave Pré-Compartilhada     |Chave Pré-Compartilhada     |
| Criptografia e algoritmos de hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Tempo de vida da SA           |28.800 segundos     |28.800 segundos     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parâmetros da Fase 2 de IKE (Modo Rápido)

| **Propriedade**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Versão IKE                   |IKEv1          |IKEv1 e IKEv2                              |
| Criptografia e algoritmos de hash |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Ofertas QM SA RouteBased](#RouteBasedOffers) |
| Tempo de vida da SA (Tempo)            |3.600 segundos  |27.000 segundos                               |
| Tempo de vida da SA (Bytes)           |102.400.000 KB |102.400.000 KB                               |
| PFS (Perfect Forward Secrecy) |Não             |[Ofertas QM SA RouteBased](#RouteBasedOffers) |
| Detecção de par inativo (DPD)     |Sem suporte  |Com suporte                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>Ofertas de associação de segurança de VPN RouteBased (SA IKE Modo Rápido)

A tabela a seguir lista as ofertas de SA do IPsec (IKE Modo Rápido). Ofertas estão listadas na ordem de preferência que a oferta é apresentada ou aceita.

#### <a name="azure-gateway-as-initiator"></a>Gateway do Azure como iniciador

|-  |**Criptografia**|**Autenticação**|**Grupo PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhum         |
| 2 |AES256        |SHA1              |Nenhum         |
| 3 |3DES          |SHA1              |Nenhum         |
| 4 |AES256        |SHA256            |Nenhum         |
| 5 |AES128        |SHA1              |Nenhum         |
| 6 |3DES          |SHA256            |Nenhum         |

#### <a name="azure-gateway-as-responder"></a>Gateway do Azure como respondente

|-  |**Criptografia**|**Autenticação**|**Grupo PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Nenhum         |
| 2 |AES256        |SHA1              |Nenhum         |
| 3 |3DES          |SHA1              |Nenhum         |
| 4 |AES256        |SHA256            |Nenhum         |
| 5 |AES128        |SHA1              |Nenhum         |
| 6 |3DES          |SHA256            |Nenhum         |
| 7 |DES           |SHA1              |Nenhum         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Nenhum         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Você pode especificar a criptografia NULL de IPsec ESP com gateways de VPN RouteBased e de Alto Desempenho. Criptografia com base em nulo não oferece proteção para dados em trânsito, devendo ser usada apenas quando for exigido o máximo de taxa de transferência com o mínimo de latência mínima. Os clientes podem optar por usar isso nos cenários de comunicação entre VNets ou quando a criptografia está sendo aplicada em outro lugar na solução.
* Para a conectividade entre locais através da Internet, use as configurações padrão de gateway de VPN do Azure com criptografia e algoritmos de hash listados nas tabelas acima para garantir a segurança da comunicação crítica.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>Problemas conhecidos de compatibilidade de dispositivo

> [!IMPORTANT]
> Estes são os problemas conhecidos de compatibilidade entre dispositivos VPN de terceiros e gateways de VPN do Azure. A equipe do Azure está trabalhando ativamente com os fornecedores para resolver os problemas listados aqui. Depois que os problemas forem resolvidos, esta página será atualizada com as informações mais recentes. Verifique periodicamente.
>
>

### <a name="feb-16-2017"></a>16 de fevereiro de 2017

**Dispositivos Palo Alto Networks com versão anterior à 7.1.4** para VPN do Azure baseada em rota: se você estiver usando dispositivos VPN da Palo Alto Networks com versão do PAN-OS anterior à 7.1.4 e estiver com problemas de conectividade com os gateways de VPN do Azure baseados em rota, execute as seguintes etapas:

1. Verifique a versão do firmware do dispositivo Palo Alto Networks. Se a versão do PAN-OS for anterior à 7.1.4, atualize para a 7.1.4.
2. No dispositivo Palo Alto Networks, altere o tempo de vida da SA da Fase 2 (ou SA de Modo Rápido) para 28.800 segundos (8 horas) ao conectar-se com o gateway de VPN do Azure.
3. Se você ainda estiver tendo problemas de conectividade, abra uma solicitação de suporte no portal do Azure.
