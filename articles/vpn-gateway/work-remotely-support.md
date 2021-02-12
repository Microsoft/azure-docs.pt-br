---
title: 'Trabalho remoto com P2S: gateway de VPN do Azure'
description: Esta página descreve como você pode aproveitar o gateway de VPN para habilitar o trabalho remotamente devido ao pandemia COVID-19.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880077"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Trabalho remoto usando o ponto a site do gateway de VPN do Azure

>[!NOTE]
>Este artigo descreve como você pode aproveitar o gateway de VPN do Azure, o Azure, a rede da Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você está enfrentando devido à crise COVID-19.
>

Este artigo descreve as opções disponíveis para que as organizações configurem o acesso remoto para seus usuários ou complementem suas soluções existentes com capacidade adicional durante o COVID-19 epidemia.

A solução de ponto a site do Azure é baseada em nuvem e pode ser provisionada rapidamente para atender à maior demanda de usuários para trabalharem de casa. Ele pode escalar verticalmente com facilidade e desligamento com a mesma facilidade e rapidez quando a capacidade aumentada não é mais necessária.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Sobre VPN Ponto a Site

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Essa solução é útil para telecomutadores que desejam se conectar a VNets do Azure ou data centers locais de um local remoto, como de casa ou de uma conferência. Este artigo descreve como permitir que os usuários trabalhem remotamente com base em vários cenários.

A tabela a seguir mostra os sistemas operacionais do cliente e as opções de autenticação que estão disponíveis para eles. Seria útil selecionar o método de autenticação com base no sistema operacional do cliente que já está em uso. Por exemplo, selecione OpenVPN com autenticação baseada em certificado se você tiver uma mistura de sistemas operacionais cliente que precisam se conectar. Além disso, observe que a VPN ponto a site só tem suporte em gateways de VPN baseados em rota.

![Captura de tela que mostra os sistemas operacionais do cliente e as opções de autenticação disponíveis.](./media/working-remotely-support/ostable.png "Sistema operacional")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Cenário 1-os usuários precisam acessar recursos somente no Azure

Nesse cenário, os usuários remotos só precisam acessar os recursos que estão no Azure.

![Diagrama que mostra um cenário ponto a site para usuários que precisam acessar recursos somente no Azure.](./media/working-remotely-support/scenario1.png "Cenário 1")

Em um alto nível, as etapas a seguir são necessárias para permitir que os usuários se conectem aos recursos do Azure com segurança:

1. Criar um gateway de rede virtual (se não houver um).
2. Configure a VPN ponto a site no gateway.

   * Para autenticação de certificado, siga [este link](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Para OpenVPN, siga [este link](vpn-gateway-howto-openvpn.md).
   * Para a autenticação do Azure AD, siga [este link](openvpn-azure-ad-tenant.md).
   * Para solucionar problemas de conexões ponto a site, siga [este link](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Baixe e distribua a configuração do cliente VPN.
4. Distribua os certificados (se a autenticação de certificado estiver selecionada) para os clientes.
5. Conecte-se à VPN do Azure.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Cenário 2-os usuários precisam acessar recursos no Azure e/ou recursos locais

Nesse cenário, os usuários remotos precisam acessar os recursos que estão no Azure e no data center local (s).

![Diagrama que mostra um cenário ponto a site para usuários que precisam de acesso a recursos no Azure.](./media/working-remotely-support/scenario2.png "Cenário 2")

Em um alto nível, as etapas a seguir são necessárias para permitir que os usuários se conectem aos recursos do Azure com segurança:

1. Criar um gateway de rede virtual (se não houver um).
2. Configure a VPN ponto a site no gateway (consulte o [cenário 1](#scenario1)).
3. Configure um túnel site a site no gateway de rede virtual do Azure com BGP habilitado.
4. Configure o dispositivo local para se conectar ao gateway de rede virtual do Azure.
5. Baixar o perfil de ponto a site do portal do Azure e distribuir para clientes

Para saber como configurar um túnel VPN site a site, consulte [este link](./tutorial-site-to-site-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Perguntas frequentes sobre a autenticação nativa de certificado do Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Perguntas frequentes sobre a autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma conexão P2S-autenticação do Azure AD](openvpn-azure-ad-tenant.md)

* [Configurar uma conexão P2S – autenticação RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurar uma conexão P2S – autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**