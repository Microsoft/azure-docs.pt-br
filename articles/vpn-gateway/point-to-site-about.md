---
title: Sobre as conexões VPN Ponto a Site do Azure | Microsoft Docs
description: Este artigo ajuda você a entender as conexões Ponto a Site e ajuda você a decidir qual tipo de autenticação de gateway de VPN P2S usar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: 11fbf14cdeb8d22dbfdf522e1c5838634937f6cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679353"
---
# <a name="about-point-to-site-vpn"></a>Sobre VPN Ponto a Site

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Essa solução é útil para pessoas que trabalham remotamente que querem se conectar às VNets do Azure de um local remoto, como de casa ou de uma conferência. A VPN P2S também é uma solução útil para usar em vez de uma VPN S2S, quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual. Este artigo se aplica ao modelo de implantação do Gerenciador de Recursos.

## <a name="protocol"></a>Qual protocolo o P2S usa?

VPN Ponto a Site pode usar um dos seguintes protocolos:

* **Protocolo de® OpenVPN**, um SSL/TLS com base em protocolo de VPN. Uma solução de SSL VPN pode invadir firewalls, desde que a maioria dos firewalls abra a porta TCP 443, usada pelo SSL. OpenVPN pode ser usado para conectar-se do Android, iOS (versões 11.0 e posteriores), dispositivos Windows, Linux e Mac (OSX versões 10.13 e acima).

* SSTP (Secure Socket Tunneling Protocol), que é um protocolo VPN baseado em SSL proprietário. Uma solução de SSL VPN pode invadir firewalls, desde que a maioria dos firewalls abra a porta TCP 443, usada pelo SSL. SSTP só tem suporte em dispositivos com Windows. O Azure oferece suporte a todas as versões do Windows com SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em padrões. VPN IKEv2 pode ser usada para se conectar de dispositivos Mac (OSX versões 10.11 e acima).


>[!NOTE]
>O IKEv2 e o OpenVPN para P2S estão disponíveis somente para o modelo de implantação do Resource Manager. Eles não estão disponíveis para o modelo de implantação clássico.
>

## <a name="authentication"></a>Como os clientes VPN P2S são autenticados?

Antes de o Azure aceitar uma conexão VPN P2S, o usuário deve autenticar primeiro. Há dois mecanismos oferecidos pelo Azure para autenticar um usuário em conexão.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticar usando a autenticação de certificado nativa do Azure

Ao usar a autenticação de certificado nativa do Azure, um certificado de cliente que está presente no dispositivo é usado para autenticar o usuário conectado. Certificados de cliente são gerados a partir de um certificado raiz confiável e instalado em cada computador cliente. Você pode usar um certificado raiz que foi gerado usando uma solução corporativa ou você pode gerar um certificado autoassinado.

A validação do certificado do cliente é realizada pelo gateway de VPN e acontece durante o estabelecimento da conexão de VPN P2S. O certificado raiz é necessário para a validação e deve ser carregado no Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticar usando o servidor de domínio do Active Directory (AD)

A autenticação de Domínio do AD permite que os usuários se conectem ao Azure usando suas credenciais de domínio da organização. Ela requer um servidor RADIUS que integra-se com o servidor do AD. As empresas também podem aproveitar sua implantação existente do RADIUS.   
  
O servidor RADIUS pode ser implantado localmente ou na VNET do Azure. Durante a autenticação, o Gateway de VPN do Azure atua como uma passagem e encaminha as mensagens de autenticação entre o servidor RADIUS e o dispositivo de conexão. Portanto, a capacidade de acesso ao servidor RADIUS pelo Gateway é importante. Se o servidor RADIUS for local, será necessária uma conexão VPN S2S do Azure para o site local para permitir o acesso.  
  
O servidor RADIUS também pode ser integrado aos Serviços de Certificados do AD. Isso permite que você use o servidor RADIUS e sua implantação de certificado corporativo para autenticação de certificado P2S como uma alternativa para a autenticação de certificado do Azure. A vantagem é que você não precisa carregar certificados raiz e certificados revogados no Azure.

Um servidor RADIUS também pode integrar-se com outros sistemas de identidade externa. Isso possibilita várias opções de autenticação para VPNs P2S, incluindo opções de multifator.

>[!NOTE]
>**Protocolo de® OpenVPN** não é compatível com a autenticação RADIUS.
>

![ponto a site](./media/point-to-site-about/p2s.png "Ponto a Site")

## <a name="what-are-the-client-configuration-requirements"></a>Quais são os requisitos de configuração do cliente?

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

## <a name="gwsku"></a>Quais SKUs de gateway dão suporte à VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Para obter recomendações de SKU de Gateway, confira [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>A SKU Básica não dá suporte à autenticação IKEv2 ou RADIUS.
>

## <a name="configure"></a>Como fazer para configurar uma conexão P2S?

Uma configuração P2S exige algumas etapas específicas. Os seguintes artigos contêm etapas para orientá-lo pela configuração de P2S e links para configurar os dispositivos cliente VPN:

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurar o OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="faqcert"></a>Perguntas frequentes sobre a autenticação nativa de certificado do Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Perguntas frequentes sobre a autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca registrada da Inc OpenVPN.**