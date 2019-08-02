---
title: Diagnóstico do Azure Standard Load Balancer
titlesuffix: Azure Load Balancer
description: Use as métricas e informações de integridade disponíveis para diagnóstico para o Azure Load Balancer Standard.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: allensu
ms.openlocfilehash: e0329f5f975b67460796bf7dd9429752549a3483
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274473"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Diagnóstico de métricas e integridade para o Load Balancer Standard

O Balanceador de Carga Padrão do Azure expõe o Balanceador de Carga Padrão do Azure que fornece aos recursos os seguintes recursos de diagnóstico:
* **Métricas multidimensionais**: Fornece novas funcionalidades de diagnóstico multidimensionais por meio do [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para configurações de balanceadores de carga públicos e internos. É possível monitorar, gerenciar e resolver problemas dos seus recursos de balanceador de carga.

* **Resource Health**: A página do Load Balancer no portal do Azure e a página do Resource Health (em Monitor) expõem a seção Resource Health para a configuração do balanceador de carga público do Standard Load Balancer.

Este artigo fornece um tour rápido dessas funcionalidades e oferece maneiras de usá-las para o Load Balancer Standard.

## <a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece novas métricas multidimensionais por meio das novas métricas do Azure no portal do Azure e ajuda você a obter informações de diagnóstico em tempo real sobre os recursos do balanceador de carga. 

As várias configurações do Load Balancer Standard oferecem as seguintes métricas:

| Métrica | Tipo de recurso | DESCRIÇÃO | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade do caminho de dados (disponibilidade VIP)| Balanceador de carga público e interno | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do balanceador de carga e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados que seus clientes usam também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.| Average |
| Status da investigação de integridade (disponibilidade DIP) | Balanceador de carga público e interno | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância no pool do balanceador de carga. É possível ver como o Load Balancer exibe a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade. |  Average |
| Pacotes SYN (sincronizar) | Balanceador de carga público e interno | O Load Balancer Standard não encerra conexões TCP nem interage com fluxos de pacote TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.| Average |
| Conexões SNAT | Balanceador de carga público |O Load Balancer Standard relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT (conversão de endereços de rede) de origem são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa. Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.| Average |
| Contadores de bytes |  Balanceador de carga público e interno | O Load Balancer Standard informa os dados processados por front-end.| Average |
| Contadores de pacotes |  Balanceador de carga público e interno | O Load Balancer Standard relata os pacotes processados por front-end.| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Exibir suas métricas do balanceador de carga no portal do Azure

O portal do Azure expõe as métricas do balanceador de carga por meio da página métricas, que está disponível na página de recursos do balanceador de carga para um recurso específico e a página Azure Monitor. 

Para exibir as métricas de seus recursos do Load Balancer Standard:
1. Vá para a página métricas e siga um destes procedimentos:
   * Na página de recursos do balanceador de carga, selecione o tipo de métrica na lista suspensa.
   * Na página do Azure Monitor, selecione o recurso do balanceador de carga.
2. Defina o tipo de agregação adequado.
3. Ou configure a filtragem e o agrupamento necessários.

    ![Métricas para Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Figura: Métrica de disponibilidade de caminho de dados para Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar as métricas multidimensionais programaticamente por meio de APIs

Para obter diretrizes sobre API para recuperar valores e definições de métricas multidimensionais, consulte o [passo a passo da API REST de Monitoramento do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Cenários comuns de diagnósticos e modos de exibição recomendados

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>O caminho de dados está disponível e funcionando para o VIP do balanceador de carga?

A métrica de disponibilidade do VIP descreve a integridade do caminho de dados dentro da região para o host de computação onde se encontram suas VMs. A métrica é uma reflexão da integridade da infraestrutura do Azure. É possível usar a métrica para:
- Monitorar a disponibilidade externa do seu serviço
- Obter detalhes e entender se a plataforma na qual o serviço é implantado está íntegra ou se a instância do aplicativo ou o SO convidado está íntegro.
- Isolar se um evento está relacionado ao seu serviço ou o plano de dados subjacente. Não confunda essa métrica com o status de investigação de integridade ("disponibilidade DIP").

Para obter a disponibilidade do caminho de dados para seus recursos de Standard Load Balancer:
1. Verifique se o recurso do balanceador de carga correto está selecionado. 
2. Na lista suspensa **métrica** , selecione **disponibilidade do caminho de dados**. 
3. Na lista suspensa **Agregação**, selecione **Méd**. 
4. Além disso, adicione um filtro no endereço IP de front-end ou na porta de front-end como a dimensão com o endereço IP de front-end ou a porta de front-ends necessária e agrupe-os pela dimensão selecionada.

![Investigação do VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Detalhes de investigação de front-end Load Balancer*

A métrica é gerada por uma medida de ativa e dentro da faixa. Um serviço de investigação dentro da região origina o tráfego da medida. O serviço é ativado assim que você cria uma implantação com um front-end público, e ele continua até que você remova o front-end. 

Um pacote que corresponde ao front-end e a uma regra de sua implantação é gerado periodicamente. Ele atravessa a região da origem para o host no qual uma VM no pool de back-end está localizada. A infraestrutura do balanceador de carga executa as mesmas operações de balanceamento de carga e de translação, assim como faz para todos os outros tráfegos. Essa investigação é feita dentro da banda no seu ponto de extremidade com balanceamento de carga. Depois que a investigação chega no host de computação no qual uma VM íntegra no pool de back-end está localizada, o host de computação gera uma resposta para o serviço de investigação. A VM não vê esse tráfego.

A disponibilidade de VIP falha pelos seguintes motivos:
- Sua implantação não tem nenhuma VM íntegra restante no pool de back-end. 
- Ocorreu uma interrupção de infraestrutura.

Para fins de diagnóstico, você pode usar a [métrica de disponibilidade de caminho de dados junto com o status da investigação de integridade](#vipavailabilityandhealthprobes).

Use **Média** como a agregação para a maioria dos cenários.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>As instâncias de back-end para o VIP estão respondendo às investigações?

A métrica de status de investigação de integridade descreve a integridade da implantação do aplicativo, conforme configurado por você ao configurar a investigação de integridade do balanceador de carga. O balanceador de carga usa o status da investigação de integridade para determinar para onde enviar novos fluxos. As investigações de integridade se originam de um endereço de infraestrutura do Azure e são visíveis no sistema operacional convidado da VM.

Para obter o status da investigação de integridade para seus recursos de Standard Load Balancer:
1. Selecione a métrica **status da investigação de integridade** com tipo de agregação **Méd** . 
2. Aplique um filtro na porta ou endereço IP de front-end necessário (ou ambos).

As investigações de integridade falham pelos seguintes motivos:
- Ao configurar uma investigação de integridade em uma porta que não está escutando ou não está respondendo ou que está usando o protocolo incorreto. Se seu serviço está usando regras de DSR (retorno de servidor direto ou IP flutuante), verifique ele está escutando no endereço IP da configuração IP do NIC e não apenas no loopback configurado com o endereço IP de front-end.
- Sua investigação não é permitida pelo Grupo de Segurança de Rede, o firewall do SO convidado da VM ou os filtros da camada do aplicativo.

Use **Média** como a agregação para a maioria dos cenários.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como verificar as minhas estatísticas de conexão de saída? 

A métrica de conexões SNAT descreve o volume de conexões bem-sucedidas e com falha para [fluxos de saída](https://aka.ms/lboutbound).

Um volume de conexões com falha maior que zero indica o esgotamento da porta SNAT. É necessário investigar mais para determinar o que pode estar causando essas falhas. O esgotamento de porta SNAT manifesta-se como uma falha para estabelecer um [fluxo de saída](https://aka.ms/lboutbound). Examine o artigo sobre conexões de saída para entender os cenários e mecanismos no trabalho e para saber como minimizar e criar para evitar o esgotamento da porta SNAT. 

Para obter estatísticas de conexão SNAT:
1. Selecione o tipo de métrica **Conexões SNAT** e **Soma** como agregação. 
2. Agrupe por **Estado de Conexão** para contagens de conexão SNAT bem-sucedidas e com falha representadas por linhas diferentes. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Contagem de conexões SNAT do Load Balancer*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como verificar as tentativas de conexão de entrada/saída para meu serviço?

Uma métrica de pacotes SYN descreve o volume de pacotes TCP SYN, que chegaram ou foram enviados (para [fluxos de saída](https://aka.ms/lboutbound)) associados a um front-end específico. É possível usar essa métrica para entender as tentativas de conexão TCP com seu serviço.

Use **Total** como a agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Contagem de SYN do Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como verificar o consumo de largura de banda da rede? 

A métrica de contadores de pacote e de bytes descreve o volume de bytes e de pacotes enviados ou recebidos pelo seu serviço por front-end.

Use **Total** como a agregação para a maioria dos cenários.

Para obter estatísticas de contagem de bytes ou de pacotes:
1. Selecione o tipo de métrica **Contagem de Bytes** e/ou **Contagem de Pacotes** com **Méd** como a agregação. 
2. Faça uma das opções a seguir:
   * Aplique um filtro em um IP de front-end específico, em uma porta de front-end, em um IP de back-end ou em uma porta de back-end.
   * Obtenha estatísticas gerais para seu recurso de balanceador de carga sem nenhuma filtragem.

![Contagem de Bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Contagem de bytes do Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Como posso diagnosticar a implantação do meu balanceador de carga?

Usando uma combinação das métricas de investigação de integridade e disponibilidade do VIP em um único gráfico, é possível identificar onde procurar o problema e resolvê-lo. É possível obter a garantia de que o Azure está funcionando corretamente e usar esse conhecimento para determinar conclusivamente que a configuração ou o aplicativo é a causa raiz.

Você pode usar métricas de investigação de integridade para entender como o Azure exibe a integridade de sua implantação de acordo com a configuração que você forneceu. Olhar para investigações de integridade é sempre uma excelente primeira etapa no monitoramento ou determinação de uma causa.

É possível executar uma etapa adicional e usar as métricas de disponibilidade do VIP para obter insights sobre como o Azure exibe a integridade do plano de dados subjacente responsável por sua implantação específica. Ao combinar as duas métricas, é possível isolar onde a falha pode estar, conforme ilustrado nesse exemplo:

![Combinando métricas de status de investigação de integridade e disponibilidade de caminho de dados](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: Combinando métricas de status de investigação de integridade e disponibilidade de caminho de dados*

O gráfico exibe as seguintes informações:
- A infraestrutura que hospeda suas VMs não estava disponível e está em 0% no início do gráfico. Posteriormente, a infraestrutura estava íntegra e as VMs estavam acessíveis e mais de uma VM foi colocada no back-end. Essas informações são indicadas pelo rastreamento azul para disponibilidade de caminho de dados (disponibilidade de VIP), que foi posterior em 100%. 
- O status da investigação de integridade (disponibilidade DIP), indicado pelo rastreamento roxo, está em 0% no início do gráfico. A área circulada em verde destaca onde o status da investigação de integridade (disponibilidade DIP) se tornou íntegro e, em que ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes resolvam problemas da implantação sozinhos sem a necessidade de adivinhar ou perguntar ao suporte se outros problemas estão ocorrendo. O serviço não estava disponível porque as investigações de integridade estavam falhando devido a um erro de configuração ou a um aplicativo com falha.

## <a name = "ResourceHealth"></a>Status de integridade de recurso

O status de integridade para os recursos do Load Balancer Standard é exposto por meio do **Recursos de integridade** existente em **Monitor > Integridade do Serviço**.

>[!NOTE]
>No momento, o status da integridade do recurso do Load Balancer está disponível apenas para configuração pública ou para o Load Balancer Standard. Os recursos de balanceador de carga internos ou os SKUs básicos de recursos do Load Balancer não expõem a integridade do recurso.

Para exibir a integridade dos seus recursos do Load Balancer Standard:
1. Selecione **Monitorar** > **Integridade do Serviço**.

   ![Página do Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: O link da Integridade do Serviço no Azure Monitor*

2. Selecione **Resource Health** e certifique-se de que a **ID da assinatura** e o **Tipo de recurso = Load Balancer** estão selecionados.

   ![Status de integridade de recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selecionar recurso para exibição de integridade*

3. Na lista, selecione o recurso do Load Balancer para exibir o status da integridade histórico.

    ![Status de integridade do Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Exibição de Resource Health do Load Balancer*
 
Os vários status da integridade do recurso e suas descrições estão listadas na seguinte tabela: 

| Status de integridade de recurso | DESCRIÇÃO |
| --- | --- |
| Disponível | O recurso do balanceador de carga padrão público está íntegro e disponível. |
| Indisponível | O recurso do balanceador de carga padrão público não está íntegro. Faça o diagnóstico da integridade selecionando **Azure Monitor** > **Métricas**.<br>(O status *Não disponível* também pode significar que o recurso não está conectado ao balanceador de carga padrão público.) |
| Unknown | O status da integridade do recurso do seu recurso de balanceador de carga padrão público ainda não foi atualizado.<br>(O status *Desconhecido* também pode significar que o recurso não está conectado ao balanceador de carga padrão público.)  |

## <a name="limitations"></a>Limitações 

- A disponibilidade do caminho de dados (disponibilidade VIP) não está disponível para front-ends internos do Load Balancer.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- Saiba mais sobre a [Conectividade de saída do balanceador de carga](https://aka.ms/lboutbound).
- Saiba mais sobre [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Saiba mais sobre o [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) e [como recuperar as métricas por meio da API REST](/rest/api/monitor/metrics/list).


