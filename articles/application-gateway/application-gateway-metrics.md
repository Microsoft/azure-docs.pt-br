---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: surmb
ms.openlocfilehash: 9faa3a284aa7151880526c1ee70cfadc3dbf3089
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576106"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

O gateway de aplicativo publica pontos de dados, chamados métricas, para [Azure monitor](../azure-monitor/overview.md) para o desempenho do seu gateway de aplicativo e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [Azure monitor métricas](../azure-monitor/essentials/data-platform-metrics.md).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

O gateway de aplicativo fornece várias métricas de tempo internas relacionadas à solicitação e à resposta, que são todas medidas em milissegundos. 

![Diagrama de métricas de tempo para o gateway de aplicativo.](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Se houver mais de um ouvinte no gateway de aplicativo, sempre filtre pela dimensão do *ouvinte* , comparando métricas de latência diferentes para obter uma inferência significativa.

- **Tempo de conexão de back-end**

  Tempo gasto estabelecendo uma conexão com o aplicativo back-end. 

  Isso inclui a latência de rede, bem como o tempo gasto pela pilha TCP do servidor de back-end para estabelecer novas conexões. No caso do TLS, também inclui o tempo de handshake. 

- **Tempo de resposta do primeiro byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do primeiro byte do cabeçalho de resposta. 

  Isso aproxima a soma do *tempo de conexão de back-end*, o tempo gasto pela solicitação para alcançar o back-end do gateway de aplicativo, o tempo gasto pelo aplicativo de back-end para responder (o tempo que o servidor levou para gerar conteúdo, potencialmente buscar consultas de banco de dados) e o tempo gasto pelo primeiro byte da resposta para alcançar o gateway de aplicativo do back-end.

- **Tempo de resposta do último byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta. 

  Isso aproxima a soma do *tempo de resposta do primeiro byte de back-end* e do tempo de transferência de dados (dependendo do tamanho dos objetos solicitados e da latência da rede do servidor, esse número pode variar bastante).

- **Tempo total do gateway de aplicativo**

  Tempo médio que leva para que uma solicitação seja recebida, processada e sua resposta seja enviada. 

  Esse é o intervalo a partir do momento em que o gateway de aplicativo recebe o primeiro byte da solicitação HTTP até a hora em que o último byte de resposta foi enviado ao cliente. Isso inclui o tempo de processamento usado pelo gateway de aplicativo, o *tempo de resposta do último byte de back-end*, o tempo gasto pelo gateway de aplicativo para enviar toda a resposta e o *RTT do cliente*.

- **RTT do Cliente**

  Tempo médio de ida e volta entre clientes e o gateway de aplicativo.



Essas métricas podem ser usadas para determinar se a lentidão observada é devido à rede do cliente, ao desempenho do gateway de aplicativo, à rede de back-end e à saturação de pilha TCP do servidor de back-end, ao desempenho do aplicativo de backend ou ao tamanho de arquivo grande.

Por exemplo, se houver um pico na tendência de *tempo de resposta do primeiro byte de back-end* , mas a tendência de *tempo de conexão de back-end* for estável, poderá ser inferida que o gateway de aplicativo para latência de back-end e o tempo necessário para estabelecer a conexão é estável e o pico é causado devido a um aumento no tempo de resposta do aplicativo back-end. Por outro lado, se o tempo de resposta do pico no *primeiro byte de back-end* estiver associado a um pico correspondente no *tempo de conexão de back-end*, ele poderá ser deduzido que a rede entre o gateway de aplicativo e o servidor de back-end ou a pilha TCP do servidor de back-end está saturada. 

Se você notar um pico no *tempo de resposta do último byte de back-end* , mas o *tempo de resposta do primeiro byte de back-end* for estável, ele poderá ser deduzido de que o pico é devido a um arquivo maior sendo solicitado.

Da mesma forma, se o *tempo total do gateway de aplicativo* tiver um pico, mas o *tempo de resposta do último byte do back-end* for estável, ele poderá ser um sinal de afunilamento de desempenho no gateway de aplicativo ou um afunilamento na rede entre o cliente e o gateway de aplicativo. Além disso, se o *RTT do cliente* também tiver um pico correspondente, isso indicará que a degradação é devido à rede entre o cliente e o gateway de aplicativo.

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo gateway de aplicativo dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pelo gateway de aplicativo para os clientes

- **Protocolo TLS do cliente**

   Contagem de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas para balancear a carga do tráfego. Há três determinantes para unidade de computação de unidade de capacidade, conexões persistentes e taxa de transferência. Cada unidade de capacidade é composta de no máximo: 1 unidade de computação ou 2500 conexões persistentes ou taxa de transferência de 2.22 Mbps.

- **Unidades de computação atuais**

   Contagem de capacidade de processador consumida. Entre os fatores que afetam a unidade de computação, estão as conexões TLS/s, as computações de reescrita de URL e o processamento de regra WAF. 

- **Conexões atuais**

   O número total de conexões simultâneas ativas de clientes para o gateway de aplicativo
   
- **Estimativa de unidades de capacidade cobradas**

  Com o SKU v2, o modelo de preços é orientado pelo consumo. As unidades de capacidade medem o custo baseado no consumo que é cobrado junto com o custo fixo. A *estimativa de unidades de capacidade cobrada* indica o número de unidades de capacidade que usam a cobrança estimada. Isso é calculado como o maior valor entre as *unidades de capacidade atuais* (unidades de capacidade necessárias para balancear a carga do tráfego) e as *unidades de capacidade cobráveis fixas* (unidades de capacidade mínimas mantidas provisionadas).

- **Solicitações com falha**

  Número de solicitações que o gateway de aplicativo serviu com códigos de erro do servidor 5xx. Isso inclui os códigos 5xx gerados a partir do gateway de aplicativo, bem como os códigos 5xx gerados a partir do back-end. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.
   
- **Unidades de capacidade cobráveis fixas**

  O número mínimo de unidades de capacidade mantido provisionado de acordo com a configuração de *unidades de escala mínimas* (uma instância representa dez unidades de capacidade) na configuração do Gateway de Aplicativo.
   
 - **Novas conexões por segundo**

   O número médio de novas conexões TCP por segundo estabelecidas de clientes para o gateway de aplicativo e do gateway de aplicativo para os membros de back-end.


- **Status da Resposta**

   Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

   Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

   Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Status da resposta de back-end**

  Contagem de códigos de status de resposta HTTP retornados pelos back-ends. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts não íntegros em um pool de back-end específico.
  
- **Solicitações por minuto por Host Íntegro**

  O número médio de solicitações recebidas por cada membro íntegro em um pool de back-end em um minuto. Você deve especificar o pool de back-end usando a dimensão *HttpSettings do backendpool* .  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Utilização da CPU**

  Exibe a utilização das CPUs alocadas para o gateway de aplicativo.  Sob condições normais, o uso da CPU não deveria exceder 90%, pois isso pode causar latência nos sites hospedados atrás do gateway de aplicativo e comprometer a experiência do cliente. É possível controlar ou aprimorar indiretamente a utilização da CPU ao alterar a configuração do gateway de aplicativo por meio do aumento da contagem de instâncias ou da mudança para um tamanho de SKU maior ou de ambos.

- **Conexões atuais**

  Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

  Número de solicitações que falharam devido a problemas de conexão. Essa contagem inclui solicitações que falharam devido à excedeção da configuração de HTTP "tempo limite de solicitação" e solicitações que falharam devido a problemas de conexão entre o gateway de aplicativo e o back-end. Essa contagem não inclui falhas porque nenhum back-end íntegro está disponível. as respostas 4xx e 5xx do back-end também não são consideradas como parte dessa métrica.

- **Status da Resposta**

  Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

  Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

  Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Contagem de Solicitações Bloqueadas do Firewall do Aplicativo Web**
- **Distribuição de solicitações bloqueadas do firewall do aplicativo Web**
- **Distribuição de Regra Total do Firewall do Aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar o número de hosts não íntegros em um pool de back-end específico.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue até um gateway de aplicativo, em **monitoramento** selecione **métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

Na imagem a seguir, você pode ver um exemplo com três métricas exibidas para os últimos 30 minutos:

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="Exibição de métrica." lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

Para ver uma lista atual de métricas, consulte [Métricas com suporte no Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta poderá chamar um webhook ou enviar um email para um administrador se a vazão de dados do gateway de aplicativo estiver acima, abaixo ou no limite durante um período especificado.

O seguinte exemplo orientará você pela criação de uma regra de alerta que envia um email para um administrador após um limite de vazão de dados ter sido violado:

1. Selecione **adicionar alerta de métrica** para abrir a página **Adicionar regra** . Você também pode acessar essa página na página métricas.

   ![Botão “Adicionar alerta de métrica”][6]

2. Na página **Adicionar regra** , preencha as seções nome, condição e notificar e selecione **OK**.

   * No seletor **Condição**, selecione um dos quatro valores: **Maior que**, **Maior ou igual a**, **Menor que** ou **Menor ou igual a**.

   * No seletor **Período**, selecione um período de cinco minutos a seis horas.

   * Se você selecionar **Proprietários, colaboradores e leitores de email**, o email poderá ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, você poderá fornecer uma lista separada por vírgula de usuários na caixa **Emails de administrador adicionais**.

   ![Página Adicionar regra][7]

Se o limite for violado, um email semelhante ao mostrado na seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida após a criação de um alerta de métrica. Ela fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [Receber notificações de alerta](../azure-monitor/alerts/alerts-overview.md).

Para entender mais sobre webhooks e como eles podem ser usados com alertas, consulte [Configurar um webhook em um alerta de métrica do Azure](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="next-steps"></a>Próximas etapas

* Visualize o contador e os logs de eventos com os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualize o log de atividades do Azure com](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) a postagem de blog Power bi.
* Postagem no blog [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Exibir e analisar os Logs de Atividades do Azure no Power BI e muito mais).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png