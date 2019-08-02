---
title: Perfis do Aninhados do Gerenciador de Tráfego no Azure
titlesuffix: Azure Traffic Manager
description: Este artigo explica o recurso “Perfis Aninhados” do Gerenciador de Tráfego do Azure
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: allensu
ms.openlocfilehash: 8815d852ad9f8a1823e1c21cc2d233409518da33
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333800"
---
# <a name="nested-traffic-manager-profiles"></a>Perfis aninhados do Gerenciador de Tráfego

O Gerenciador de Tráfego inclui uma variedade de métodos de roteamento de tráfego que permitem controlar como o Gerenciador de Tráfego escolhe qual ponto de extremidade deve receber o tráfego de cada usuário final. Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

Cada perfil do Gerenciador de Tráfego especifica um único método de roteamento de tráfego. No entanto, há cenários que exigem um roteamento de tráfego mais sofisticado do que o roteamento fornecido por um único perfil do Gerenciador de Tráfego. Aninhe perfis do Gerenciador de Tráfego para combinar os benefícios de mais de um método de roteamento de tráfego. Os perfis aninhados permitem que você substitua o comportamento padrão do Gerenciador de Tráfego para dar suporte a implantações de aplicativo maiores e mais complexas.

Os exemplos a seguir ilustram como usar os perfis aninhados do Gerenciador de Tráfego em vários cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: combinando roteamento de tráfego de "Desempenho" e "Ponderado"

Suponha que você tenha implantado um aplicativo nas seguintes regiões do Azure: Oeste dos EUA, Europa Ocidental e Ásia Oriental. Você usa o método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego para distribuir o tráfego para a região mais próxima do usuário.

![Perfil único do Gerenciador de Tráfego][4]

Agora, suponha que você deseja testar uma atualização ao serviço antes de distribuí-la mais amplamente. Você deseja usar o método de roteamento de tráfego “ponderado” para direcionar um pequeno percentual do tráfego para a implantação de teste. Você configura a implantação de teste junto com a implantação de produção existente na Europa Ocidental.

Não é possível combinar roteamentos de tráfego “Ponderado” e por “Desempenho” em um único perfil. Para dar suporte a esse cenário, você cria um perfil do Gerenciador de Tráfego usando os dois pontos de extremidade da Europa Ocidental e o método de roteamento de tráfego “Ponderado”. Em seguida, você adiciona esse perfil “filho” como um ponto de extremidade ao perfil “pai”. O perfil pai ainda usa o método de roteamento de tráfego por Desempenho e contém as outras implantações globais como pontos de extremidade.

O diagrama a seguir ilustra esse exemplo:

![Perfis aninhados do Gerenciador de Tráfego][2]

Nessa configuração, o tráfego direcionado por meio do perfil pai distribui o tráfego entre regiões normalmente. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos de extremidade de produção e de teste de acordo com os pesos atribuídos.

Quando o perfil pai usa o método de roteamento de tráfego por “Desempenho”, uma localização deve ser atribuída a cada ponto de extremidade. A localização é atribuída quando você configura o ponto de extremidade. Escolha a região do Azure mais próxima de sua implantação. As regiões do Azure são os valores de localização com suporte na Tabela de Latência da Internet. Para obter mais informações, consulte [Método de roteamento de tráfego por “Desempenho” do Gerenciador de Tráfego](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: monitoramento de ponto de extremidade em Perfis Aninhados

O Gerenciador de Tráfego monitora ativamente a integridade de cada ponto de extremidade de serviço. Se um ponto de extremidade não estiver íntegro, o Gerenciador de Tráfego direcionará os usuários para pontos de extremidade alternativos a fim de preservar a disponibilidade do serviço. Esse comportamento de failover e monitoramento do ponto de extremidade se aplica a todos os métodos de roteamento de tráfego. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md). O monitoramento de ponto de extremidade funciona de forma diferente para perfis aninhados. Com perfis aninhados, o perfil pai não executa verificações de integridade no filho diretamente. Em vez disso, a integridade dos pontos de extremidade do perfil filho é usada para calcular a integridade geral do perfil filho. Essas informações de integridade são propagadas até a hierarquia do perfil aninhado. O perfil pai usa essa integridade agregada para determinar se o tráfego será direcionado ao perfil filho. Consulte as [Perguntas frequentes](traffic-manager-FAQs.md#traffic-manager-nested-profiles) para obter detalhes completos sobre o monitoramento de integridade de perfis aninhados.

Voltando ao exemplo anterior, suponha que a implantação de produção na Europa Ocidental falhe. Por padrão, o perfil “filho” direciona todo o tráfego para a implantação de teste. Se a implantação de teste também falhar, o perfil pai determinará que o perfil filho não deve receber o tráfego, pois nenhum ponto de extremidade filho está íntegro. Em seguida, o perfil pai distribui o tráfego para as outras regiões.

![Failover de perfil aninhado (comportamento padrão)][3]

Talvez você esteja satisfeito com essa organização. Ou você pode estar preocupado porque todo o tráfego para Europa Ocidental agora vai para a implantação de teste, em vez de um tráfego de subconjunto limitado. Independentemente da integridade da implantação de teste, você desejará executar failover para as outras regiões quando a implantação de produção na Europa Ocidental falhar. Para habilitar esse failover, é possível especificar o parâmetro “MinChildEndpoints” ao configurar o perfil filho como um ponto de extremidade no perfil pai. O parâmetro determina o número mínimo de pontos de extremidade disponíveis no perfil filho. O valor padrão é '1'. Para este cenário, você pode definir o valor de MinChildEndpoints como 2. Abaixo desse limite, o perfil pai considera o perfil filho inteiro como indisponível e direciona o tráfego para os outros pontos de extremidade.

A seguinte figura ilustra essa configuração:

![Failover de Perfil Aninhado com “MinChildEndpoints” = 2][4]

> [!NOTE]
> O método de roteamento de tráfego por “Prioridade” distribui todo o tráfego para um único ponto de extremidade. Portanto, não há muita utilidade em uma configuração de MinChildEndpoints diferente de “1” para um perfil filho.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: regiões de failover priorizadas no roteamento de tráfego de "Desempenho"

O comportamento padrão do método de roteamento de tráfego 'Desempenho' é quando há pontos de extremidade em diferentes localizações geográficas em que os usuários finais são roteados para o ponto de extremidade "mais próximo" em termos de menor latência de rede.

No entanto, suponha que você prefere o failover de tráfego da Europa Ocidental para o Oeste dos EUA e direciona o tráfego para outras regiões somente quando os pontos de extremidade não estão disponíveis. Você pode criar essa solução usando um perfil filho com o método de roteamento de tráfego por “Prioridade”.

![Tráfego de roteamento de “Desempenho” com failover preferencial][6]

Como o ponto de extremidade da Europa Ocidental tem prioridade maior que o ponto de extremidade do Oeste dos EUA, todo o tráfego é enviado para o ponto de extremidade da Europa Ocidental quando os dois pontos de extremidade estão online. Se a Europa Ocidental falhar, seu tráfego será direcionado para o Oeste dos EUA. Com o perfil aninhado, o tráfego é direcionado para a Ásia Oriental somente quando há uma falha na Europa Ocidental e no Oeste dos EUA.

É possível repetir esse padrão para todas as regiões. Substitua os três pontos de extremidade no perfil pai por três perfis filho, cada um deles fornecendo uma sequência de failover priorizada.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: controlando o roteamento de tráfego de “Desempenho” entre vários pontos de extremidade na mesma região

Suponha que o método de roteamento de tráfego por “Desempenho” é usado em um perfil que tem mais de um ponto de extremidade em determinada região. Por padrão, o tráfego direcionado para essa região é distribuído uniformemente entre todos os pontos de extremidade disponíveis nessa região.

![Roteamento de tráfego por “desempenho” com distribuição de tráfego na região (comportamento padrão)][7]

Em vez de adicionar vários pontos de extremidade na Europa Ocidental, esses pontos de extremidade são fechados em um perfil filho separado. O perfil filho é adicionado ao pai como o único ponto de extremidade da Europa Ocidental. As configurações no perfil filho podem controlar a distribuição de tráfego com a Europa Ocidental, habilitando o roteamento de tráfego ponderado ou baseado em prioridade nessa região.

![Roteamento de tráfego por “desempenho” com distribuição de tráfego na região personalizado][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: configurações de monitoramento por ponto de extremidade

Suponha que você está usando o Gerenciador de Tráfego para migrar diretamente o tráfego de um site local herdado para uma nova versão baseada em Nuvem hospedada no Azure. Para o site herdado, você deseja usar o URI da home page para monitorar a integridade do site. Mas para a nova versão baseada em Nuvem, você está implementando uma página personalizada de monitoramento (caminho “/monitor.aspx”) que inclui verificações adicionais.

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego (comportamento padrão)][9]

As configurações de monitoramento em um perfil do Gerenciador de Tráfego se aplicam a todos os pontos de extremidade em um único perfil. Com perfis aninhados, você usa um perfil filho diferente por site para definir diferentes configurações de monitoramento.

![Monitoramento do ponto de extremidade do Gerenciador de Tráfego com definições por ponto de extremidade][10]

## <a name="faqs"></a>Perguntas Frequentes

* [Como fazer configurar perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Quantas camadas de aninhamento o Gerenciador de tráfego dá suporte?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Posso misturar outros tipos de ponto de extremidade com perfis filho aninhados, no mesmo perfil do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Como o modelo de cobrança se aplica a perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Há um impacto no desempenho para perfis aninhados?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Como o Gerenciador de tráfego computa a integridade de um ponto de extremidade aninhado em um perfil pai?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [perfis do Gerenciador de Tráfego](traffic-manager-overview.md)

Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
