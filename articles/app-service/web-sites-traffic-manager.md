---
title: Controlar o tráfego com o Gerenciador de tráfego
description: Encontre as práticas recomendadas para configurar o Gerenciador de tráfego do Azure ao integrá-lo com o serviço Azure App.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80437889"
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

* **[Prioridade](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** use um aplicativo primário para todo o tráfego e forneça backups caso o backup primário ou os aplicativos de backup não estejam disponíveis.
* **[Ponderada](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribua tráfego em um conjunto de aplicativos, seja uniformemente ou acordo com pesos, o que você definir.
* **[Desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando você tiver aplicativos em diferentes localizações geográficas, use o aplicativo "mais próximo" em termos da menor latência de rede.
* **[Geográfico](../traffic-manager/traffic-manager-routing-methods.md#geographic):** direcionar os usuários para aplicativos específicos com base em qual localização geográfica a consulta DNS se origina. 

Para obter mais informações, confira [Traffic Manager routing methods](../traffic-manager/traffic-manager-routing-methods.md) (Métodos de roteamento do Gerenciador de Tráfego).

## <a name="app-service-and-traffic-manager-profiles"></a>Perfis do Serviço de Aplicativo e do Gerenciador de Tráfego
Para configurar o controle do tráfego do aplicativo do serviço de aplicativo, você cria um perfil no Gerenciador de tráfego do Azure que usa um dos quatro métodos de balanceamento de carga descritos anteriormente e, em seguida, adiciona os pontos de extremidade (nesse caso, o serviço de aplicativo) para o qual você deseja controlar o tráfego para o perfil. O status do aplicativo (em execução, parado ou excluído) é comunicado regularmente ao perfil para que o Gerenciador de Tráfego do Azure possa direcioná-lo de acordo.

Ao usar o Gerenciador de Tráfego do Azure com o Azure, tenha em mente os seguintes pontos:

* Para implantações apenas de aplicativos na mesma região, o Serviço de Aplicativo já oferece funcionalidade de failover e rodízio independentemente do modo do aplicativo.
* Para implantações na mesma região usando o Serviço de Aplicativo juntamente com outro serviço de nuvem do Azure, você pode combinar ambos os tipos de pontos de extremidade para habilitar cenários híbridos.
* Você só pode especificar um ponto de extremidade do Serviço de Aplicativo por região em um perfil. Quando você seleciona um aplicativo como um ponto de extremidade para uma região, os aplicativos restantes nessa região ficam indisponíveis para seleção para esse perfil.
* Os pontos de extremidade do Serviço de Aplicativo que você especificar no perfil do Gerenciador de Tráfego do Azure serão exibidos na seção **Nomes de Domínio** da página Configurar do aplicativo no perfil, mas não serão configuráveis lá.
* Depois que você adicionar um aplicativo a um perfil, a **URL do Site** no Painel da página do portal do aplicativo exibirá a URL do domínio personalizado do aplicativo se você tiver configurado uma. Caso contrário, exibe a URL do perfil do Gerenciador de Tráfego (por exemplo, `contoso.trafficmanager.net`). O nome de domínio direto do aplicativo e a URL do Gerenciador de Tráfego estarão visíveis na página Configurar do aplicativo na seção **Nomes de Domínio**.
* Os nomes de domínio personalizados funcionam conforme esperado, mas, além de adicioná-los a seus aplicativos, você também deve configurar o mapa DNS para apontar para a URL do Gerenciador de Tráfego. Para obter informações sobre como configurar um domínio personalizado para um aplicativo do serviço de aplicativo, consulte [configurar um nome de domínio personalizado no serviço de Azure app com a integração do Gerenciador de tráfego](configure-domain-traffic-manager.md).
* Você só pode adicionar aplicativos que estejam no modo padrão ou premium para um perfil do Gerenciador de Tráfego do Azure.
* Adicionar um aplicativo a um perfil do Gerenciador de tráfego faz com que o aplicativo seja reiniciado.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral conceitual e técnica do Gerenciador de Tráfego do Azure, consulte [Visão geral do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).


