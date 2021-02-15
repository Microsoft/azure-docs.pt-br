---
title: Diagnósticos com métricas, alertas e integridade de recursos-Azure Standard Load Balancer
description: Use as informações disponíveis sobre métricas, alertas e recursos de integridade para diagnosticar sua Standard Load Balancer do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2021
ms.author: allensu
ms.openlocfilehash: 43d83d994c9a4ee3cf89b584f6c3835a62fa2cfe
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806006"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnóstico do Standard Load Balancer com métricas, alertas e integridade de recursos

O Standard Load Balancer do Azure expõe os seguintes recursos de diagnóstico:

* **Métricas e alertas multidimensionais**: fornece recursos de diagnóstico multidimensionais por meio de [Azure monitor](../azure-monitor/overview.md) para configurações padrão do Load Balancer. Você pode monitorar, gerenciar e solucionar problemas de seus recursos padrão do Load Balancer.

* **Resource Health**: o status de Resource Health do seu Load Balancer está disponível na página Resource Health em monitor. Essa verificação automática informa sobre a disponibilidade atual do recurso de Load Balancer.
Este artigo fornece um tour rápido dessas funcionalidades e oferece maneiras de usá-las para o Load Balancer Standard. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Métricas multidimensionais

O Azure Load Balancer fornece métricas multidimensionais por meio das métricas do Azure na portal do Azure e ajuda você a obter informações de diagnóstico em tempo real sobre os recursos do balanceador de carga. 

As várias configurações do Load Balancer Standard oferecem as seguintes métricas:

| Métrica | Tipo de recurso | Descrição | Agregação recomendada |
| --- | --- | --- | --- |
| Disponibilidade do caminho de dados | Balanceador de carga público e interno | O Load Balancer Standard usa continuamente o caminho de dados de dentro de uma região para o front-end do balanceador de carga e até a pilha do SDN compatível com a sua VM. Contanto que instâncias íntegras permaneçam, a medição seguirá o mesmo caminho que o tráfego com balanceamento de carga do seu aplicativo. O caminho de dados que seus clientes usam também é validado. A medição é invisível para seu aplicativo e não interfere com outras operações.| Média |
| Status de investigação de integridade | Balanceador de carga público e interno | O Load Balancer Standard usa um serviço de investigação de integridade distribuído que monitora a integridade do ponto de extremidade do aplicativo de acordo com as definições de configuração. Essa métrica fornece uma exibição agregada ou por ponto de extremidade filtrado de cada ponto de extremidade de instância no pool do balanceador de carga. É possível ver como o Load Balancer exibe a integridade de seu aplicativo conforme indicado pela configuração de sua investigação de integridade. |  Média |
| Contagem de SYN (sincronizar) | Balanceador de carga público e interno | O Load Balancer Standard não encerra conexões TCP nem interage com fluxos de pacote TCP ou UDP. Fluxos e seus handshakes estão sempre entre a origem e a instância VM. Para solucionar melhor os problemas dos cenários de protocolo TCP, é possível usar contadores de pacotes SYN para entender quantas tentativas de conexão TCP são feitas. A métrica informa o número de pacotes SYN do TCP que foram recebidos.| Somar |
| Contagem de conexões SNAT | Balanceador de carga público |O Load Balancer Standard relata o número de fluxos de saída mascarados para o front-end do endereço IP Público. As portas SNAT (conversão de endereços de rede) de origem são um recurso esgotável. Essa métrica pode dar uma indicação do grau de dependência que seu aplicativo tem do SNAT para fluxos com origem externa. Contadores para fluxos SNAT de saída bem-sucedidos e com falha são relatados e podem ser usados para solucionar problemas e entender a integridade dos fluxos de saída.| Somar |
| Portas SNAT alocadas | Balanceador de carga público | Standard Load Balancer relata o número de portas SNAT alocadas por instância de back-end | Cerca. |
| Portas SNAT usadas | Balanceador de carga público | Standard Load Balancer relata o número de portas SNAT utilizadas por instância de back-end. | Média | 
| Contagem de Bytes |  Balanceador de carga público e interno | O Load Balancer Standard informa os dados processados por front-end. Você pode observar que os bytes não são distribuídos igualmente nas instâncias de back-end. Isso é esperado, pois o algoritmo de Load Balancer do Azure se baseia em fluxos | Somar |
| Contagem de pacotes |  Balanceador de carga público e interno | O Load Balancer Standard relata os pacotes processados por front-end.| Somar |

  >[!NOTE]
  >Ao usar a distribuição de tráfego de um balanceador de carga interno por meio de um pacote SYN NVA ou firewall, a contagem de bytes e as métricas de contagem de pacotes não estarão disponíveis e serão exibidas como zero. 
  
  >[!NOTE]
  >As agregações Max e min não estão disponíveis para a contagem de SYN, a contagem de pacotes, a contagem de conexões SNAT e as métricas de contagem de bytes 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Exibir suas métricas do balanceador de carga no portal do Azure

O portal do Azure expõe as métricas do balanceador de carga por meio da página métricas, que está disponível na página de recursos do balanceador de carga para um recurso específico e a página Azure Monitor. 

Para exibir as métricas de seus recursos do Load Balancer Standard:
1. Vá para a página métricas e siga um destes procedimentos:
   * Na página de recursos do balanceador de carga, selecione o tipo de métrica na lista suspensa.
   * Na página do Azure Monitor, selecione o recurso do balanceador de carga.
2. Defina o tipo de agregação de métrica apropriado.
3. Ou configure a filtragem e o agrupamento necessários.
4. Opcionalmente, configure o intervalo de tempo e a agregação. Por hora padrão, é exibido em UTC.

  >[!NOTE] 
  >A agregação de tempo é importante ao interpretar determinadas métricas, pois os dados são amostrados uma vez por minuto. Se a agregação de tempo for definida como cinco minutos e a soma do tipo de agregação de métrica for usada para métricas como a alocação de SNAT, o grafo exibirá cinco vezes o total de portas SNAT alocadas. 

![Métricas para Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Figura: métrica de disponibilidade de caminho de dados para Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperar as métricas multidimensionais programaticamente por meio de APIs

Para obter diretrizes sobre API para recuperar valores e definições de métricas multidimensionais, consulte o [passo a passo da API REST de Monitoramento do Azure](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-definitions-multi-dimensional-api). Essas métricas podem ser gravadas em uma conta de armazenamento adicionando uma [configuração de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) para a categoria ' todas as métricas '. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Configurar alertas para métricas multidimensionais ###

O Azure Standard Load Balancer dá suporte a alertas facilmente configuráveis para métricas multidimensionais. Configure limites personalizados para métricas específicas para disparar alertas com diferentes níveis de severidade para capacitar uma experiência de monitoramento de recursos sem toque.

Para configurar alertas:
1. Ir para a subfolha de alerta para o balanceador de carga
1. Criar nova regra de alerta
    1.  Configurar condição de alerta
    1.  Adicional Adicionar grupo de ação para reparo automatizado
    1.  Atribuir severidade de alerta, nome e descrição que habilita a reação intuitiva

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Cenários comuns de diagnósticos e modos de exibição recomendados

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>O caminho de dados está ativo e disponível para o meu front-end Load Balancer?
<details><summary>Expanda</summary>

A métrica de disponibilidade do caminho de dados descreve a integridade do caminho de dados dentro da região para o host de computação onde as VMs estão localizadas. A métrica é uma reflexão da integridade da infraestrutura do Azure. É possível usar a métrica para:
- Monitorar a disponibilidade externa do seu serviço
- Obter detalhes e entender se a plataforma na qual o serviço é implantado está íntegra ou se a instância do aplicativo ou o SO convidado está íntegro.
- Isolar se um evento está relacionado ao seu serviço ou o plano de dados subjacente. Não confunda essa métrica com o status da investigação de integridade ("disponibilidade da instância de back-end").

Para obter a disponibilidade do caminho de dados para seus recursos de Standard Load Balancer:
1. Verifique se o recurso do balanceador de carga correto está selecionado. 
2. Na lista suspensa **métrica** , selecione **disponibilidade do caminho de dados**. 
3. Na lista suspensa **Agregação**, selecione **Méd**. 
4. Além disso, adicione um filtro no endereço IP de front-end ou na porta de front-end como a dimensão com o endereço IP de front-end ou a porta de front-ends necessária e agrupe-os pela dimensão selecionada.

![Investigação do VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: detalhes de investigação de front-end Load Balancer*

A métrica é gerada por uma medida de ativa e dentro da faixa. Um serviço de investigação dentro da região origina o tráfego da medida. O serviço é ativado assim que você cria uma implantação com um front-end público, e ele continua até que você remova o front-end. 

Um pacote que corresponde ao front-end e a uma regra de sua implantação é gerado periodicamente. Ele atravessa a região da origem para o host no qual uma VM no pool de back-end está localizada. A infraestrutura do balanceador de carga executa as mesmas operações de balanceamento de carga e de translação, assim como faz para todos os outros tráfegos. Essa investigação é feita dentro da banda no seu ponto de extremidade com balanceamento de carga. Depois que a investigação chega no host de computação no qual uma VM íntegra no pool de back-end está localizada, o host de computação gera uma resposta para o serviço de investigação. A VM não vê esse tráfego.

A disponibilidade do caminho de DataPath falha pelos seguintes motivos:
- Sua implantação não tem nenhuma VM íntegra restante no pool de back-end. 
- Ocorreu uma interrupção de infraestrutura.

Para fins de diagnóstico, você pode usar a [métrica de disponibilidade de caminho de dados junto com o status da investigação de integridade](#vipavailabilityandhealthprobes).

Use **Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>As instâncias de back-end para o meu Load Balancer estão respondendo às investigações?
<details>
  <summary>Expanda</summary>
A métrica de status de investigação de integridade descreve a integridade da implantação do aplicativo, conforme configurado por você ao configurar a investigação de integridade do balanceador de carga. O balanceador de carga usa o status da investigação de integridade para determinar para onde enviar novos fluxos. As investigações de integridade se originam de um endereço de infraestrutura do Azure e são visíveis no sistema operacional convidado da VM.

Para obter o status da investigação de integridade para seus recursos de Standard Load Balancer:
1. Selecione a métrica **status da investigação de integridade** com tipo de agregação **Méd** . 
2. Aplique um filtro na porta ou endereço IP de front-end necessário (ou ambos).

As investigações de integridade falham pelos seguintes motivos:
- Ao configurar uma investigação de integridade em uma porta que não está escutando ou não está respondendo ou que está usando o protocolo incorreto. Se seu serviço está usando regras de DSR (retorno de servidor direto ou IP flutuante), verifique ele está escutando no endereço IP da configuração IP do NIC e não apenas no loopback configurado com o endereço IP de front-end.
- Sua investigação não é permitida pelo Grupo de Segurança de Rede, o firewall do SO convidado da VM ou os filtros da camada do aplicativo.

Use **Média** como a agregação para a maioria dos cenários.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Como verificar as minhas estatísticas de conexão de saída? 
<details>
  <summary>Expanda</summary>
A métrica de conexões SNAT descreve o volume de conexões bem-sucedidas e com falha para [fluxos de saída](./load-balancer-outbound-connections.md).

Um volume de conexões com falha maior que zero indica o esgotamento da porta SNAT. É necessário investigar mais para determinar o que pode estar causando essas falhas. O esgotamento de porta SNAT manifesta-se como uma falha para estabelecer um [fluxo de saída](./load-balancer-outbound-connections.md). Examine o artigo sobre conexões de saída para entender os cenários e mecanismos no trabalho e para saber como minimizar e criar para evitar o esgotamento da porta SNAT. 

Para obter estatísticas de conexão SNAT:
1. Selecione o tipo de métrica **Conexões SNAT** e **Soma** como agregação. 
2. Agrupar por **estado de conexão** para contagens de conexão SNAT bem-sucedidas e com falha para serem representadas por linhas diferentes. 

![Conexão SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: contagem de conexões SNAT Load Balancer*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Como fazer verificar o uso e a alocação da porta SNAT?
<details>
  <summary>Expanda</summary>
A métrica portas de SNAT usadas rastreia quantas portas SNAT estão sendo consumidas para manter fluxos de saída. Isso indica quantos fluxos exclusivos são estabelecidos entre uma origem da Internet e uma VM de back-end ou um conjunto de dimensionamento de máquinas virtuais que está atrás de um balanceador de carga e não tem um endereço IP público. Ao comparar o número de portas SNAT que você está usando com a métrica portas de SNAT alocadas, você pode determinar se o serviço está experimentando ou em risco de esgotamento de SNAT e falha resultante do fluxo de saída. 

Se suas métricas indicarem o risco de falha de [fluxo de saída](./load-balancer-outbound-connections.md) , referencie o artigo e execute as etapas para mitigar isso para garantir a integridade do serviço.

Para exibir o uso e a alocação da porta SNAT:
1. Defina a agregação de tempo do grafo como 1 minuto para garantir que os dados desejados sejam exibidos.
1. Selecione **portas SNAT usadas** e/ou **portas SNAT alocadas** como o tipo de métrica e **média** como a agregação
    * Por padrão, essas métricas são o número médio de portas SNAT alocadas ou usadas por cada VM ou VMSS de back-end, correspondentes a todos os IPs públicos de front-end mapeados para a Load Balancer, agregadas sobre TCP e UDP.
    * Para exibir as portas SNAT totais usadas pelo ou alocadas para o balanceador de carga, use a **soma** de agregação de métrica
1. Filtre para um **tipo de protocolo** específico, um conjunto de **IPS de back-end** e/ou IPS de front- **end**.
1. Para monitorar a integridade por backend ou por meio de uma instância de front-end, aplique a divisão. 
    * A divisão de observação permite que apenas uma única métrica seja exibida de cada vez. 
1. Por exemplo, para monitorar o uso de SNAT para fluxos TCP por máquina, agregar por **média**, dividir por **IPS de back-end** e filtrar por **tipo de protocolo**. 

![Alocação e uso de SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figura: alocação e uso médio da porta SNAT TCP para um conjunto de VMs de back-end*

![Uso de SNAT por instância de back-end](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figura: uso da porta TCP SNAT por instância de back-end*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Como verificar as tentativas de conexão de entrada/saída para meu serviço?
<details>
  <summary>Expanda</summary>
Uma métrica de pacotes SYN descreve o volume de pacotes TCP SYN, que chegaram ou foram enviados (para [fluxos de saída](./load-balancer-outbound-connections.md)) associados a um front-end específico. É possível usar essa métrica para entender as tentativas de conexão TCP com seu serviço.

Use **sum** como a agregação para a maioria dos cenários.

![Conexão SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Load Balancer contagem de SYN*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Como verificar o consumo de largura de banda da rede? 
<details>
  <summary>Expanda</summary>
A métrica de contadores de pacote e de bytes descreve o volume de bytes e de pacotes enviados ou recebidos pelo seu serviço por front-end.

Use **sum** como a agregação para a maioria dos cenários.

Para obter estatísticas de contagem de bytes ou de pacotes:
1. Selecione a **contagem de bytes** e/ou o tipo de métrica de **contagem de pacotes** , com **sum** como a agregação. 
2. Siga um destes procedimentos:
   * Aplique um filtro em um IP de front-end específico, em uma porta de front-end, em um IP de back-end ou em uma porta de back-end.
   * Obtenha estatísticas gerais para seu recurso de balanceador de carga sem nenhuma filtragem.

![Contagem de Bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: contagem de Load Balancer bytes*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Como posso diagnosticar a implantação do meu balanceador de carga?
<details>
  <summary>Expanda</summary>
Usando uma combinação das métricas de status de investigação de integridade e disponibilidade do caminho de dados em um único gráfico, você pode identificar onde procurar o problema e resolver o problema. É possível obter a garantia de que o Azure está funcionando corretamente e usar esse conhecimento para determinar conclusivamente que a configuração ou o aplicativo é a causa raiz.

Você pode usar métricas de investigação de integridade para entender como o Azure exibe a integridade de sua implantação de acordo com a configuração que você forneceu. Olhar para investigações de integridade é sempre uma excelente primeira etapa no monitoramento ou determinação de uma causa.

Você pode levar um passo adiante e usar a métrica de disponibilidade de caminho de dados para obter informações sobre como o Azure exibe a integridade do plano de dados subjacente que é responsável pela sua implantação específica. Ao combinar as duas métricas, é possível isolar onde a falha pode estar, conforme ilustrado nesse exemplo:

![Combinando métricas de status de investigação de integridade e disponibilidade de caminho de dados](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figura: combinação de métricas de status de investigação de integridade e disponibilidade do caminho de dados*

O gráfico exibe as seguintes informações:
- A infraestrutura que hospeda suas VMs não estava disponível e está em 0% no início do gráfico. Posteriormente, a infraestrutura estava íntegra e as VMs estavam acessíveis e mais de uma VM foi colocada no back-end. Essas informações são indicadas pelo rastreamento azul para disponibilidade de caminho de dados, que foi mais tarde em 100%. 
- O status da investigação de integridade, indicado pelo rastreamento roxo, está em 0% no início do gráfico. A área circulada em verde destaca onde o status da investigação de integridade se tornou íntegro e, em que ponto a implantação do cliente foi capaz de aceitar novos fluxos.

O gráfico permite que os clientes resolvam problemas da implantação sozinhos sem a necessidade de adivinhar ou perguntar ao suporte se outros problemas estão ocorrendo. O serviço não estava disponível porque as investigações de integridade estavam falhando devido a um erro de configuração ou a um aplicativo com falha.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Status de integridade de recurso

O status de integridade para os recursos do Load Balancer Standard é exposto por meio do **Recursos de integridade** existente em **Monitor > Integridade do Serviço**. Ela é avaliada a cada **dois minutos** medindo a disponibilidade do caminho de dados que determina se os pontos de extremidade de balanceamento de carga de front-end estão disponíveis.

| Status de integridade de recurso | Descrição |
| --- | --- |
| Disponível | O recurso padrão do Load Balancer está íntegro e disponível. |
| Degradado | O balanceador de carga padrão tem eventos iniciados pela plataforma ou pelo usuário que afetam o desempenho. A métrica de disponibilidade do Datapath relatou menos de 90%, mas mais de 25% de integridade por pelo menos dois minutos. Você passará por um impacto de desempenho moderado a severo. [Siga o guia de solução de problemas do RHC](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) para determinar se há eventos iniciados pelo usuário causando impacto na disponibilidade.
| Indisponível | O recurso padrão do Load Balancer não está íntegro. A métrica de disponibilidade do caminho de dado relatou menos a integridade de 25% por pelo menos dois minutos. Você terá um impacto significativo no desempenho ou falta de disponibilidade para a conectividade de entrada. Pode haver eventos de usuário ou plataforma causando indisponibilidade. [Siga o guia de solução de problemas do RHC](https://docs.microsoft.com/azure/load-balancer/troubleshoot-rhc) para determinar se há eventos iniciados pelo usuário que afetam sua disponibilidade. |
| Unknown | O status de integridade do recurso para o recurso de Load Balancer padrão ainda não foi atualizado ou não recebeu informações de disponibilidade do caminho de dados dos últimos 10 minutos. Esse estado deve ser transitório e reflete o status correto assim que os dados são recebidos. |

Para exibir a integridade dos seus recursos do Load Balancer Standard:
1. Selecione **monitorar**  >  **integridade do serviço**.

   ![Página do Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: o link da Integridade do Serviço no Azure Monitor*

2. Selecione **Resource Health** e certifique-se de que a **ID da assinatura** e o **Tipo de recurso = Load Balancer** estão selecionados.

   ![Status de integridade de recurso](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: selecione o recurso para o modo de exibição de integridade*

3. Na lista, selecione o recurso do Load Balancer para exibir o status da integridade histórico.

    ![Status de integridade do Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: modo de exibição de integridade de recurso do Load Balancer*
 
A descrição do status de integridade do recurso genérico está disponível na [documentação do RHC](../service-health/resource-health-overview.md). Para obter status específicos para os Azure Load Balancer estão listados na tabela abaixo: 


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o uso de [percepções](https://docs.microsoft.com/azure/load-balancer/load-balancer-insights) para exibir essas métricas pré-configuradas para seu Load Balancer
- Saiba mais sobre o [Load Balancer Standard](./load-balancer-overview.md).
- Saiba mais sobre a [Conectividade de saída do balanceador de carga](./load-balancer-outbound-connections.md).
- Saiba mais sobre [o Azure Monitor](../azure-monitor/overview.md).
- Saiba mais sobre o [API de REST do Azure Monitor](/rest/api/monitor/) e [como recuperar as métricas por meio da API REST](/rest/api/monitor/metrics/list).
