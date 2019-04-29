---
title: Controlar o tráfego com o Gerenciador de Tráfego – Serviço de Aplicativo do Azure
description: Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Azure, já que ele está relacionado ao Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: ''
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
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831972"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controle do tráfego do Serviço de Aplicativo do Azure com o Gerenciador de Tráfego do Azure
> [!NOTE]
> Este artigo fornece informações resumidas sobre o Gerenciador de Tráfego do Microsoft Azure, já que está relacionado ao Serviço de Aplicativo do Azure. Mais informações sobre o próprio Gerenciador de Tráfego do Azure podem ser encontradas visitando-se os links ao final deste artigo.
> 
> 

## <a name="introduction"></a>Introdução
Você pode usar o Gerenciador de Tráfego do Azure para controlar como as solicitações de clientes Web são distribuídas aos aplicativos no Serviço de Aplicativo do Azure. Quando os pontos de extremidade do Serviço de Aplicativo são adicionados a um perfil do Gerenciador de Tráfego do Azure, o Gerenciador de Tráfego do Azure acompanha o status de seus aplicativos do Serviço de Aplicativo (em execução, parados ou excluídos) para decidir quais desses pontos de extremidade devem receber tráfego.

## <a name="routing-methods"></a>Métodos de roteamento
O Gerenciador de Tráfego do Azure usa quatro métodos diferentes de roteamento. Esses métodos são descritos na lista a seguir, já que pertencem a aplicativos do Serviço de Aplicativo do Azure.

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority):** use um aplicativo primário para todo o tráfego e forneça backups caso o backup primário ou os aplicativos de backup não estejam disponíveis.
* **[Ponderada](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribua tráfego em um conjunto de aplicativos, seja uniformemente ou acordo com pesos, o que você definir.
* **[Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando você tiver aplicativos em diferentes localizações geográficas, use o aplicativo "mais próximo" em termos da menor latência de rede.
* **[Geográfico](../traffic-manager/traffic-manager-routing-methods.md#geographic):** direcionar os usuários para aplicativos específicos com base em qual localização geográfica a consulta DNS se origina. 

Para obter mais informações, consulte [Métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Perfis do Serviço de Aplicativo e do Gerenciador de Tráfego
Para configurar o controle do tráfego de aplicativo do serviço de aplicativo, crie um perfil no Azure Traffic Manager que use um dos quatro métodos de carga balanceamento descritos anteriormente e, em seguida, adicione os pontos de extremidade (neste caso, o serviço de aplicativo) para o qual você deseja controlar o tráfego para o perfil. O status do aplicativo (em execução, parado ou excluído) é comunicado regularmente ao perfil para que o Gerenciador de Tráfego do Azure possa direcioná-lo de acordo.

Ao usar o Gerenciador de Tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações apenas de aplicativos na mesma região, o Serviço de Aplicativo já oferece funcionalidade de failover e rodízio independentemente do modo do aplicativo.
* Para implantações na mesma região usando o Serviço de Aplicativo juntamente com outro serviço de nuvem do Azure, você pode combinar ambos os tipos de pontos de extremidade para habilitar cenários híbridos.
* Você só pode especificar um ponto de extremidade do Serviço de Aplicativo por região em um perfil. Quando você seleciona um aplicativo como um ponto de extremidade para uma região, os aplicativos restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos de extremidade do Serviço de Aplicativo que você especificar no perfil do Gerenciador de Tráfego do Azure serão exibidos na seção **Nomes de Domínio** da página Configurar do aplicativo no perfil, mas não serão configuráveis lá.
* Depois que você adicionar um aplicativo a um perfil, a **URL do Site** no Painel da página do portal do aplicativo exibirá a URL do domínio personalizado do aplicativo se você tiver configurado uma. Caso contrário, exibe a URL do perfil do Gerenciador de Tráfego (por exemplo, `contoso.trafficmanager.net`). O nome de domínio direto do aplicativo e a URL do Gerenciador de Tráfego estarão visíveis na página Configurar do aplicativo na seção **Nomes de Domínio**.
* Os nomes de domínio personalizados funcionam conforme esperado, mas, além de adicioná-los a seus aplicativos, você também deve configurar o mapa DNS para apontar para a URL do Gerenciador de Tráfego. Para saber mais sobre como configurar um domínio personalizado para um aplicativo do Serviço de Aplicativo, confira [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md).
* Você só pode adicionar aplicativos que estejam no modo padrão ou premium para um perfil do Gerenciador de Tráfego do Azure.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral conceitual e técnica do Gerenciador de Tráfego do Azure, consulte [Visão geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

Para saber mais sobre como usar o Gerenciador de Tráfego com o Serviço de Aplicativo, veja as postagens de blog [Uso do Gerenciador de Tráfego do Azure com sites do Azure](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) (O Gerenciador de Tráfego do Azure agora pode se integrar a sites do Azure).

