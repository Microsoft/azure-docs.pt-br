---
title: Métricas do Azure Spring Cloud
description: Saiba como examinar as métricas no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5a8d7d5906a769b778eb8f0ab5abe396a5f2e104
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877816"
---
# <a name="metrics-for-azure-spring-cloud"></a>Métricas do Azure Spring Cloud

O Azure Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar visualmente tendências e investigar picos e quedas em métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. 

No Azure Spring Cloud, há dois pontos de vista para métricas.
* Gráficos em cada página de visão geral do aplicativo
* Página métricas comuns

 ![Gráficos de métricas](media/metrics/metrics-1.png)

Os gráficos na **visão geral** do aplicativo fornecem verificações rápidas de status para cada aplicativo. A página **métricas** comuns contém todas as métricas disponíveis para referência. Você pode criar seus próprios gráficos na página métricas comuns e fixá-los no painel.

## <a name="application-overview-page"></a>Página Visão geral do aplicativo
Selecione um aplicativo em **aplicativos** para localizar gráficos na página Visão geral.  

 ![Gerenciamento de métricas do aplicativo](media/metrics/metrics-2.png)

A página **Visão Geral do Aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você realize uma rápida verificação de status do seu aplicativo.  

 ![Visão geral das métricas do aplicativo](media/metrics/metrics-3.png)

O Azure Spring Cloud fornece esses cinco gráficos com métricas que são atualizadas a cada minuto:

* **Erros do servidor http**: contagem de erros para solicitações HTTP para seu aplicativo
* **Dados em**: bytes recebidos por seu aplicativo
* **Saída de dados**: bytes enviados pelo seu aplicativo
* **Solicitações**: solicitações recebidas por seu aplicativo
* **Tempo médio de resposta**: tempo médio de resposta do seu aplicativo

Para o gráfico, você pode selecionar um intervalo de tempo de uma hora para sete dias.

## <a name="common-metrics-page"></a>Página métricas comuns

As **métricas** no painel de navegação à esquerda são vinculadas à página métricas comuns.

Primeiro, selecione as métricas a serem exibidas:

![Selecionar exibição de métrica](media/metrics/metrics-4.png)

Detalhes de todas as opções de métricas podem ser encontrados na [seção](#user-metrics-options) abaixo.

Em seguida, selecione tipo de agregação para cada métrica:

![Agregação de métrica](media/metrics/metrics-5.png)

O tipo de agregação indica como agregar pontos de métricas no gráfico por tempo. Há um ponto de métrica bruto a cada minuto, e o tipo de pré-autenticação em um minuto é predefinido pelo tipo de métricas.
* Sum: soma todos os valores como saída de destino.
* Média: Use o valor médio no período como saída de destino.
* Max/min: Use o valor max/min no período como saída de destino.

O intervalo de tempo também pode ser ajustado dos últimos 30 minutos para os últimos 30 dias ou um intervalo de tempo personalizado.

![Modificação de métrica](media/metrics/metrics-6.png)

A exibição padrão inclui todas as métricas de application's do serviço de nuvem do Spring do Azure juntas. As métricas de um aplicativo ou instância podem ser filtradas na exibição.  Clique em **Adicionar filtro**, defina a propriedade como **aplicativo** e selecione o aplicativo de destino que você deseja monitorar na caixa de texto **valores** . 

Você pode usar dois tipos de filtros (Propriedades):
* Aplicativo: filtrar por nome do aplicativo
* Instância: filtrar por instância de aplicativo

![Filtros de métrica](media/metrics/metrics-7.png)

Você também pode usar a opção **aplicar divisão** , que irá desenhar várias linhas para um aplicativo:

![Divisão de métrica](media/metrics/metrics-8.png)

>[!TIP]
> Você pode criar seus próprios gráficos na página métricas e fixá-los em seu **painel**. Comece nomeando seu gráfico.  Em seguida, selecione **Fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **Painel** do Portal.

## <a name="user-metrics-options"></a>Opções de métricas do usuário

As tabelas a seguir mostram as métricas e os detalhes disponíveis.

### <a name="error"></a>Erro
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat. global. Error | Tomcat. global. Error | Contagem | Número de erros que ocorreram em solicitações processadas |

### <a name="performance"></a>Desempenho
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| System. CPU. Usage | System. CPU. Usage | Porcentagem | Uso recente da CPU para todo o sistema (obsoleto e não sugira usá-lo). Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que todas as CPUs estavam ociosas durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam executando ativamente 100% do tempo durante o período recente observado.|
>| Process. CPU. Usage | Percentual de Uso de CPU do Aplicativo | Porcentagem | Uso recente da CPU para o processo de Máquina Virtual Java (obsoleto e não sugira usá-lo). Esse valor é um duplo no intervalo [0,0, 1,0]. Um valor de 0,0 significa que nenhuma das CPUs estavam executando threads do processo JVM durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam ativamente executando threads da JVM 100% do tempo durante o período recente observado. Os threads da JVM incluem os threads do aplicativo, bem como os threads internos da JVM.|
>| AppCpuUsage | Uso da CPU do aplicativo | Porcentagem | Uso recente de CPU do processo JVM em relação à CPU alocada para esse aplicativo, um valor de tipo duplo entre [0,0, 1,0]. Um valor de 0,0 significa que nenhuma das CPUs estavam executando threads do processo JVM durante o período de tempo recente observado, enquanto um valor de 1,0 significa que todas as CPUs estavam ativamente executando threads da JVM 100% do tempo durante o período recente observado. Os threads da JVM incluem os threads do aplicativo, bem como os threads internos da JVM.|
>| JVM. Memory. Committed | JVM. Memory. Committed | Bytes | Representa a quantidade de memória que tem a garantia de estar disponível para uso pela JVM. A JVM pode liberar memória para o sistema e confirmada pode ser menor que init. Commit sempre será maior ou igual a usado. |
>| JVM. Memory. Used | JVM. Memory. Used | Bytes | Representa a quantidade de memória usada atualmente em bytes. |
>| JVM. Memory. Max | JVM. Memory. Max | Bytes | Representa a quantidade máxima de memória que pode ser usada para gerenciamento de memória. A quantidade de memória usada e confirmada sempre será menor ou igual ao máximo se a opção máximo for definida. Uma alocação de memória pode falhar se tentar aumentar a memória usada, de modo que usada > confirmada mesmo se usado <= Max ainda seria verdadeiro (por exemplo, quando o sistema está com pouca memória virtual). |
>| JVM. GC. Max. Data. Size | JVM. GC. Max. Data. Size | Bytes | O pico de uso de memória do pool de memória de geração antiga desde que a máquina virtual Java foi iniciada. |
>| JVM. GC. Live. Data. Size | JVM. GC. Live. Data. Size | Bytes | Tamanho do pool de memória de geração antiga após um GC completo. |
>| JVM. GC. Memory. promovida | JVM. GC. Memory. promovida | Bytes | Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC. |
>| JVM. GC. Memory. allocated | JVM. GC. Memory. allocated | Bytes | Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo. |
>| JVM. GC. PAUSE. total. Count | JVM. GC. PAUSE (total-contagem) | Contagem | Contagem total de GC após esse JMV iniciado, incluindo GC jovem e antigo. |
>| JVM. GC. PAUSE. total. time | JVM. GC. PAUSE (tempo total) | Milissegundos | Tempo total de GC consumido após esse JMV iniciado, incluindo o GC jovem e antigo. |

### <a name="performance-net"></a>Desempenho (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|------|-----------------------------|------|---------|
>| Uso da CPU       | uso da CPU      | Porcentagem      | A porcentagem do uso de CPU do processo em relação a todos os recursos de CPU do sistema [0-100]. |
>| Conjunto de trabalho     | conjunto de trabalho    | Megabytes    | Quantidade de conjunto de trabalho usado pelo processo. |
>| Tamanho do heap do GC    | GC-heap-tamanho   | Megabytes    | Tamanho total do heap relatado pelo coletor de lixo. |
>| Contagem de GC de Gen 0  | Gen-0-GC-contagem | Contagem        | Número de coletas de lixo de geração 0 por segundo. |
>| Contagem de GC de Gen 1  | Gen-1-GC-contagem | Contagem        | Número de coletas de lixo de geração 1 por segundo. |
>| Contagem de GC de Gen 2  | Gen-2-GC-contagem | Contagem        | Número de coletas de lixo de geração 2 por segundo. |
>| Hora em GC      | time-GC      | Porcentagem      | A porcentagem de tempo na coleta de lixo desde a última coleta de lixo. |
>| Tamanho do heap de geração 0 | Ger-0-tamanho     | Bytes        | Tamanho da pilha de geração 0. |
>| Tamanho do heap de geração 1 | Ger-1-tamanho     | Bytes        | Tamanho de heap de geração 1. |
>| Tamanho do heap de geração 2 | Gen-2-tamanho     | Bytes        | Tamanho de heap de geração 2. |
>| Tamanho do heap de LOH   | Loh-tamanho       | Bytes        | Tamanho do heap de heap de objeto grande. |
>| Taxa de alocação | taxa de alocação     | Bytes        | Número de bytes alocados por segundo. |
>| Contagem de assembly  | assembly-contagem | Contagem        | Número de assemblies carregados. |
>| Contagem de exceção | contagem de exceção | Contagem       | Número de exceções por segundo. |
>| Contagem de threads do pool de threads      | ThreadPool-contagem de threads              | Contagem | Número de threads do pool de threads. |
>| Monitorar contagem de contenções de bloqueio | monitor-contagem de contenção de bloqueio        | Contagem | O número de vezes por segundo em que houve contenção ao tentar usar um bloqueio de monitor. |
>| Comprimento da fila do pool de threads      | ThreadPool-comprimento da fila              | Contagem | Comprimento da fila de itens de trabalho do pool de threads. |
>| Contagem de itens concluídos do pool de threads | ThreadPool-concluído-itens-contagem | Contagem | O pool de threads concluiu a contagem de itens de trabalho. |
>| Contagem de temporizadores ativos               | active-timer-Count               | Contagem | O número de temporizadores que estão ativos no momento. Um temporizador ativo é aquele registrado para tique em algum momento no futuro e ainda não foi cancelado. |

Para obter mais informações, consulte [dotnet Counters](/dotnet/core/diagnostics/dotnet-counters).

### <a name="request"></a>Solicitação
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat. global. sent | Tomcat. global. sent | Bytes | Quantidade de dados do servidor Web Tomcat enviados |
>| Tomcat. global. Received | Tomcat. global. Received | Bytes | Quantidade de dados do servidor Web Tomcat recebidos |
>| Tomcat. global. Request. total. Count | Tomcat. global. Request (total-contagem) | Contagem | Contagem total de solicitações processadas do servidor Web Tomcat |
>| Tomcat. global. Request. Max | Tomcat. global. Request. Max | Milissegundos | Tempo máximo do servidor Web Tomcat para processar uma solicitação |

### <a name="request-net"></a>Solicitação (.NET)

>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|------|-----------------------------|------|---------|
>| Solicitações por segundo | solicitações por segundo | Contagem | Taxa de solicitação. |
>| Total de solicitações | total-solicitações | Contagem | Número total de solicitações. |
>| Solicitações atuais | solicitações atuais | Contagem | Número de solicitações atuais. |
>| Solicitações com falha | solicitações com falha | Contagem | Número de solicitações com falha. |

Para obter mais informações, consulte [dotnet Counters](/dotnet/core/diagnostics/dotnet-counters).

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| Nome | Nome da métrica do atuador Spring | Unidade | Detalhes |
>|----|----|----|------------|
>| Tomcat. Sessions. Active. Max | Tomcat. Sessions. Active. Max | Contagem | Número máximo de sessões que estão ativas ao mesmo tempo |
>| Tomcat. Sessions. Alive. Max | Tomcat. Sessions. Alive. Max | Milissegundos | Tempo mais longo (em segundos) que uma sessão expirada esteve ativa |
>| Tomcat. Sessions. Created | Tomcat. Sessions. Created | Contagem | Número de sessões que foram criadas |
>| Tomcat. Sessions. Expired | Tomcat. Sessions. Expired | Contagem | Número de sessões que expiraram |
>| Tomcat. Sessions. Rejected | Tomcat. Sessions. Rejected | Contagem | Número de sessões que não foram criadas porque o número máximo de sessões ativas foi atingido. |
>| Tomcat. Sessions. Active. Current | Tomcat. Sessions. Active. Current | Contagem | Contagem ativa da sessão Tomcat |

## <a name="see-also"></a>Confira também

* [Início Rápido: Monitoramento de aplicativos do Azure Spring Cloud com logs, métricas e rastreamento](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Introdução ao Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md)

* [Analisar logs e métricas com configurações de diagnóstico](./diagnostic-services.md)

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: monitorar recursos de nuvem Spring usando alertas e grupos de ações](./spring-cloud-tutorial-alerts-action-groups.md)

* [Cotas e planos de serviço para o Azure Spring Cloud](./spring-cloud-quotas.md)
