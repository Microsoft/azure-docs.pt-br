---
title: Requisitos de QoS para ExpressRoute | Microsoft Docs
description: "Esta página fornece requisitos detalhados para a configuração e gerenciamento de QoS para circuitos do ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: db1c1447-0283-4a09-907b-ae481adc40c7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: cherylmc
ms.openlocfilehash: c097a9ccba91f59b323215d42d37e6d85e0981ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-qos-requirements"></a>Requisitos de QoS para o ExpressRoute
Skype for Business tem várias cargas de trabalho que exigem tratamento de QoS diferenciado. Se você planeja utilizar serviços de voz por meio de ExpressRoute, siga os requisitos descritos abaixo.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Os requisitos de QoS aplicam-se somente ao emparelhamento Microsoft. Os valores de DSCP no tráfego de rede recebido no emparelhamento público e no emparelhamento privado do Azure serão redefinidos como 0. 
> 
> 

A tabela a seguir fornece uma lista de marcações DSCP usadas pelo Skype for Business. Para obter mais informações, consulte [Gerenciamento de QoS para Skype for Business](https://technet.microsoft.com/library/gg405409.aspx) .

| **Classe de Tráfego** | **Tratamento (Marcação DSCP)** | **Cargas de Trabalho do Skype for Business** |
| --- | --- | --- |
| **Voz** |EF (46) |Voz do Skype / Lync |
| **Interativo** |AF41 (34) |Vídeo, VBSS |
| AF21 (18) |Compartilhamento de aplicativo | |
| **Padrão** |AF11 (10) |Transferência de arquivo |
| CS0 (0) |Qualquer outra coisa | |

* Você deve classificar as cargas de trabalho e marcar os valores DSCP corretos. Siga as orientações fornecidas [aqui](https://technet.microsoft.com/library/gg405409.aspx) sobre como definir as marcações de DSCP em sua rede.
* Você deve configurar e dar suporte a várias filas de QoS em sua rede. Voz deve ser uma classe autônoma e receber o tratamento de EF especificado no RFC 3246. 
* Você pode decidir o mecanismo de enfileiramento, a política de detecção de congestionamento e a alocação de largura de banda por classe de tráfego. Porém, a marcação DSCP para cargas de trabalho do Skype for Business deve ser preservada. Se você estiver usando marcas DSCP não listadas acima, por exemplo, AF31 (26), você deve reescrever esse valor DSCP para 0 antes de enviar o pacote para a Microsoft. A Microsoft envia somente pacotes marcados com o valor DSCP mostrado na tabela acima. 

## <a name="next-steps"></a>Próximas etapas
* Consulte os requisitos para [Roteamento](expressroute-routing.md) e [NAT](expressroute-nat.md).
* Consulte os links a seguir para configurar a conexão do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configurar o roteamento](expressroute-howto-routing-classic.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md)

