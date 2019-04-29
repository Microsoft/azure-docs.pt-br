---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/02/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 857d29f407c9939143fbb8263be40dadb040efdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456403"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>A autenticação RADIUS tem suporte em todos os SKUs de Gateway de VPN do Azure?

A autenticação RADIUS tem suporte nos SKUs VpnGw1, VpnGw2 e VpnGw3. Se você estiver usando SKUs herdados, a autenticação RADIUS terá suporte nos SKUs Standard e de Alto Desempenho. Ela não tem suporte no SKU de Gateway Básico. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>Há suporte para autenticação RADIUS para o modelo de implantação clássico?
 
Não. Não há suporte para autenticação RADIUS para o modelo de implantação clássico.
 
### <a name="are-3rd-party-radius-servers-supported"></a>Há suporte para os servidores RADIUS de terceiros?

Sim, há suporte para os servidores RADIUS de terceiros.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Quais são os requisitos de conectividade para garantir que o gateway do Azure seja capaz de acessar um servidor RADIUS local?

Uma conexão VPN Site a Site com o site local, com as rotas apropriadas configuradas.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>O tráfego para um servidor RADIUS local (do gateway de VPN do Azure) pode ser roteado por uma conexão de ExpressRoute?

Não. Ele só pode ser roteado através de uma conexão Site a Site.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>Há uma alteração no número de conexões SSTP com suporte na autenticação RADIUS? Qual é o número máximo de conexões SSTP e IKEv2 com suporte?

Não há alteração no número máximo de conexões SSTP com suporte em um gateway com autenticação RADIUS. Ele permanece 128 para SSTP, mas depende do SKU do gateway para IKEv2. Para obter mais informações sobre o número de conexões compatíveis, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trustedcertificate-to-azure"></a>Qual é a diferença entre realizar a autenticação de certificado usando um servidor RADIUS e usando a autenticação de certificado nativa do Azure (carregando um certificado confiável no Azure).

Na autenticação de certificado RADIUS, a solicitação de autenticação é encaminhada a um servidor RADIUS que manipula a própria validação de certificado. Essa opção será útil se você quiser integrar-se a uma infraestrutura de autenticação de certificado já existente por meio do RADIUS.
  
Ao usar o Azure para autenticação de certificado, o gateway de VPN do Azure executa a validação do certificado. Você precisa carregar a chave pública do certificado no gateway. Você também pode especificar a lista de certificados revogados que não podem se conectar.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>A autenticação RADIUS funciona com o IKEv2 e o SSTP VPN?

Sim, há suporte para a autenticação RADIUS tanto para IKEv2 quanto para SSTP VPN. 
