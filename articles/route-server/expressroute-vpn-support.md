---
title: Sobre o servidor de rota do Azure (versão prévia) dá suporte para ExpressRoute e VPN do Azure
description: Saiba mais sobre como o servidor de rota do Azure interage com gateways de VPN e do ExpressRoute.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679082"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Sobre o suporte do servidor de rota do Azure (versão prévia) para ExpressRoute e VPN do Azure

O servidor de rota do Azure dá suporte a não apenas soluções de virtualização de rede (NVA) de terceiros em execução no Azure, mas também se integra perfeitamente com gateways de VPN e do ExpressRoute. Você não precisa configurar ou gerenciar o emparelhamento via protocolo BGP entre o gateway e o servidor de rota do Azure. Você pode habilitar a troca de rotas entre o gateway e o servidor de rota do Azure com uma [alteração de configuração](quickstart-configure-route-server-powershell.md#route-exchange)simples.

> [!IMPORTANT]
> O servidor de rota do Azure (visualização) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Como ele funciona?

Quando você implanta um servidor de rota do Azure junto com um gateway de ExpressRoute e um NVA em uma rede virtual por padrão, o servidor de rota do Azure não propaga as rotas recebidas do gateway NVA e do ExpressRoute entre si. Depois de habilitar a troca de rota, o ExpressRoute e o NVA irão aprender as rotas de cada uma.

Por exemplo, no diagrama a seguir:

* O dispositivo SDWAN receberá do servidor de rota do Azure a rota do "local 2", que está conectado ao ExpressRoute, junto com a rota de rede virtual.

* O gateway de ExpressRoute receberá a rota do "local 1", que está conectado ao dispositivo SDWAN, junto com a rota de rede virtual do servidor de rota do Azure.

    ![Diagrama mostrando o ExpressRoute configurado com o servidor de rota.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Você também pode substituir o dispositivo SDWAN pelo gateway de VPN do Azure. Como o gateway de VPN do Azure e o ExpressRoute são totalmente gerenciados, você só precisa habilitar a troca de rotas para que as duas redes locais se comuniquem umas com as outras.

> [!IMPORTANT] 
> O gateway de VPN do Azure deve ser configurado no modo [**ativo-ativo**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) .
>

![Diagrama mostrando o ExpressRoute e o gateway de VPN configurados com o servidor de rota.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [servidor de rota do Azure](route-server-faq.md).
- Saiba como [Configurar o servidor de rota do Azure](quickstart-configure-route-server-powershell.md).
- Saiba mais sobre o [Azure ExpressRoute e a coexistência de VPN do Azure](../expressroute/expressroute-howto-coexist-resource-manager.md).
