---
title: Gateways de VPN na solução do VMware CloudSimple - Azure
description: Saiba mais sobre conceitos VPN ponto a site e de VPN de site a site CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2eae81f357904bd5034d7409ef42b681d1085930
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695229"
---
# <a name="vpn-gateways-overview"></a>Visão geral de gateways VPN

Um gateway de VPN é usado para enviar tráfego criptografado entre uma rede de região CloudSimple em um caminho local ou em um computador pela Internet pública.  Cada região pode ter apenas um gateway VPN. No entanto, você pode criar várias conexões com o mesmo gateway de VPN. Quando você cria várias conexões com o mesmo gateway de VPN, todos os túneis de VPN compartilham a largura de banda de gateway disponível.

CloudSimple fornece dois tipos de gateways de VPN:

* Gateway VPN site a site
* Gateway VPN ponto a site

## <a name="site-to-site-vpn-gateway"></a>Gateway de VPN site a site

Um gateway de VPN site a site é usado para enviar tráfego criptografado entre uma rede de região CloudSimple e um datacenter local. Use essa conexão para definir o intervalo de CIDR/sub-redes para comunicação entre sua rede local e a rede da região CloudSimple.

O gateway de VPN permite que você consuma serviços locais em sua nuvem privada e serviços em sua nuvem privada, da rede local.  CloudSimple fornece um servidor VPN baseado em políticas para estabelecer a conexão de sua rede local.

Casos de uso de VPN site a site incluem:

* Acessibilidade do vCenter sua nuvem privada de qualquer estação de trabalho em sua rede local.
* Uso do Active Directory local como uma fonte de identidade do vCenter.
* Transferência conveniente de modelos VM, ISOs e outros arquivos de recursos do local para seu vCenter de nuvem privada.
* Acessibilidade de cargas de trabalho em execução em sua nuvem privada da sua rede local.

![Topologia de conexão de VPN site a Site](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> Você deve fixar MSS TCP em bytes 1078 ou inferior. Ou se os dispositivos VPN não derem suporte a fixação de MSS, como alternativa, definir a MTU na interface de túnel para 1118 bytes em vez disso. 

### <a name="cryptographic-parameters"></a>Parâmetros de criptografia

Uma conexão de VPN site a site usa os seguintes parâmetros de criptografia padrão para estabelecer uma conexão segura.  Quando você cria uma conexão de dispositivo VPN no local, use qualquer um dos seguintes parâmetros compatíveis com o gateway VPN local.

#### <a name="phase-1-proposals"></a>Fase 1 propostas

| Parâmetro | Proposta 1 | Proposta 2 | Proposta de 3 |
|-----------|------------|------------|------------|
| Versão IKE | IKEv1 | IKEv1 | IKEv1 |
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo do Diffie Hellman (DH grupo) | 2 | 2 | 2 |
| Tempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Propostas de fase 2 

| Parâmetro | Proposta 1 | Proposta 2 | Proposta de 3 |
|-----------|------------|------------|------------|
| Criptografia | AES 128 | AES 256 | AES 256 |
| Algoritmo de hash| SHA 256 | SHA 256 | SHA 1 |
| Perfeito encaminhar Secrecy Group (grupo de PFS) | Nenhum | Nenhum | Nenhum |
| Tempo de vida | 1\.800 segundos | 1\.800 segundos | 1\.800 segundos |
| Tamanho dos dados | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Gateway de VPN ponto a site

Uma VPN ponto a site é usada para enviar tráfego criptografado entre uma rede de região CloudSimple e um computador cliente.  VPN ponto a site é a maneira mais fácil para acessar sua rede de nuvem privada, incluindo sua nuvem privada vCenter e VMs da carga de trabalho.  Use a conectividade VPN ponto a site se você estiver se conectando à nuvem privada remotamente.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o gateway VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)