---
title: "Visão Geral do Azure Monitor| Microsoft Docs"
description: "O Azure Monitor coleta estatísticas para uso em alertas, webhooks, dimensionamento automático e automação. O artigo também lista outras opções de monitoramento da Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: robb
ms.openlocfilehash: 5003ede9a40848db6905ef182da68577bca342cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-monitor"></a>Visão geral do Azure Monitor
Este artigo fornece uma visão geral do Serviço do Azure Monitor no Microsoft Azure. Ele aborda o que o Azure Monitor faz e fornece ponteiros para informações adicionais sobre como usar o Azure Monitor.  Se preferir uma introdução em vídeo, consulte os links em Próximas etapas no final deste artigo. 

## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure Monitor e outros produtos de monitoramento da Microsoft
O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Microsoft Azure. Os serviços do Azure que ainda não colocam seus dados no Azure Monitor o farão no futuro.

A Microsoft fornece produtos e serviços adicionais que oferecem mais recursos de monitoramento para desenvolvedores, DevOps ou operações de TI que também têm instalações locais. Para ter uma compreensão e visão geral de como esses diferentes produtos e serviços funcionam juntos, consulte [Monitoramento no Microsoft Azure](monitoring-overview.md).

## <a name="portal-overview-page"></a>Página de visão geral do portal

O Azure Monitor tem uma página de aterrissagem que ajuda os usuários a: 
- Compreender as funcionalidades de monitoramento oferecidas pelo Azure.
- Descobrir, configurar e integrar as funcionalidades de monitoramento premium de plataforma do Azure.

Embora o serviço Azure Monitor esteja liberado, a página de aterrissagem de visão geral está em versão prévia. 

A página é um ponto de partida para a navegação, incluindo a integração. Ela mostra problemas importantes selecionados de diferentes serviços e permite ao usuário navegar para eles no contexto.
 
![Modelo para o monitoramento e diagnóstico dos recursos não de computação](./media/monitoring-overview-azure-monitor/monitor-overview-ux2.png)

Ao abrir a página, selecione uma entre as assinaturas às quais você tem acesso de leitura. Para uma assinatura selecionada, você poderá ver:

- **Alertas disparados e fontes de alerta** – essa tabela mostra as contagens resumidas, as fontes de alerta e quantas vezes os alertas foram disparados na duração selecionada. Ela se aplica alertas de métricas e do log de atividades.
- **Erros do Log de Atividades** – se um dos recursos do Azure registrar eventos com uma gravidade em nível de erro, você poderá exibir uma contagem de alto nível e clicar para acessar a página do log de atividades para investigar cada evento.
- **Integridade do Serviço do Azure** – você pode ver uma contagem de problemas do serviço de Integridade do Serviço, eventos de manutenção planejada e avisos de integridade. A Integridade do Serviço do Azure fornece informações personalizadas quando problemas na infraestrutura do Azure afetam os serviços.  Consulte [Integridade do Serviço do Azure](../service-health/service-health-overview.md) para obter mais informações.  
- **Application Insights** – consulte os KPIs de cada recurso do AppInsights na assinatura atual. Os KPIs são otimizados para o monitoramento de aplicativos do lado do servidor em tipos de aplicativo ASP.NET, aplicativos Web, Java, Node e Geral. Os KPIs incluem métricas de taxa de solicitação, duração da resposta, taxa de falha e % de disponibilidade. 

Caso você não tenha feito a integração ao Log Analytics ou ao Application Insights ou, caso não tenha configurado nenhum Alerta do Azure na assinatura atual, a página fornecerá links para iniciar seu processo de integração.



## <a name="azure-monitor-sources---compute-subset"></a>Fontes do Azure Monitor – subconjunto de Computação

![Modelo para o monitoramento e diagnóstico dos recursos não de computação](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)

Os serviços de Computação listados aqui incluem 
- Serviços de Nuvem 
- Máquinas Virtuais 
- Conjuntos de escala de Máquina Virtual 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Aplicativo - Logs de Diagnóstico, Logs de Aplicativo e Métrica
Os aplicativos podem ser executados sobre o SO convidado no modelo de computação. Eles emitem seu próprio conjunto de métricas e logs. O Azure Monitor se baseia na extensão de diagnóstico do Azure (Windows ou Linux) para coletar a maioria dos logs e métricas de nível do aplicativo. Os tipos incluem

* Contadores de desempenho
* Logs de aplicativo
* Logs de Eventos do Windows
* Fonte de evento do .NET
* Logs IIS
* Manifesto com base no ETW
* Despejos de Falha
* Logs de Erro do Cliente

Sem a extensão de diagnóstico, somente algumas métricas, como o uso da CPU, estão disponíveis. 

### <a name="host-and-guest-vm-metrics"></a>Métricas de VM host e convidada
Os recursos de computação listados anteriormente têm uma VM host dedicada e SO convidado com que interagem. A VM host e o SO convidado são equivalentes à VM raiz e à VM convidada no modelo de hipervisor Hyper-V. Você pode coletar métricas sobre ambos. Você também pode coletar logs de diagnóstico sobre o SO convidado.   

### <a name="activity-log"></a>Log de Atividade
Você pode pesquisar o Log de atividade (anteriormente chamado de Logs de auditoria ou operacionais) para obter informações sobre o recurso como visto pela infraestrutura do Azure. O log contém informações como os horários quando os recursos são criados ou destruídos.  Para obter mais informações, consulte [Visão geral do log de atividades](monitoring-overview-activity-logs.md). 

## <a name="azure-monitor-sources---everything-else"></a>Fontes do Azure Monitor – todas as outras

![Modelo para o monitoramento e diagnóstico dos recursos de computação](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>Recursos - Métricas e Logs de Diagnóstico
As métricas e logs de diagnóstico que podem ser coletados variam com base no tipo de recurso. Por exemplo, os Aplicativos Web fornecem estatísticas sobre E/S de disco e Porcentagem da CPU. Essas métricas não existem para uma fila do Barramento de Serviço, que, em vez disso, fornece métricas como tamanho da fila e taxa de transferência de mensagem. Uma lista de métricas que podem ser coletadas para cada recurso está disponível em [métricas com suporte](monitoring-supported-metrics.md). 

### <a name="host-and-guest-vm-metrics"></a>Métricas de VM host e convidada
Não há necessariamente um mapeamento individual entre o recurso e uma determinada VM host ou convidada, portanto, as métricas não estão disponíveis.

### <a name="activity-log"></a>Log de Atividade
O log de atividades é igual ao dos recursos de computação.  

## <a name="uses-for-monitoring-data"></a>Usos dos Dados de Monitoramento
Depois de coletar os dados, você poderá fazer o seguinte com eles no Azure Monitor.

### <a name="route"></a>Rota
Você pode transmitir os dados de monitoramento para outros locais. 

Os exemplos incluem:

- Enviar para o Application Insights para que você possa usar ferramentas mais avançadas de visualização e análise.
- Enviar para os Hubs de Eventos para que você possa rotear para ferramentas de terceiros. 

### <a name="store-and-archive"></a>Armazenar e arquivar
Alguns dados de monitoramento já ficam armazenados e disponíveis no Azure Monitor por um período determinado. 
- As métricas são armazenadas por 30 dias. 
- As entradas do log de atividades são armazenadas por 90 dias. 
- Logs de diagnóstico não são armazenados. 

Se quiser armazenar dados por mais tempo que os períodos listados acima, você poderá usar um armazenamento do Azure. Os dados de monitoramento são mantidos em sua conta de armazenamento de acordo com uma política de retenção definida. Você precisa pagar pelo espaço os dados ocupam no armazenamento do Azure. 

Algumas formas de usar esses dados:

- Uma vez gravados, você pode ter outras ferramentas dentro ou fora do Azure para lê-los e processá-los.
- Você baixa os dados localmente para um arquivo local ou altera a política de retenção na nuvem para manter os dados por longos períodos de tempo.  
- Deixe os dados no armazenamento do Azure indefinidamente para fins de arquivamento. 

### <a name="query"></a>Consultar
Você pode usar a API REST do Azure Monitor, comandos de CLI (interface de linha de comando) entre plataformas, cmdlets do PowerShell ou o SDK do .NET para acessar os dados no sistema ou no armazenamento do Azure

Alguns exemplos incluem: 

* Obtendo dados para um aplicativo de monitoramento personalizado escrito por você
* Criando consultas personalizadas e enviando esses dados para um aplicativo de terceiros.

### <a name="visualize"></a>Visualizar
Visualizar os dados de monitoramento em gráficos ajuda a localizar tendências mais rapidamente do que examinar os dados em si.  

Alguns métodos de visualização incluem:

* Use o Portal do Azure
* Rotear os dados para Application Insights do Azure
* Rotear os dados para o Microsoft PowerBI
* Rotear os dados para uma ferramenta de visualização de terceiros usando a transmissão ao vivo ou com a ferramenta de leitura de um arquivo no armazenamento do Azure


### <a name="automate"></a>Automatizar
Você pode usar os dados de monitoramento para disparar alertas ou até processos inteiros. Os exemplos incluem:

* Use os dados para dimensionar automaticamente as instâncias de computação com base na carga do aplicativo.
* Envie emails quando uma métrica passar de um limite predeterminado.
* Chame uma URL da Web (webhook) para executar uma ação em um sistema fora do Azure
* Inicie um runbook na automação do Azure para executar quaisquer tarefas

## <a name="methods-of-accessing-azure-monitor"></a>Métodos para acessar o Azure Monitor
Em geral, você pode manipular o controle, roteamento e recuperação dos dados usando um dos métodos a seguir. Nem todos os métodos estão disponíveis para todas as ações ou tipos de dados.

* [Portal do Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [CLI (Interface de linha de comando) de plataforma cruzada](insights-cli-samples.md)
* [API REST](https://docs.microsoft.com/rest/api/monitor/)
* [SDK .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre
- Um passo a passo em vídeo do Azure Monitor está disponível em  
[Introdução ao Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). 
- Um vídeo que explica um cenário no qual você pode usar o Azure Monitor está disponível em [Explorar o monitoramento e diagnósticos do Microsoft Azure](https://channel9.msdn.com/events/Ignite/2016/BRK2234) e [Azure Monitor em um vídeo do Ignite 2016](https://myignite.microsoft.com/videos/4977).
- Percorra a interface do Azure Monitor na [Introdução ao Azure Monitor](monitoring-get-started.md)
- Configure as [Extensões de Diagnóstico do Azure](../azure-diagnostics.md) se você estiver tentando diagnosticar problemas em seu Serviço de Nuvem, Máquina Virtual, conjunto de dimensionamento de máquinas virtuais ou aplicativo do Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) se você tiver problemas de diagnóstico em seu aplicativo Web do Serviço de Aplicativo.
- [Solucionar de problemas no Armazenamento do Azure](../storage/common/storage-e2e-troubleshooting.md) ao usar os Blobs, Tabelas ou Filas de Armazenamento
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e [Operations Management Suite](https://www.microsoft.com/oms/)
