---
title: Transição para OpenVPN ou IKEv2 do SSTP | Gateway de VPN do Azure
description: Este artigo ajuda você a entender as maneiras de superar o limite de conexões simultâneas de 128 do SSTP.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2fa265e580bc0e752498284ed50e398b59423fd
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657134"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transição para o protocolo OpenVPN ou IKEv2 do SSTP

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Este artigo se aplica ao modelo de implantação do Gerenciador de recursos e fala sobre maneiras de superar o limite de conexões simultâneas de 128 do SSTP fazendo a transição para o protocolo OpenVPN ou IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Qual protocolo o P2S usa?

VPN Ponto a Site pode usar um dos seguintes protocolos:

* **OpenVPN &reg; Protocolo**, um protocolo VPN baseado em SSL/TLS. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443 de saída, que o SSL usa. O OpenVPN pode ser usado para se conectar do Android, iOS (versões 11,0 e superiores), Windows, Linux e dispositivos Mac (OSX versões 10,13 e posteriores).

* **SSTP**, um protocolo de VPN com base em SSL proprietário. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443 de saída, que o SSL usa. SSTP só tem suporte em dispositivos com Windows. O Azure oferece suporte a todas as versões do Windows com SSTP (Windows 7 e posterior). **O SSTP dá suporte a até 128 conexões simultâneas apenas independentemente da SKU do gateway**.

* VPN IKEv2, uma solução de VPN IPsec baseada em padrões. VPN IKEv2 pode ser usada para se conectar de dispositivos Mac (OSX versões 10.11 e acima).


>[!NOTE]
>O IKEv2 e o OpenVPN para P2S estão disponíveis somente para o modelo de implantação do Resource Manager. Eles não estão disponíveis para o modelo de implantação clássico. O SKU do gateway básico não dá suporte a protocolos IKEv2 ou OpenVPN. Se você estiver usando o SKU básico, precisará excluir e recriar um gateway de rede virtual SKU de produção.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrando do SSTP para IKEv2 ou OpenVPN

Pode haver casos em que você deseja dar suporte a mais de 128 conexão P2S simultânea com um gateway de VPN, mas que estão usando SSTP. Nesse caso, você precisa mover para o protocolo IKEv2 ou OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opção 1-adicionar IKEv2 além do SSTP no gateway

Essa é a opção mais simples. SSTP e IKEv2 podem coexistir no mesmo gateway e fornecer um número maior de conexões simultâneas. Você pode simplesmente habilitar o IKEv2 no gateway existente e baixar o cliente novamente.

Adicionar IKEv2 a um gateway de VPN SSTP existente não afetará os clientes existentes e você poderá configurá-los para usar IKEv2 em pequenos lotes ou apenas configurar os novos clientes para usar IKEv2. Se um cliente do Windows estiver configurado para SSTP e IKEv2, ele tentará se conectar usando IKEV2 primeiro e, se isso falhar, ele retornará ao SSTP.

**O IKEv2 usa portas UDP não padrão para que você precise garantir que essas portas não sejam bloqueadas no firewall do usuário. As portas em uso são UDP 500 e 4500.**

Para adicionar o IKEv2 a um gateway existente, basta ir para a guia "configuração de ponto a site" no gateway de rede virtual no portal e selecionar **IKEv2 e SSTP (SSL)** na caixa suspensa.

![Captura de tela que mostra a página "configuração ponto a site" com a lista suspensa "tipo de túnel" aberta e "IKEv2 e SSTP (SSL)" selecionado.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opção 2-remover SSTP e habilitar OpenVPN no gateway

Como o SSTP e o OpenVPN são do protocolo baseado em TLS, eles não podem coexistir no mesmo gateway. Se você decidir sair do SSTP para o OpenVPN, terá que desabilitar o SSTP e habilitar o OpenVPN no gateway. Esta operação fará com que os clientes existentes percam a conectividade com o gateway de VPN até que o novo perfil tenha sido configurado no cliente.

Você pode habilitar OpenVPN junto com IKEv2, se desejar. O OpenVPN é baseado em TLS e usa a porta TCP 443 padrão. Para alternar para OpenVPN, vá para a guia "configuração de ponto a site" no gateway de rede virtual no portal e selecione **OpenVPN (SSL)** ou **IKEv2 e OpenVPN (SSL)** na caixa suspensa.

![ponto a site](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Depois que o gateway tiver sido configurado, os clientes existentes não poderão se conectar até que você [implante e configure os clientes do OpenVPN](./vpn-gateway-howto-openvpn-clients.md).

Se você estiver usando o Windows 10, também poderá usar o [cliente VPN do Azure para Windows](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

>[!NOTE]
>Para clientes do Windows, é necessário ter direitos de administrador no dispositivo cliente para iniciar a conexão VPN do dispositivo cliente para o Azure.
>

Os usuários usam os clientes VPN nativos em dispositivos Windows e Mac para P2S. O Azure fornece um arquivo compactado de configuração de cliente de VPN que contém as configurações necessárias para esses clientes nativos se conectarem ao Azure.

* Para dispositivos Windows, a configuração de cliente de VPN é composta por um pacote de instalador que os usuários instalam em seus dispositivos.
* Para dispositivos Mac, ela é composta pelo arquivo mobileconfig que os usuários instalem em seus dispositivos.

O arquivo zip também fornece os valores de algumas das configurações importantes no lado do Azure que você pode usar para criar seu próprio perfil para esses dispositivos. Alguns dos valores incluem o endereço de gateway de VPN, os tipos de encapsulamento configurados, rotas e o certificado raiz para validação de gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Quais SKUs de gateway dão suporte à VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para obter recomendações de SKU de Gateway, confira [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>A SKU Básica não dá suporte à autenticação IKEv2 ou RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Quais políticas de IKE/IPsec são configuradas em gateways de VPN para P2S?


**IKEv2**

| **Codificação** | **Integridade** | **PRF** | **Grupo DH** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Codificação** | **Integridade** | **Grupo PFS** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Quais políticas de TLS são configuradas em gateways de VPN para P2S?
**TLS**

|**Políticas** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Como fazer para configurar uma conexão P2S?

Uma configuração P2S exige algumas etapas específicas. Os seguintes artigos contêm etapas para orientá-lo pela configuração de P2S e links para configurar os dispositivos cliente VPN:

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**