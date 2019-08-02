---
title: Detecção Inteligente - anomalias de desempenho | Microsoft Docs
description: O Application Insights executa uma análise inteligente da telemetria do seu aplicativo e o avisa de possíveis problemas. Esse recurso não precisa de nenhuma configuração.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.author: mbullwin
ms.openlocfilehash: 5ccff22a74b0cb1edcbae40fca087fe3197cb6ca
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867721"
---
# <a name="smart-detection---performance-anomalies"></a>Detecção Inteligente - anomalias de desempenho

O [Application Insights](../../azure-monitor/app/app-insights-overview.md) analisa automaticamente o desempenho do seu aplicativo Web e pode alertá-lo sobre problemas potenciais. Talvez você esteja lendo este artigo porque recebeu uma de nossas notificações de detecção inteligente.

Esse recurso não exige nenhuma configuração especial além de configurar seu aplicativo para o Application Insights (no [ASP.NET](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md) ou [Node.js](../../azure-monitor/app/nodejs.md) e no [código da página da Web](../../azure-monitor/app/javascript.md)). Ele fica ativo quando seu aplicativo gera telemetria suficiente.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Quando eu receberia uma notificação de detecção inteligente?

O Application Insights detectou que o desempenho do seu aplicativo apresentou uma degradação de uma das seguintes maneiras:

* **Degradação do tempo de resposta** – seu aplicativo começou a responder solicitações mais lentamente. A mudança pode ter sido rápida, por exemplo, porque havia uma regressão em sua implantação mais recente. Ou pode ter sido gradual, talvez causada por uma perda de memória. 
* **Degradação de duração da dependência** – seu aplicativo faz chamadas para uma API REST, banco de dados ou outra dependência. A dependência está respondendo mais lentamente do que antes.
* **Padrão de desempenho lento** – seu aplicativo parece ter um problema de desempenho que está afetando somente algumas solicitações. Por exemplo, as páginas estão sendo carregadas mais lentamente em um tipo de navegador do que em outros ou solicitações estão sendo atendidas de modo mais lento de um servidor específico. Atualmente, nossos algoritmos examinam os tempos de carregamento da página, os tempos de resposta de solicitação e os tempos de resposta de dependência.  

A Detecção Inteligente precisa de pelo menos oito dias de telemetria com um volume viável para estabelecer uma linha de base de desempenho normal. Portanto, após o aplicativo ser executado por esse período, qualquer problema significativo resultará em uma notificação.


## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?

Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. É simplesmente uma sugestão sobre algo que talvez você queira examinar mais detalhadamente.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?

As notificações incluem informações de diagnóstico. Veja um exemplo:


![Veja um exemplo de detecção de Degradação do tempo de resposta do servidor](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Triagem**. A notificação mostra quantos usuários ou quantas operações foram afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo**. O problema está afetando todo o tráfego ou apenas algumas páginas? Ele é restrito a navegadores ou locais específicos? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar**. Com frequência, as informações de diagnóstico na notificação sugerem a natureza do problema. Por exemplo, se o tempo de resposta fica mais lento quando a taxa de solicitação está alta, o que sugere que seu servidor ou suas dependências estão sobrecarregadas. 

    Caso contrário, abra a folha Desempenho no Application Insights. Nela, você encontrará dados do [Criador de Perfil](profiler.md). Se forem geradas exceções, você também poderá tentar usar o [depurador instantâneo](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configurar notificações por email

As notificações de detecção inteligente são habilitadas por padrão e enviadas aos que têm acesso de [monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [monitoramento de colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) para a assinatura na qual reside o recurso de Application insights. Para alterar isso, clique em **Configurar** na notificação por email ou abra as configurações de Detecção Inteligente no Application Insights. 
  
  ![Configurações de Detecção Inteligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Use o link **cancelar assinatura** no email de Detecção Inteligente para parar de receber as notificações por email.

Emails sobre anomalias de desempenho de Detecção Inteligente são limitados a um email por dia por recurso do Application Insights. O email será enviado somente se houver pelo menos um novo problema que foi detectado no dia em questão. Você não receberá nenhuma mensagem repetida. 

## <a name="faq"></a>Perguntas Frequentes

* *Portanto, a equipe da Microsoft examina os meus dados?*
  * Nº O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](../../azure-monitor/app/data-retention-privacy.md).
* *Todos os dados coletados pelo Application Insights são analisados?*
  * Não no momento. Atualmente, analisamos o tempo de resposta de solicitação, o tempo de resposta da dependência e o tempo de carregamento da página. A análise de métricas adicionais está em nossa lista de pendências para o futuro.

* Com que tipos de aplicativo ele funciona?
  * Esses degradações são detectadas em qualquer aplicativo que gera a telemetria apropriada. Se você tiver instalado o Application Insights em seu aplicativo Web, as solicitações e as dependências serão monitoradas automaticamente. Mas em serviços de back-end ou outros aplicativos, se você tiver inserido chamadas para [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), a Detecção Inteligente funcionará da mesma maneira.

* *Posso criar minhas próprias regras de detecção de anomalias ou personalizar regras existentes?*

  * Ainda não, mas você pode:
    * [Configurar alertas](../../azure-monitor/app/alerts.md) que informam quando uma métrica excede um limite.
    * [Exporte a telemetria](../../azure-monitor/app/export-telemetry.md) para um [banco de dados](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) ou para o [PowerBI](../../azure-monitor/app/export-power-bi.md ), em que você mesmo poderá analisá-la.
* *Com que frequência a análise é executada?*

  * Executamos diariamente a análise da telemetria do dia anterior (dia inteiro no fuso-horário UTC).
* *Então isso substitui os [alertas de métrica](../../azure-monitor/app/alerts.md)?*
  * Nº  Nosso compromisso não é detectar todos os comportamentos que você pode considerar anormais.


* *Se eu não fizer nada em resposta a uma notificação, receberei um lembrete?*
  * Não, você receberá uma mensagem sobre cada problema apenas uma vez. Se o problema persistir, ele será atualizado na folha de feed Detecção Inteligente.
* *Perdi o email. Onde posso encontrar as notificações no portal?*
  * Na visão geral do Application Insights do seu aplicativo, clique no bloco **Detecção Inteligente**. Nele, você poderá encontrar todas as notificações de até 90 dias anteriores.

## <a name="how-can-i-improve-performance"></a>Como posso melhorar o desempenho?
As respostas lentas e com falha são uma das maiores frustrações para os usuários do site, como você já deve saber por experiência própria. Dessa forma, é importante resolver os problemas.

### <a name="triage"></a>Triagem
Primeiro, isto é importante? Se uma página sempre for carregada lentamente, mas apenas 1% dos usuários do site tiverem de vê-la, talvez haja coisas mais importantes a considerar. Por outro lado, se apenas 1% dos usuários abrir essa página, mas ela lançar exceções todas as vezes, talvez valha a pena investigar.

Use a declaração de impacto (usuários afetados ou % de tráfego) como uma orientação geral, mas lembre-se de que isso não é tudo. Obtenha outras evidências para confirmar.

Considere os parâmetros do problema. Se for dependente da geografia, configure os [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) incluindo a região; talvez a área esteja simplesmente enfrentando problemas de rede.

### <a name="diagnose-slow-page-loads"></a>Diagnosticar carregamentos lentos de página
Onde está o problema? O servidor responde lentamente, a página é muito longa ou o navegador precisa trabalhar muito para exibi-la?

Abra a folha Métrica de navegadores. A exibição segmentada do tempo de carregamento da página do navegador mostra onde o tempo está sendo gasto. 

* Se o **Tempo de Envio da Solicitação** for alto, o servidor estará respondendo lentamente ou a solicitação será uma publicação com uma grande quantidade de dados. Examine as [métricas de desempenho](../../azure-monitor/app/web-monitor-performance.md#metrics) para investigar os tempos de resposta.
* Configure o [rastreamento de dependências](../../azure-monitor/app/asp-net-dependencies.md) para verificar se a lentidão ocorre devido a serviços externos ou ao seu banco de dados.
* Se **Recebendo Resposta** for predominante, sua página e seus componentes dependentes, JavaScript, CSS, imagens e assim por diante (mas não os dados carregados de forma assíncrona) serão longos. Configure um [teste de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)e não se esqueça de definir a opção para carregar os componentes dependentes. Ao obter alguns resultados, abra os detalhes de um resultado e amplie-os para ver os tempos de carregamento de arquivos diferentes.
* Um **Tempo de Processamento do Cliente** alto sugere que os scripts estão sendo executados lentamente. Se o motivo não for óbvio, considere a adição de um código de tempo e envie os tempos em chamadas trackMetric.

### <a name="improve-slow-pages"></a>Aprimorar páginas lentas
Há muitos conselhos na Web sobre como melhorar os tempos de resposta de seu servidor e de carregamento da página, portanto não os repetiremos aqui. Veja algumas dicas as quais você provavelmente já conhece, apenas para ajudá-lo a pensar:

* Carregamento lento devido a arquivos grandes: Carregue os scripts e outras partes de forma assíncrona. Use o agrupamento de script. Divida a página principal em widgets que carregam os dados separadamente. Não envie o antigo HTML simples para tabelas longas: use um script para solicitar os dados como JSON ou outro formato compacto e preencha a tabela. Há estruturas excelentes para ajudar você com tudo isso. (Que também envolvem scripts grandes, é claro).
* Dependências lentas do servidor: Considere as localizações geográficas dos componentes. Por exemplo, se você estiver usando o Azure, verifique se o servidor Web e o banco de dados estão na mesma região. As consultas recuperam mais informações do que o necessário? O armazenamento em cache ou o envio em lote ajudaria?
* Problemas de capacidade: Observe as métricas do servidor de tempos de resposta e contagens de solicitações. Se os tempos de resposta apresentarem picos desproporcionais, com picos nas contagens de solicitação, é provável que seus servidores estejam alongados.


## <a name="server-response-time-degradation"></a>Degradação do tempo de resposta do servidor

A notificação de degradação do tempo de resposta lhe informa:

* O tempo de resposta em comparação ao tempo de resposta normal para essa operação.
* Quantos usuários são afetados.
* O tempo médio de resposta e o tempo de resposta do 90º percentil para essa operação, no dia da detecção e sete dias antes. 
* Contagem de solicitações dessa operação no dia da detecção e sete dias antes.
* A correlação entre a degradação nessa operação e degradações em dependências relacionadas. 
* Links para ajudá-lo a diagnosticar o problema.
  * Rastreamentos do Criador de Perfil para ajudá-lo a exibir onde o tempo de operação é gasto (o link fica disponível se exemplos de rastreamento do Criador de Perfil tiverem sido coletados para essa operação durante o período de detecção). 
  * Relatórios de desempenho no Gerenciador de Métricas, nos quais você pode dividir e os filtros/intervalos de tempo para a operação.
  * Pesquise essa chamada para exibir propriedades de chamadas específicas.
  * Relatórios de falha – se a contagem for maior que um, houve falhas nessa operação que podem ter contribuído para a degradação do desempenho.

## <a name="dependency-duration-degradation"></a>Degradação da duração da dependência

Aplicativos modernos adotam cada vez mais a abordagem de design de microsserviços, que, em muitos casos, leva a uma grande dependência de serviços externos. Por exemplo, se seu aplicativo depender de uma plataforma de dados ou mesmo se você criar seu próprio serviço de bot, provavelmente você usará algum provedor de serviços cognitivos para habilitar seus bots a interagirem de maneiras mais humanas, além de um serviço de armazenamento de dados do qual o bot obterá as respostas.  

Exemplo de notificação de degradação de dependência:

![Veja um exemplo de detecção de Degradação da duração da dependência](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Observe o que ele diz:

* A duração em comparação ao tempo de resposta normal para essa operação
* Quantos usuários são afetados
* A duração média e a duração do 90º percentil para a dependência no dia da detecção e sete dias antes
* Número de chamadas de dependência no dia da detecção e sete dias antes
* Links para ajudá-lo a diagnosticar o problema
  * Relatórios de desempenho no Explorador de Métricas para essa dependência
  * Pesquise por chamadas dessa dependência para exibir propriedades das chamadas
  * Relatórios de falha - Se a contagem for > 1, isso significa que houve chamadas de dependência com falha durante o período de detecção que podem ter contribuído para a degradação da duração. 
  * Abra o Analytics com consultas que calculam a contagem e a duração dessa dependência  

## <a name="smart-detection-of-slow-performing-patterns"></a>Detecção Inteligente de padrões de desempenho lentos 

O Application Insights encontra problemas de desempenho que afetam apenas alguns dos seus usuários ou só afetam os usuários em alguns casos. Por exemplo, pode haver uma notificação de que o carregamento de páginas é mais lento em um tipo de navegador do que em outros ou de que solicitações são atendidas mais lentamente em um servidor específico. Ele também pode descobrir problemas associados com combinações de propriedades, como carregamentos de página lentos em uma área geográfica para clientes que usam um sistema operacional específico.  

Anomalias como essas são muito difíceis de detectar apenas inspecionando os dados, mas são mais comuns do que você imagina. Elas geralmente só surgem quando seus clientes reclamam. A essa altura, é tarde demais: os usuários afetados já estão migrando para seus concorrentes!

Atualmente, nossos algoritmos Examinam os tempos de carregamento de página, tempos de resposta de solicitação no servidor e os tempos de resposta de dependência.  

Você não precisa definir nenhum limite nem configurar regras. Machine learning e algoritmos de mineração de dados são usados para detectar padrões anormais.

![No alerta de email, clique no link para abrir o relatório de diagnóstico no Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** mostra a hora em que o problema foi detectado.
* **O que** descreve:

  * O problema que foi detectado;
  * As características do conjunto de eventos que encontramos que apresentaram o comportamento do problema.
* A tabela compara o conjunto com desempenho insatisfatório com o comportamento médio de todos os outros eventos.

Clique nos links para abrir o Metrics Explorer e a Pesquisa de relatórios relevantes, filtrados com base na hora e propriedades do conjunto de desempenho lento.

Modifique o intervalo de tempo e os filtros para explorar a telemetria.

## <a name="next-steps"></a>Próximas etapas
Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Criador de perfil](profiler.md) 
* [Depurador instantâneo](../../azure-monitor/app/snapshot-debugger.md)
* [Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Diagnóstico inteligente do Analytics](../../azure-monitor/app/analytics.md)

As detecções inteligentes são totalmente automáticas. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](../../azure-monitor/app/alerts.md)
* [Testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)
