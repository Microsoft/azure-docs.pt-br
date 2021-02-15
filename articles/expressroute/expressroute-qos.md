---
title: 'Azure ExpressRoute: requisitos de QoS'
description: Esta página fornece requisitos detalhados para a configuração e o gerenciamento de QoS. Skype for Business/serviços de voz são abordados.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 7f14f43fa341df40ecd35340b7311e1acd18004c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204700"
---
# <a name="expressroute-qos-requirements"></a>Requisitos de QoS para o ExpressRoute
Skype for Business tem várias cargas de trabalho que exigem tratamento de QoS diferenciado. Se você planeja utilizar serviços de voz por meio de ExpressRoute, siga os requisitos descritos abaixo.

![Diagrama que mostra os serviços de voz que passam pelo ExpressRoute.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Os requisitos de QoS aplicam-se somente ao emparelhamento Microsoft. Os valores de DSCP no tráfego de rede recebido no emparelhamento público e no emparelhamento privado do Azure serão redefinidos como 0. 
> 
> 

A tabela a seguir fornece uma lista de marcações DSCP usadas pelo Microsoft Teams e pelo Skype for Business. Para obter mais informações, consulte [Gerenciamento de QoS para Skype for Business](/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) .

| **Classe de Tráfego** | **Tratamento (Marcação DSCP)** | **Cargas de trabalho do Microsoft Teams e do Skype for Business** |
| --- | --- | --- |
| **Voz** |EF (46) |Skype/Microsoft Teams/Lync Voice |
| **Interativo** |AF41 (34) |Vídeo, VBSS |
| |AF21 (18) |Compartilhamento de aplicativo | 
| **Default** |AF11 (10) |Transferência de arquivos |
| |CS0 (0) |Qualquer outra coisa |

* Você deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga as orientações fornecidas [aqui](/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) sobre como definir as marcações de DSCP em sua rede.
* Você deve configurar e dar suporte a várias filas de QoS em sua rede. A voz deve ser uma classe autônoma e receber o tratamento do EF especificado no [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Você pode decidir o mecanismo de enfileiramento, a política de detecção de congestionamento e a alocação de largura de banda por classe de tráfego. Porém, a marcação DSCP para cargas de trabalho do Skype for Business deve ser preservada. Se você estiver usando marcas DSCP não listadas acima, por exemplo, AF31 (26), você deve reescrever esse valor DSCP para 0 antes de enviar o pacote para a Microsoft. A Microsoft envia somente pacotes marcados com o valor DSCP mostrado na tabela acima. 

## <a name="next-steps"></a>Próximas etapas
* Consulte os requisitos para [Roteamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
* Consulte os links a seguir para configurar a conexão do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configurar o roteamento](expressroute-howto-routing-classic.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)