---
title: "Controlando o tráfego de aplicativos Web do Azure com o Gerenciador de Tráfego do Azure"
description: "Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Azure, já que ele está relacionado a aplicativos Web do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: fb7d391e3118a9dccde5501c3f30c6f580932a30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlando o tráfego de aplicativos Web do Azure com o Gerenciador de Tráfego do Azure
> [!NOTE]
> Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Microsoft Azure, já que está relacionado a aplicativos Web do Serviço de Aplicativo do Azure. Mais informações sobre o próprio Gerenciador de Tráfego do Azure podem ser encontradas visitando-se os links ao final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Você pode usar o Gerenciador de Tráfego do Azure para controlar como as solicitações de clientes Web são distribuídas aos aplicativos Web no Serviço de Aplicativo do Azure. Quando os pontos de extremidade do aplicativo Web são adicionados a um perfil do Gerenciador de Tráfego do Azure, este acompanha o status de seus aplicativos Web (em execução, parado ou excluído) de modo que pode decidir quais desses pontos de extremidade devem receber tráfego.

## <a name="load-balancing-methods"></a>Métodos de balanceamento de carga
O Gerenciador de Tráfego do Azure usa três métodos de balanceamento de carga diferentes. Eles estão descritos na lista a seguir, já que pertencem a aplicativos Web do Azure.

* **Failover**: se tiver clones de aplicativos Web em regiões diferentes, você poderá usar esse método para configurar um aplicativo Web para suprir todo o tráfego do cliente Web e configurar outro aplicativo Web em uma região diferente, para suprir esse tráfego caso o primeiro site fique indisponível.
* **Round Robin**: se tiver clones de aplicativo Web em regiões diferentes, você poderá usar esse método para distribuir tráfego por igual entre os aplicativos Web em regiões diferentes.
* **Desempenho**: o método de desempenho distribui o tráfego com base no menor tempo de ida e volta até os clientes. O método de desempenho pode ser usado em aplicativos Web dentro da mesma região ou em regiões diferentes.

## <a name="web-apps-and-traffic-manager-profiles"></a>Aplicativos Web e perfis do Gerenciador de Tráfego
Para configurar o controle do tráfego de aplicativos Web, crie um perfil no Gerenciador de Tráfego do Azure que use um dos três métodos de balanceamento de carga descritos anteriormente e, em seguida, adicione os pontos de extremidade (neste caso, aplicativos Web) para os quais você deseja controlar o tráfego até o perfil. O status do aplicativo Web (em execução, parado ou excluído) é comunicado regularmente ao perfil, para que o Gerenciador de Tráfego do Azure possa direcionar o tráfego de acordo.

Ao usar o Gerenciador de Tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações apenas de aplicativos Web na mesma região, Aplicativos Web já oferece funcionalidade de failover e rodízio independentemente do modo do aplicativo Web.
* Para implantações na mesma região usando Aplicativos Web juntamente com outro serviço de nuvem do Azure, você pode combinar ambos os tipos de pontos de extremidade para habilitar cenários híbridos.
* Você só pode especificar um ponto de extremidade de aplicativo Web por região em um perfil. Quando você seleciona um aplicativo Web como um ponto de extremidade para uma região, os aplicativos Web restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos de extremidade de aplicativo Web que você especificar em um perfil do Gerenciador de Tráfego do Azure serão exibidos na seção **Nomes de Domínio** da página Configurar do aplicativo Web no perfil, mas não serão configuráveis nessa página.
* Depois que você adicionar um aplicativo Web a um perfil, a **URL do Site** no painel da página de portal do aplicativo Web exibirá a URL do domínio personalizado do aplicativo Web, se você tiver definido uma. Do contrário, ele exibirá a URL do perfil do Gerenciador de Tráfego (por exemplo, `contoso.trafficmgr.com`). O nome de domínio direto do aplicativo Web e a URL do Gerenciador de Tráfego estarão visíveis na página Configurar do aplicativo Web na seção **Nomes de Domínio** .
* Os nomes de domínio personalizados funcionarão conforme esperado, mas além de adicioná-los a seus aplicativos Web, você deve configurar o mapa DNS a fim de apontar para a URL do Gerenciador de Tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo Web do Azure, veja [Mapear um nome de domínio personalizado para um aplicativo do Azure](app-service-web-tutorial-custom-domain.md).
* Você só pode adicionar a um perfil do Gerenciador de Tráfego do Azure aplicativos Web que estejam no modo padrão ou premium.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral conceitual e técnica do Gerenciador de Tráfego do Azure, consulte [Visão geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

Para saber mais sobre como usar o Gerenciador de Tráfego com aplicativos Web, veja as postagens de blog [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) (Usando o Gerenciador de Tráfego do Azure com sites do Azure) e [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) (O Gerenciador de Tráfego do Azure agora pode se integrar a sites do Azure).

