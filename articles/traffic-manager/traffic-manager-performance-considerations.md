---
title: Considerações sobre o desempenho para o Gerenciador de Tráfego do Azure | Microsoft Docs
description: Compreenda o desempenho no Gerenciador de Tráfego e como testar o desempenho de seu site ao usar o Gerenciador de Tráfego
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186168"
---
# <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho sobre Gerenciador de Tráfego

Esta página explica as considerações de desempenho usando o Gerenciador de Tráfego. Considere o seguinte cenário:

Você tem instâncias do seu site nas regiões WestUS e EastAsia. Uma das instâncias está falhando na verificação de integridade para a investigação do gerenciador de tráfego. O tráfego de aplicativo é direcionado para a região íntegra. Esse failover é esperado, mas o desempenho pode ser um problema com base na latência do tráfego que agora viaja para uma região distante.

## <a name="performance-considerations-for-traffic-manager"></a>Considerações de desempenho sobre Gerenciador de Tráfego

O único impacto sobre o desempenho que o Gerenciador de Tráfego pode no seu site é na pesquisa de DNS inicial. Uma solicitação DNS para o nome do seu perfil do Gerenciador de Tráfego é tratada pelo servidor raiz DNS da Microsoft que hospeda a zona trafficmanager.net. O Gerenciador de Tráfego preenche e atualiza regularmente os servidores raiz DNS da Microsoft com base na política do Gerenciador de Tráfego e nos resultados da investigação. Portanto, mesmo durante a pesquisa de DNS inicial, nenhuma consulta DNS é enviada ao Gerenciador de Tráfego.

O Gerenciador de Tráfego é composto por vários componentes: servidores de nome DNS, um serviço de API, a camada de armazenamento e um serviço de monitoramento de ponto de extremidade. Se um componente de serviço do Gerenciador de Tráfego falhar, não haverá nenhum efeito sobre o nome DNS associado ao perfil do Gerenciador de Tráfego. Os registros nos servidores DNS da Microsoft permanecem inalterados. No entanto, o monitoramento de ponto de extremidade e a atualização de DNS não acontecem. Portanto, o Gerenciador de Tráfego não consegue atualizar o DNS para apontar para seu site de failover quando seu site primário fica inativo.

A resolução de nome DNS é rápida e os resultados são armazenados em cache. A velocidade da pesquisa DNS inicial depende dos servidores DNS que o cliente usa para resolução de nomes. Normalmente, um cliente pode concluir uma pesquisa de DNS em cerca de 50 ms. Os resultados da pesquisa são armazenados em cache durante a TTL (vida útil) do DNS. A TTL padrão para o Gerenciador de Tráfego é de 300 segundos.

O tráfego NÃO flui pelo Gerenciador de Tráfego. Uma vez concluída a pesquisa DNS, o cliente tem um endereço IP para uma instância do seu site. O cliente conecta-se diretamente ao endereço e não passa pelo Gerenciador de Tráfego. A política do Gerenciador de Tráfego que você escolhe não tem nenhuma influência sobre o desempenho do DNS. No entanto, um método de roteamento de Desempenho pode afetar negativamente a experiência do aplicativo. Por exemplo, se sua política redirecionar tráfego da América do Norte para uma instância hospedada na Ásia, a latência de rede para essas sessões poderá causar um problema de desempenho.

## <a name="measuring-traffic-manager-performance"></a>Medindo o desempenho do Gerenciador de Tráfego

Há vários sites que você pode usar para entender o desempenho e o comportamento de um perfil do Gerenciador de Tráfego. Muitos desses sites são gratuitos, mas podem ter limitações. Alguns sites oferecem monitoramento e relatórios aprimorados por uma taxa.

As ferramentas nesses sites medem as latências de DNS e exibem os endereços IP resolvidos para locais de cliente em todo o mundo. A maioria dessas ferramentas não armazena em cache os resultados DNS. Portanto, as ferramentas mostram a pesquisa de DNS completa sempre que um teste é executado. Quando você testa de seu próprio cliente, experimenta o desempenho de pesquisa DNS completo apenas uma vez durante a duração da TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Exemplo de ferramentas para medir o desempenho de DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS oferece várias ferramentas de desempenho. A ferramenta de Comparação de DNS pode mostrar quanto tempo leva para resolver o nome DNS e como isso se compara a outros provedores de serviço DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Uma das ferramentas mais simples é o WebSitePulse. Insira a URL para ver o tempo de resolução DNS, o Primeiro Byte, o Último Byte e outras estatísticas de desempenho. Você pode escolher entre três locais de teste diferentes. Neste exemplo, você verá que a primeira execução mostra que a pesquisa de DNS leva 0,204 s.

    ![Captura de tela que mostra a ferramenta "WebSitePulse" com o resultado de pesquisa "DNS" realçado.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Uma vez que os resultados estão armazenados em cache, no segundo teste para o mesmo ponto de extremidade do Gerenciador de Tráfego, a pesquisa de DNS leva 0,002 segundo.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor Sintético de Aplicativo de AC](https://asm.ca.com/en/checkit.php)

    Anteriormente conhecido como a ferramenta de site Watch-mouse check, este site mostra o tempo de resolução DNS de várias regiões geográficas simultaneamente. Insira a URL para ver o tempo de resolução de DNS, o tempo de conexão e a velocidade de vários locais geográficos. Use esse teste para ver qual serviço hospedado é retornado para diferentes locais no mundo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Essa ferramenta fornece estatísticas de desempenho para cada elemento de uma página da Web. A guia Análise da Página mostra o percentual de tempo gasto na pesquisa de DNS.

* [What's My DNS?](https://www.whatsmydns.net/)

    Este site realiza uma pesquisa de DNS de 20 locais diferentes e exibe os resultados em um mapa.

* [Interface Dig Web](https://www.digwebinterface.com)

    Esse site mostra informações de DNS mais detalhadas, incluindo CNAMEs e registros A. Marque “Colorir Saída” e “Estatísticas” em opções e selecione “Todos em Nameservers.

## <a name="next-steps"></a>Próximas etapas

[Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[Teste as configurações do Gerenciador de Tráfego](traffic-manager-testing-settings.md)

[Operações no Gerenciador de Tráfego (referência de API REST)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Cmdlets do Gerenciador de Tráfego do Azure](/powershell/module/az.trafficmanager)