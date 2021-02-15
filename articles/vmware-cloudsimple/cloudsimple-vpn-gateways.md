---
title: Solução do Azure VMware por gateways de CloudSimple-VPN
description: Saiba mais sobre gateways de VPN site a site e ponto a site do CloudSimple, que são usados para enviar tráfego criptografado entre uma região CloudSimple e outros recursos.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140625"
---
# <a name="vpn-gateways-overview"></a>Visão geral de gateways de VPN

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede de região CloudSimple em um local ou um computador pela Internet pública.  Cada região pode ter um gateway de VPN, que pode dar suporte a várias conexões. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

O CloudSimple fornece dois tipos de gateways de VPN:

* Gateway de VPN site a site
* Gateway de VPN ponto a site

## <a name="site-to-site-vpn-gateway"></a>Gateway de VPN site a site

Um gateway de VPN site a site é usado para enviar tráfego criptografado entre uma rede de região CloudSimple e um datacenter local. Use essa conexão para definir o intervalo de sub-redes/CIDR, para o tráfego de rede entre a rede local e a rede de região CloudSimple.

O gateway de VPN permite que você consuma serviços do local em sua nuvem privada e serviços em sua nuvem privada a partir da rede local.  O CloudSimple fornece um servidor VPN baseado em políticas para estabelecer a conexão de sua rede local.

Casos de uso para VPN site a site:

* Acessibilidade de sua nuvem privada vCenter de qualquer estação de trabalho em sua rede local.
* Uso de seu Active Directory local como uma fonte de identidade do vCenter.
* Transferência conveniente de modelos de VM, ISOs e outros arquivos de seus recursos locais para seu vCenter de nuvem privada.
* Acessibilidade de cargas de trabalho em execução em sua nuvem privada da sua rede local.

![Topologia de conexão VPN site a site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parâmetros criptográficos

Uma conexão VPN site a site usa os seguintes parâmetros criptográficos padrão para estabelecer uma conexão segura.  Ao criar uma conexão do dispositivo VPN local, use qualquer um dos parâmetros a seguir com suporte no gateway de VPN local.

#### <a name="phase-1-proposals"></a>Propostas da fase 1

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versão IKE | IKEv1 | IKEv1 | IKEv1 |
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (grupo DH) | 2 | 2 | 2 |
| Tempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propostas da fase 2

| Parâmetro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de sigilo contínuo (grupo PFS) | Nenhum | Nenhum | Nenhum |
| Tempo de vida | 1.800 segundos | 1.800 segundos | 1.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Defina TCP MSS fixação MSS às 1200 em seu dispositivo VPN. Ou, se os dispositivos VPN não oferecerem suporte a MSS fixação MSS, você poderá definir, como alternativa, o MTU na interface de túnel como 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Gateway de VPN ponto a site

Uma VPN ponto a site é usada para enviar tráfego criptografado entre uma rede de região CloudSimple e um computador cliente.  A VPN ponto a site é a maneira mais fácil de acessar sua rede de nuvem privada, incluindo suas VMs de nuvem privada e de carga de trabalho.  Use a conectividade VPN ponto a site se você estiver se conectando à nuvem privada remotamente.

## <a name="next-steps"></a>Próximas etapas

* [Configurar gateway de VPN](vpn-gateway.md)
