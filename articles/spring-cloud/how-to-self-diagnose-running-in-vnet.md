---
title: Como diagnosticar automaticamente a VNET do Azure Spring Cloud
description: Saiba como diagnosticar automaticamente e resolver problemas no Azure Spring Cloud em execução na VNET.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877752"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Autodiagnóstico executando a nuvem Spring do Azure na VNET
O Azure Spring Cloud Diagnostics dá suporte a aplicativos de solução de problemas interativos em execução em redes virtuais sem configuração. O Azure Spring Cloud Diagnostics identifica problemas e orienta você às informações que ajudam a solucioná-los e solucioná-los.

## <a name="navigate-to-the-diagnostics-page"></a>Navegue até a página de diagnóstico
O procedimento a seguir inicia o diagnóstico para aplicativos em rede.
1. Entre no portal do Azure.
1. Vá até a página Visão geral do Azure Spring Cloud.
1. Selecione **diagnosticar e resolver problemas** no menu no painel de navegação esquerdo.
1. Selecione a terceira categoria, **rede**.

   ![Título de autodiagnóstico](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico
Depois de clicar na categoria **rede** , você poderá exibir dois problemas relacionados à rede específica para sua VNet injetada Azure Spring Cloud: **resolução DNS** e **tráfego de saída necessário**.

   ![Opções de autodiagnóstico](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Selecione o problema de destino para exibir o relatório de diagnóstico. Um resumo do diagnóstico será exibido, como: 

* *O recurso foi removido.*
* *O recurso não é implantado em sua própria rede virtual*.

Alguns resultados contêm documentação relacionada. Sub-redes diferentes exibirão os resultados separadamente.

## <a name="dns-resolution"></a>Resolução DNS 
Se você selecionar **resolução DNS**, os resultados indicarão se há problemas de DNS com os aplicativos.  Os aplicativos íntegros são listados da seguinte maneira:

* *Problemas de DNS resolvidos sem problemas na sub-rede ' subnet01 '*.
* *Problemas de DNS resolvidos sem problemas na sub-rede ' subnet02 '*.

O exemplo de relatório de diagnóstico a seguir indica que a integridade do aplicativo é desconhecida. O intervalo de tempo de relatório não inclui a hora em que o status de integridade foi relatado.  Suponha que a hora de término do contexto seja *2021-03-03T04:20:00Z*. O carimbo de data/hora mais recente nos **processamentos da tabela de resolução DNS** é *2021-03-03T03:39:00Z*, o dia anterior. O log de verificação de integridade pode não ter sido enviado por causa de uma rede bloqueada. 

Os resultados de status de integridade desconhecidos contêm documentação relacionada.  Você pode clicar no colchete angular esquerdo para ver a exibição suspensa.

   ![DNS desconhecido](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Se você tiver configurado incorretamente seu conjunto de registros de zona DNS privado, obterá um resultado crítico, como: `Failed to resolve the Private DNS in subnet xxx` . 

Nas **renderizações da tabela de resolução DNS** suspensa, você encontrará as informações da mensagem detalhada da qual você pode verificar sua configuração.

## <a name="required-outbound-traffic"></a>Tráfego de saída necessário 

Se você selecionar o **tráfego de saída necessário**, os resultados indicarão se há problemas de tráfego de saída com os aplicativos.  Os aplicativos íntegros são listados da seguinte maneira:

* * O tráfego de saída necessário foi resolvido sem problemas na sub-rede ' subnet01 '.
* * O tráfego de saída necessário foi resolvido sem problemas na sub-rede ' subnet02 '.

Se qualquer sub-rede estiver bloqueada por regras de firewall ou NSG, e se você não tiver bloqueado o log, encontrará as seguintes falhas. Você pode verificar se você desconsiderar as [responsabilidades do cliente](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Falha no ponto de extremidade](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Se não houver dados no prazo de `Required Outbound Traffic Table Renderings` 30 minutos, o resultado será `health status unknown` . Talvez sua rede esteja bloqueada ou o serviço de log esteja inoperante.

   ![Ponto de extremidade de diagnóstico desconhecido](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Confira também
* [Como diagnosticar automaticamente o Azure Spring Cloud](spring-cloud-howto-self-diagnose-solve.md)