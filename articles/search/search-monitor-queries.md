---
title: Monitorar consultas
titleSuffix: Azure Cognitive Search
description: Monitore as métricas de consulta para desempenho e taxa de transferência. Coletar e analisar entradas de cadeia de caracteres de consulta nos logs de recursos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592390"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorar solicitações de consulta no Azure Pesquisa Cognitiva

Este artigo explica como medir o desempenho e o volume da consulta usando métricas e log de recursos. Ele também explica como coletar os termos de entrada usados em consultas – informações necessárias quando você precisa avaliar o utilitário e a eficácia do seu corpus de pesquisa.

Os dados históricos que alimentam as métricas são preservados por 30 dias. Para maior retenção ou para relatar dados operacionais e cadeias de consulta, certifique-se de habilitar uma [configuração de diagnóstico](search-monitor-logs.md) que especifique uma opção de armazenamento para manter métricas e eventos registrados em log.

As condições que maximizam a integridade da medição de dados incluem:

+ Use um serviço faturável (um serviço criado na camada básica ou Standard). O serviço gratuito é compartilhado por vários assinantes, o que introduz uma determinada quantidade de volatilidade como um deslocamento de carga.

+ Use uma única réplica e partição, se possível, para criar um ambiente independente e isolado. Se você usar várias réplicas, as métricas de consulta serão calculadas em média em vários nós, o que pode reduzir a precisão dos resultados. Da mesma forma, várias partições significam que os dados são divididos, com o potencial de que algumas partições podem ter dados diferentes se a indexação também estiver em andamento. Ao ajustar o desempenho da consulta, um único nó e partição fornece um ambiente mais estável para teste.

> [!Tip]
> Com o código adicional do lado do cliente e Application Insights, você também pode capturar dados de clickthrough para obter informações mais aprofundadas sobre o que atrai o interesse dos usuários do seu aplicativo. Para saber mais, confira [Análise de tráfego de pesquisa](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Volume de consulta (QPS)

O volume é medido como **consultas de pesquisa por segundo** (QPS), uma métrica interna que pode ser relatada como um valor médio, de contagem, mínimo ou máximo de consultas executadas em uma janela de um minuto. Os intervalos de um minuto (timegranular = "PT1M") para métricas são corrigidos no sistema.

É comum que as consultas sejam executadas em milissegundos, portanto, somente as consultas que medem como segundos aparecerão em métricas.

| Tipo de agregação | Descrição |
|------------------|-------------|
| Média | O número médio de segundos em um minuto durante o qual a execução da consulta ocorreu.|
| Contagem | O número de métricas emitidas para o log dentro do intervalo de um minuto. |
| Máximo | O número mais alto de consultas de pesquisa por segundo registradas durante um minuto. |
| Mínimo | O número mais baixo de consultas de pesquisa por segundo registradas durante um minuto.  |
| Soma | A soma de todas as consultas executadas dentro do minuto.  |

Por exemplo, em um minuto, você pode ter um padrão como este: um segundo de alta carga que é o máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga média e, finalmente, um segundo com apenas uma consulta, que é o mínimo.

Outro exemplo: se um nó emite 100 métricas, em que o valor de cada métrica é 40, "Count" é 100, "Sum" é 4000, "Average" é 40 e "Max" é 40.

## <a name="query-performance"></a>Desempenho de consulta

O desempenho de consulta em todo o serviço é medido como a latência de pesquisa (por quanto tempo uma consulta leva para ser concluída) e consultas limitadas que foram descartadas como resultado da contenção de recursos.

### <a name="search-latency"></a>Latência de pesquisa

| Tipo de agregação | Latency | 
|------------------|---------|
| Média | Duração média da consulta em milissegundos. | 
| Contagem | O número de métricas emitidas para o log dentro do intervalo de um minuto. |
| Máximo | Consulta de execução mais longa no exemplo. | 
| Mínimo | Consulta de execução mais curta no exemplo.  | 
| Total | Tempo de execução total de todas as consultas no exemplo, executando dentro do intervalo (um minuto).  |

Considere o seguinte exemplo de métricas de **latência de pesquisa** : 86 consultas foram amostradas, com uma duração média de 23,26 milissegundos. Um mínimo de 0 indica que algumas consultas foram descartadas. A consulta de execução mais longa levou 1000 milissegundos para ser concluída. O tempo de execução total foi de 2 segundos.

![Agregações de latência](./media/search-monitor-usage/metrics-latency.png "Agregações de latência")

### <a name="throttled-queries"></a>Consultas limitadas

Consultas limitadas referem-se a consultas que são descartadas em vez de processo. Na maioria dos casos, a limitação é uma parte normal da execução do serviço.  Não é necessariamente uma indicação de que há algo errado.

A limitação ocorre quando o número de solicitações atualmente processadas excede os recursos disponíveis. Você pode ver um aumento nas solicitações limitadas quando uma réplica é retirada da rotação ou durante a indexação. As solicitações de consulta e indexação são tratadas pelo mesmo conjunto de recursos.

O serviço determina se as solicitações devem ser descartadas com base no consumo de recursos. A porcentagem de recursos consumidos em memória, CPU e e/s de disco são calculadas por meio de um período de tempo. Se essa porcentagem exceder um limite, todas as solicitações para o índice serão limitadas até que o volume de solicitações seja reduzido. 

Dependendo do seu cliente, uma solicitação limitada pode ser indicada das seguintes maneiras:

+ Um serviço retorna um erro "você está enviando muitas solicitações. Tente novamente mais tarde.” 
+ Um serviço retorna um código de erro 503 indicando que o serviço está indisponível no momento. 
+ Se você estiver usando o portal (por exemplo, Gerenciador de pesquisa), a consulta será descartada silenciosamente e será necessário clicar em Pesquisar novamente.

Para confirmar as consultas limitadas, use a métrica de **consultas de pesquisa limitada** . Você pode explorar as métricas no portal ou criar uma métrica de alerta, conforme descrito neste artigo. Para consultas que foram descartadas dentro do intervalo de amostragem, use *total* para obter a porcentagem de consultas que não foram executadas.

| Tipo de agregação | Limitação |
|------------------|-----------|
| Média | Porcentagem de consultas descartadas dentro do intervalo. |
| Contagem | O número de métricas emitidas para o log dentro do intervalo de um minuto. |
| Máximo | Porcentagem de consultas descartadas dentro do intervalo.|
| Mínimo | Porcentagem de consultas descartadas dentro do intervalo. |
| Total | Porcentagem de consultas descartadas dentro do intervalo. |

Para **consultas de pesquisa limitadas percentual**, mínimo, máximo, média e total, todos têm o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, do número total de consultas de pesquisa durante um minuto.

Na captura de tela a seguir, o primeiro número é a contagem (ou o número de métricas enviadas ao log). Agregações adicionais, que aparecem na parte superior ou ao passar o mouse sobre a métrica, incluem média, máximo e total. Neste exemplo, nenhuma solicitação foi descartada.

![Agregações limitadas](./media/search-monitor-usage/metrics-throttle.png "Agregações limitadas")

## <a name="explore-metrics-in-the-portal"></a>Explorar métricas no portal

Para uma visão rápida dos números atuais, a guia **monitoramento** na página Visão geral do serviço mostra três métricas (**latência de pesquisa**, **consultas de pesquisa por segundo (por unidade de pesquisa)**, **percentual de consultas de pesquisa limitadas**) sobre os intervalos fixos medidos em horas, dias e semanas, com a opção de alterar o tipo de agregação.

Para uma exploração mais profunda, abra o Metrics Explorer no menu **monitoramento** para que você possa aplicar camadas, ampliar e Visualizar dados para explorar tendências ou anomalias. Saiba mais sobre o Metrics Explorer concluindo este [tutorial sobre como criar um gráfico de métricas](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. Na seção monitoramento, selecione **métricas** para abrir o Gerenciador de métricas com o escopo definido para o serviço de pesquisa.

1. Em métrica, escolha uma na lista suspensa e examine a lista de agregações disponíveis para um tipo preferencial. A agregação define como os valores coletados serão amostrados em cada intervalo de tempo.

   ![Métricas Explorer para métrica QPS](./media/search-monitor-usage/metrics-explorer-qps.png "Métricas Explorer para métrica QPS")

1. No canto superior direito, defina o intervalo de tempo.

1. Escolha uma visualização. O padrão é um gráfico de linhas.

1. Agregações adicionais de camada escolhendo **Adicionar métrica** e selecionando diferentes agregações.

1. Aplique zoom em uma área de interesse no gráfico de linhas. Coloque o ponteiro do mouse no início da área, clique com o botão esquerdo do mouse e mantenha pressionado, arraste para o outro lado da área e solte o botão. O gráfico será ampliado naquele intervalo de tempo.

## <a name="identify-strings-used-in-queries"></a>Identificar cadeias de caracteres usadas em consultas

Quando você habilita o log de recursos, o sistema captura solicitações de consulta na tabela **AzureDiagnostics** . Como pré-requisito, você já deve ter habilitado o [log de recursos](search-monitor-logs.md), especificando um espaço de trabalho do log Analytics ou outra opção de armazenamento.

1. Na seção monitoramento, selecione **logs** para abrir uma janela de consulta vazia no log Analytics.

1. Execute a expressão a seguir para pesquisar as operações Query. Search, retornando um conjunto de resultados tabulares que consistem no nome da operação, na cadeia de caracteres de consulta, no índice consultado e no número de documentos encontrados. As duas últimas instruções excluem cadeias de caracteres de consulta que consistem em uma pesquisa vazia ou não especificada, em um índice de exemplo, que reduz o ruído nos resultados.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcionalmente, defina um filtro de coluna em *Query_s* para pesquisar por uma sintaxe ou cadeia de caracteres específica. Por exemplo, você pode filtrar por *é igual a* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Cadeias de consulta registradas](./media/search-monitor-usage/log-query-strings.png "Cadeias de consulta registradas")

Embora essa técnica funcione para a investigação ad hoc, a criação de um relatório permite que você consolide e apresente as cadeias de caracteres de consulta em um layout mais adequado à análise.

## <a name="identify-long-running-queries"></a>Identificar consultas de execução longa

Adicione a coluna Duration para obter os números de todas as consultas, não apenas aquelas que são selecionadas como uma métrica. A classificação desses dados mostra quais consultas levam mais tempo para serem concluídas.

1. Na seção monitoramento, selecione **logs** para consultar informações de log.

1. Execute a consulta a seguir para retornar consultas, classificadas por duração em milissegundos. As consultas de execução mais longa estão na parte superior.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Classificar consultas por duração](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Classificar consultas por duração")

## <a name="create-a-metric-alert"></a>Criar um alerta de métrica

Um alerta de métrica estabelece um limite no qual você receberá uma notificação ou disparará uma ação corretiva que você definir antecipadamente. 

Para um serviço de pesquisa, é comum criar um alerta de métrica para latência de pesquisa e consultas limitadas. Se você souber quando as consultas são descartadas, poderá procurar soluções que reduzem a carga ou aumentam a capacidade. Por exemplo, se as consultas limitadas aumentarem durante a indexação, você poderá adiar isso até o sublado da atividade de consulta.

Ao enviar por push os limites de uma configuração de partição de réplica específica, a configuração de alertas para QPS (limites de volume de consulta) também é útil.

1. Na seção monitoramento, selecione **alertas** e clique em **+ nova regra de alerta**. Verifique se o serviço de pesquisa está selecionado como o recurso.

1. Em condição, clique em **Adicionar**.

1. Configure a lógica de sinal. Para tipo de sinal, escolha **métricas** e, em seguida, selecione o sinal.

1. Depois de selecionar o sinal, você pode usar um gráfico para visualizar os dados históricos para uma decisão informada sobre como prosseguir com a configuração de condições.

1. Em seguida, role para baixo até a lógica de alerta. Para prova de conceito, você pode especificar um valor artificialmente baixo para fins de teste.

   ![Lógica de alerta](./media/search-monitor-usage/alert-logic-qps.png "Lógica de alerta")

1. Em seguida, especifique ou crie um grupo de ação. Essa é a resposta a ser invocada quando o limite é atingido. Pode ser uma notificação por Push ou uma resposta automatizada.

1. Por fim, especifique os detalhes do alerta. Nomeie e descreva o alerta, atribua um valor de severidade e especifique se deseja criar a regra em um estado habilitado ou desabilitado.

   ![Detalhes do alerta](./media/search-monitor-usage/alert-details.png "Detalhes do alerta")

Se você especificou uma notificação por email, receberá um email de "Microsoft Azure" com uma linha de assunto de "Azure: severidade ativada: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, examine os conceitos básicos do monitoramento do serviço de pesquisa para saber mais sobre a gama completa de recursos de supervisão.

> [!div class="nextstepaction"]
> [Monitorar operações e atividades no Azure Pesquisa Cognitiva](search-monitor-usage.md)