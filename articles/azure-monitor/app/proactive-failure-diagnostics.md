---
title: Detecção Inteligente - anomalias de falha no Application Insights | Microsoft Docs
description: Alerta para alterações incomuns na taxa de solicitações com falha para seu aplicativo Web e fornece a análise de diagnóstico. Nenhuma configuração é necessária.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 46944603fdf45a2a7a14641086959bf61b3f773e
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465878"
---
# <a name="smart-detection---failure-anomalies"></a>Detecção Inteligente - anomalias de falha
O [Application Insights](../../azure-monitor/app/app-insights-overview.md) enviará uma notificação a você automaticamente, quase em tempo real, se seu aplicativo Web experimentar um aumento anormal de solicitações com falha. Ele detecta um aumento excepcional na taxa de solicitações de HTTP ou chamadas de dependência são relatadas como falha. Para solicitações, solicitações com falha geralmente são aqueles com códigos de resposta de 400 ou superior. Para ajudar você com a triagem e o diagnóstico do problema, a notificação acompanha uma análise das características das falhas e a telemetria relacionada. Também há links para portal do Application Insights, onde você pode obter um diagnóstico mais detalhado. O recurso não precisa de qualquer configuração, pois usa algoritmos de aprendizado de máquina para prever a taxa normal de falhas.

Esse recurso funciona para qualquer aplicativo de web, hospedado na nuvem ou em seus próprios servidores, que gere telemetria de solicitação ou de dependência - por exemplo, se você tiver uma função de trabalho que chama [trackrequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [trackdependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Depois de configurar o [Application Insights para seu projeto](../../azure-monitor/app/app-insights-overview.md), e desde que o aplicativo gere uma certa quantidade mínima de telemetria, a Detecção Inteligente de anomalias de falha leva 24 horas para compreender o comportamento normal do aplicativo antes que ele seja ligado e possa enviar alertas.

Veja a seguir um exemplo do alerta.

![Exemplo de alerta de detecção inteligente mostrando a análise de cluster sobre a falha](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Por padrão, você receberá uma mensagem com formato mais curto que esse exemplo. Mas você pode [alternar para este formato detalhado](#configure-alerts).
>
>

Observe o que ele diz:

* A taxa de falhas em comparação com o comportamento normal do aplicativo.
* Quantos usuários são afetados - então você sabe o quanto se preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta específico, nome da solicitação (operação) e versão do aplicativo. Isso diz a você imediatamente onde começar a procurar em seu código. Outras possibilidades poderiam ser um navegador ou um sistema operacional cliente específico.
* A exceção, os rastreamentos de log e as falhas de dependência (bancos de dados ou outros componentes externos) que parecem estar associados às falhas caracterizadas.
* Vincula diretamente às pesquisas relevantes na telemetria no Application Insights.

## <a name="failure-anomalies-v2"></a>V2 de anomalias de falha
Uma nova versão da regra de alerta de anomalias de falha agora está disponível. Essa nova versão está em execução na nova plataforma alerta do Azure e apresenta uma variedade de aperfeiçoamentos em relação à versão existente.

### <a name="whats-new-in-this-version"></a>O que há de novo nesta versão?
- Maior rapidez na detecção de problemas
- Um conjunto mais rico de ações – a regra de alerta é criada com um associado [grupo de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) denominado "Application Insights Smart Detection" que contém ações de email e webhook e pode ser estendido para disparar ações adicionais quando o alerta é acionado.
- Mais voltada para notificações: notificações de Email enviadas dessa regra de alerta agora são enviadas por padrão para os usuários associados a funções de leitor de monitoramento e colaborador de monitoramento da assinatura. Para obter mais informações sobre isso estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- A configuração mais fácil por meio de modelos ARM – veja o exemplo [aqui](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Notificações enviadas a partir dessa regra de alerta de suporte de alerta de esquema comuns – siga as [esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Unificação de modelo de email - notificações dessa regra de alerta tem uma aparência consistente e sinta-se com outros tipos de alerta de Email. Com essa alteração, a opção para obter alertas de anomalias de falha com informações detalhadas de diagnóstico não está mais disponível.

### <a name="how-do-i-get-the-new-version"></a>Como posso obter a nova versão?
- Recursos do Application Insights recém-criado agora estão provisionados com a nova versão da regra de alerta de anomalias de falha.
- Application Insights existente regra receberá a nova versão quando sua assinatura de hospedagem de recursos com a versão clássica de anomalias de falha de alerta é migrado para a nova plataforma alerta como parte do [processo de desativação de alertas clássicos ](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> A nova versão da regra de alerta de anomalias de falha permanece livre. Além disso, as ações de email e webhook disparado por associado "Detecção inteligente ao Application Insights" grupo de ações também são gratuitos.
> 
> 

## <a name="benefits-of-smart-detection"></a>Benefícios da Detecção Inteligente
Os [alertas de métrica](../../azure-monitor/app/alerts.md) comuns mostram que pode haver um problema. Mas a Detecção Inteligente inicia o trabalho de diagnóstico para você, executando grande parte da análise que, de outra forma, você teria de fazer por conta própria. Você obtém os resultados empacotados organizadamente, o que ajuda a chegar rapidamente à raiz do problema.

## <a name="how-it-works"></a>Como ele funciona
A Detecção Inteligente monitora a telemetria recebida de seu aplicativo, especialmente as taxas de falha. Essa regra calcula o número de solicitações para o qual o `Successful request` propriedade for false, e o número de dependência chamadas para o qual o `Successful call` propriedade é false. Para as solicitações, por padrão, `Successful request == (resultCode < 400)` (a menos que você tenha escrito o código personalizado para [filtrar](../../azure-monitor/app/api-filtering-sampling.md#filtering) ou gerar suas próprias chamadas [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)). 

O desempenho do seu aplicativo tem um padrão típico de comportamento. Algumas solicitações ou chamadas de dependência serão mais propensas a falhas do que outras; a taxa geral de falha poderá aumentar à medida que a carga crescer. A Detecção Inteligente usa aprendizado de máquina para encontrar essas anomalias.

Conforme a telemetria entra no Application Insights vinda de seu aplicativo Web, a Detecção Inteligente compara o comportamento atual com os padrões vistos nos últimos dias. Se for observado um aumento anormal na taxa de falha em comparação com o desempenho anterior, uma análise será disparada.

Quando um alerta é gerado, o serviço realiza uma análise de cluster na solicitação com falha a fim de tentar identificar um padrão de valores que caracterize as falhas. No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultado específico, nome de solicitação, host da URL do servidor e instância de função. Por outro lado, a análise descobriu que a propriedade do sistema operacional do cliente é distribuída por vários valores, e portanto não é listada.

Quando seu serviço conta com essas chamadas de telemetria, o analisador procura uma exceção e uma falha de dependência associadas às solicitações identificadas no cluster, junto com um exemplo de qualquer log de rastreamento associado a essas solicitações.

A análise resultante é enviada como um alerta, a menos que você tenha configurado para isso não acontecer.

Assim como os [alertas que você definiu manualmente](../../azure-monitor/app/alerts.md), é possível inspecionar o estado do alerta e configurá-lo na folha Alertas de seu recurso Application Insights. Mas, ao contrário de outros alertas, você não precisa configurar a Detecção Inteligente. Se quiser, você pode desabilitá-lo ou alterar o endereço de email de destino.

### <a name="alert-logic-details"></a>Detalhes da lógica de alerta

Os alertas são acionados por nosso algoritmo patenteado de machine learning, portanto, não podemos compartilhar os detalhes exatos da implementação. Dito isto, sabemos que muitas vezes é preciso saber mais sobre a forma como a lógica subjacente funciona. Os principais fatores avaliados para determinar se um alerta deve ser acionado são: 

* Análise da porcentagem da falha de solicitações/dependências em uma janela de tempo com acumulação de 20 minutos.
* Uma comparação da porcentagem de falha dos últimos 20 minutos em relação à taxa nos últimos 40 minutos e nos últimos sete dias e desvios significativos que excedam X vezes esse desvio padrão.
* O uso de um limite adaptável para a porcentagem de falha mínima, que varia com base no volume de solicitações/dependências do aplicativo.

## <a name="configure-alerts"></a>Configurar alertas
Você pode desabilitar a Detecção Inteligente, alterar os destinatários do email, criar um webhook ou aceitar mensagens de alerta mais detalhadas.

Abra a página Alertas. As Anomalias de Falha estão incluídas com todos os alertas que você configurou manualmente, e é possível ver se está em estado de alerta atualmente.

![Na página Visão geral, clique no bloco Alertas. Ou em qualquer página Métricas, clique no botão Alertas.](./media/proactive-failure-diagnostics/021.png)

Clique no alerta para configurá-lo.

![Configuração](./media/proactive-failure-diagnostics/032.png)

Observe que você pode desabilitar a Detecção Inteligente, mas não excluí-la (nem criar outra).

#### <a name="detailed-alerts"></a>Alertas detalhados
Se você selecionar "Obter diagnósticos mais detalhados", o email conterá mais informações de diagnóstico. Às vezes, você poderá diagnosticar o problema apenas dos dados no email.

Há um pequeno risco de que o alerta mais detalhado possa conter informações confidenciais, pois inclui mensagens de exceção e rastreamento. No entanto, isso aconteceria apenas se seu código permitisse informações confidenciais nessas mensagens.

## <a name="triaging-and-diagnosing-an-alert"></a>Triagem e diagnóstico de um alerta
Um alerta indica a detecção de um aumento anormal na taxa de solicitações com falha. É provável que haja algum problema com seu aplicativo ou seu ambiente.

Você pode decidir a urgência do problema com base na porcentagem de solicitações e no número de usuários afetados. No exemplo acima, a taxa de falha de 22,5% é comparada a uma taxa normal de 1% e indica que está acontecendo alguma coisa errada. Por outro lado, somente 11 usuários foram afetados. Se fosse seu aplicativo, você poderia avaliar a gravidade.

Em muitos casos, você poderá diagnosticar o problema rapidamente pelo nome da solicitação, exceção, falha de dependência e pelos dados de rastreamento fornecidos.

Há alguns outros indícios. Por exemplo, a taxa de falha de dependência neste exemplo é igual à taxa de exceção (89,3%). Isso sugere que a exceção provém diretamente da falha de dependência - dando uma ideia clara de onde é preciso começar a procurar em seu código.

Para investigar melhor, os links em cada seção levarão você diretamente até uma [página de pesquisa](../../azure-monitor/app/diagnostic-search.md) filtrada para mostrar solicitações, exceções, dependências ou rastreamentos relevantes. Você também pode abrir o [portal do Azure](https://portal.azure.com), navegar até o recurso Application Insights de seu aplicativo e abrir a folha Falhas.

Neste exemplo, clicando no link 'Exibir detalhes de falhas de dependência' abre a folha de pesquisa do Application Insights. Ele mostra a instrução SQL que tem um exemplo da causa raiz: Valores nulos foram fornecidos em campos obrigatórios e não passaram na validação durante a operação de salvamento.

![Pesquisa de diagnóstico](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Exame dos alertas recentes

Clique em **Detecção Inteligente** para obter o alerta mais recente:

![Resumo de alertas](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Qual é a diferença...
A Detecção Inteligente de anomalias de falha complementa outros recursos distintos, mas parecidos, do Application Insights.

* Os [Alertas de Métrica](../../azure-monitor/app/alerts.md) são definidos por você e podem monitorar uma ampla variedade de métricas, como a ocupação da CPU, as taxas de solicitação, os tempos de carregamento de página e assim por diante. Você pode usá-los para receber um aviso, por exemplo, se precisar adicionar mais recursos. Por outro lado, a Detecção Inteligente de anomalias de falha cobre um pequeno grupo de métricas essenciais (atualmente, apenas a taxa de solicitações com falha), projetadas para notificar você quase em tempo real quando a taxa de solicitações com falha de seu aplicativo Web aumentar consideravelmente, em comparação com o comportamento normal do aplicativo Web.

    A Detecção Inteligente ajusta automaticamente seu limite em resposta às condições predominantes.

    A Detecção Inteligente inicia o trabalho de diagnóstico para você.
* A [Detecção Inteligente de anomalias de desempenho](proactive-performance-diagnostics.md) também usa a inteligência de máquina para descobrir padrões incomuns em suas métricas, sem qualquer necessidade de configuração da sua parte. Mas diferentemente da Detecção Inteligente de anomalias de falha, a finalidade da Detecção Inteligente de anomalias de desempenho é encontrar segmentos de sua coleção de uso com mau serviço, por exemplo, por páginas específicas em um tipo específico de navegador. A análise é realizada diariamente e, se qualquer resultado for encontrado, provavelmente será muito menos urgente do que um alerta. Por outro lado, a análise de anomalias de falha é feita continuamente com a telemetria recebida, e você receberá uma notificação em questão de minutos se as taxas de falha do servidor forem maiores do que o esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se você receber um alerta de Detecção Inteligente
*Por que eu recebei esse alerta?*

* Foi detectado um aumento anormal de solicitações com falha em comparação à linha de base normal do período anterior. Após a análise das falhas e a telemetria associada, acreditamos que há um problema que você deve examinar.

*A notificação significa que, definitivamente, tenho um problema?*

* Tentamos alertar sobre a interrupção do aplicativo, ou sobre sua degradação, mas só você poderá compreender totalmente a semântica e o impacto no aplicativo ou nos usuários.

*Então, vocês examinam os meus dados?*

* Não. O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](../../azure-monitor/app/data-retention-privacy.md).

*É necessário assinar este alerta?*

* Não. Todos os aplicativos que enviam uma telemetria de solicitação têm a regra de alerta de Detecção Inteligente.

*Posso cancelar a assinatura ou ter as notificações enviadas para meus colegas em vez disso?*

* Sim, em Regras de alerta, clique na regra de Detecção Inteligente para configurá-la. Você pode desabilitar o alerta ou alterar os destinatários do alerta.

*Perdi o email. Onde posso encontrar as notificações no portal?*

* Nos Logs de atividade. No Azure, abra o recurso Application Insights de seu aplicativo e selecione Logs de atividade.

*Alguns dos alertas são sobre problemas conhecidos e não quero recebê-los.*

* Nós temos a supressão de alerta em nossa lista de pendências.

## <a name="next-steps"></a>Próximas etapas
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Metrics explorer](../../azure-monitor/app/metrics-explorer.md)
* [Gerenciador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../../azure-monitor/log-query/get-started-portal.md)

As detecções inteligentes são totalmente automáticas. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)
