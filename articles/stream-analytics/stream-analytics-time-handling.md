---
title: Noções básicas sobre a manipulação de horas no Azure Stream Analytics
description: Saiba mais sobre a manipulação de horas no Azure Stream Analytics
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 0eb4b77964aa3c07bac2af615a26c3a9199525de
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760850"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Noções básicas sobre a manipulação de horas no Azure Stream Analytics

Neste artigo, abordaremos como você pode fazer escolhas de design para resolver problemas de manipulação de horas práticas no serviço do Azure Stream Analytics. As decisões de design para manipulação de horas estão estritamente relacionadas aos fatores de ordenação de eventos.

## <a name="background-time-concepts"></a>Conceitos de tempo em segundo plano

Para um melhor quadro de discussão, vamos definir alguns conceitos informativos:

- **Hora do evento**: o momento em que o evento original ocorreu. Por exemplo, quando um carro em movimento na rodovia se aproxima de uma cabine de pedágio.

- **Tempo de processamento**: o momento em que o evento atinge o sistema de processamento e é observado. Por exemplo, quando o sensor de uma cabine de pedágio detecta o carro e o sistema do computador leva alguns segundos para processar os dados.

- **Marca-d'água**: Um marcador de tempo do evento que indica até que ponto os eventos tiverem sido inseridos para o processador de streaming. As marcas-d'água permitem que o sistema indique claramente o andamento da ingestão dos eventos. Pela natureza dos fluxos, os dados do evento de entrada nunca param, de modo que as marcas-d'água indicam o andamento até um determinado ponto no fluxo.

   O conceito de marca-d'água é importante. As marcas-d'água permitem que o Stream Analytics determine quando o sistema pode gerar resultados completos, corretos e repetíveis que não precisem ser retraídos. O processamento pode ser feito de forma garantida, além de ser previsível e repetível. Por exemplo, se uma recontagem precisa ser feita por alguma condição de tratamento de erro, as marcas-d'água são pontos seguros de partida e término.

Veja as postagens no blog de Tyler Akidau, [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102), como recursos adicionais sobre esse assunto.

## <a name="choosing-the-best-starting-time"></a>Escolhendo a melhor hora de início

O Stream Analytics fornece aos usuários duas opções para escolher a hora do evento:

1. **Hora de chegada**  

   A hora de chegada é atribuída na fonte de entrada, quando o evento chega à origem. É possível acessar o tempo de chegada usando a propriedade **EventEnqueuedUtcTime** para a entrada do hub de eventos e a propriedade**IoTHub.EnqueuedTime** property for IoT Hub, e usando o **BlobProperties.LastModified** para a entrada de blob.

   Usar a hora de chegada é o comportamento padrão, e mais útil para cenários de arquivamento de dados, onde não há necessidade de lógica temporal.

2. **Hora do aplicativo** (também chamado de Hora do Evento)

   A hora do aplicativo é atribuída quando o evento é gerado, e faz parte da carga do evento. Para processar eventos por hora do aplicativo, use a cláusula **Timestamp by** na consulta selecionada. Se a cláusula **Timestamp by** estiver ausente, os eventos serão processados na hora de chegada.

   É importante usar um carimbo de data/hora na carga quando a lógica temporal estiver envolvida. Dessa forma, os atrasos no sistema de origem ou na rede podem ser considerados.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Como o tempo progride no Azure Stream Analytics

Ao usar a hora do aplicativo, a progressão do tempo se baseia nos eventos de entrada. É difícil para o sistema de processamento de fluxo saber se não há eventos ou se eventos estão atrasados. Por esse motivo, o Azure Stream Analytics gera marcas-d'água heurísticas das seguintes maneiras para cada partição de entrada:

1. Sempre que houver qualquer evento de entrada, a marca-d'água será a hora do evento mais alta que veremos até o momento menos o tamanho da janela de tolerância fora de ordem.

2. Sempre que não houver evento de entrada, a marca-d'água será a hora de chegada atual estimada (o tempo decorrido em segundo plano da VM que está processando os eventos desde a última vez que um evento de entrada foi visto, mais a hora de chegada desse evento de entrada) menos a última janela de tolerância de chegada.

   A hora de chegada só pode ser estimada, pois a hora de chegada real é gerada no agente do evento de entrada, como Hubs de Eventos, e não na VM do Azure Stream Analytics que está processando os eventos.

O design tem duas finalidades adicionais, além de gerar as marcas-d'água:

1. O sistema gera resultados de forma oportuna, com ou sem os eventos de entrada.

   Você tem controle sobre a frequência com que eles querem ver os resultados gerados. No portal do Azure, na página **Ordenação de eventos** do seu trabalho do Stream Analytics, você pode definir a configuração **Eventos fora de ordem**. Ao definir essa configuração, considere a compensação entre pontualidade e tolerância dos eventos fora de ordem no fluxo de eventos.

   A janela de tolerância de chegada tardia é importante para continuar gerando marcas-d'água, mesmo na ausência de eventos de entrada. Às vezes, pode haver um período em que nenhum evento de entrada chega, por exemplo, quando o fluxo de entrada de um evento é esparso. Esse problema é exacerbado pelo uso de várias partições no agente de evento de entrada.

   Os sistemas de processamento de dados de streaming sem uma janela de tolerância de chegada tardia podem sofrer de saídas atrasadas quando entradas são esparsas e várias partições são usadas.

2. O comportamento do sistema deve ser repetível. A repetibilidade é uma propriedade importante de um sistema de processamento de dados de streaming.

   A marca-d'água é derivada da hora de chegada e da hora do aplicativo. Ambas são persistentes no agente do evento e, portanto, repetíveis. Caso a hora de chegada tenha que ser estimada na ausência de eventos, o Azure Stream Analytics lança a hora de chegada estimada para repetibilidade durante reprodução com a finalidade de recuperação de falhas.

Observe que quando você opta por usar a **hora de chegada** como a hora do evento, não há necessidade de configurar a tolerância fora de ordem nem a tolerância de chegada tardia. Uma vez que a **hora de chegada** tem a garantia de aumento contínuo no agente do evento de entrada, o Azure Stream Analytics simplesmente desconsidera as configurações.

## <a name="late-arriving-events"></a>Eventos de chegada tardia

Pela definição da janela de tolerância de chegada tardia, para cada evento de entrada, o Azure Stream Analytics compara a **hora do evento** com a **hora de chegada**; se a hora do evento estiver fora da janela de tolerância, você poderá configurar o sistema para descartar o evento ou ajustar a hora do evento para estar dentro da tolerância.

Considere que, depois que as marcas-d'água são geradas, o serviço pode potencialmente receber eventos com a hora do evento anterior ao da marca-d'água. Você pode configurar o serviço para **descartar** esses eventos ou **ajustar** a hora do evento para o valor da marca-d'água.

Como parte do ajuste, o **System.Timestamp** do evento é definido para o novo valor, mas o campo **hora do evento** em si não é alterado. Esse ajuste é a única situação em que o **System.Timestamp** do evento pode ser diferente do valor no campo de hora do evento, e pode fazer com que resultados inesperados sejam gerados.

## <a name="handling-time-variation-with-substreams"></a>Como lidar com a variação de tempo com subfluxos

O mecanismo heurístico de geração de marca-d'água descrito aqui funciona bem na maioria dos casos em que a hora é sincronizada na maioria das vezes entre os diversos remetentes de eventos. No entanto, na vida real, especialmente em muitos cenários de IoT, o sistema tem pouco controle sobre o relógio dos remetentes de eventos. Os remetentes de eventos podem estar em todos os tipos de dispositivo no campo, talvez em diferentes versões de hardware e software.

Em vez de usar uma marca d'água global para todos os eventos em uma partição de entrada, o Stream Analytics tem outro mecanismo chamado subfluxos para ajudar você. Você pode utilizar subfluxos em seu trabalho escrevendo uma consulta de trabalho que usa a cláusula [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) e a palavra-chave **OVER**. Para designar o subfluxo, forneça um nome de coluna de chave após a palavra-chave **OVER**, como `deviceid`, para que o sistema aplique as políticas de hora por essa coluna. Cada subfluxo obtém a sua própria marca-d'água independente. Esse mecanismo é útil para permitir a geração de saída em tempo hábil ao lidar com grandes desvios de relógio ou atrasos de rede entre remetentes de eventos.

Os subfluxos são uma solução exclusiva fornecida pelo Azure Stream Analytics e não são oferecidos por outros sistemas de processamento de dados de streaming. O Stream Analytics aplica a janela de tolerância de chegada tardia a eventos de entrada quando subfluxos são usados. A configuração padrão (5 segundos) provavelmente é muito pequena para dispositivos com carimbos de data/hora divergentes. É recomendável iniciar com 5 minutos e fazer ajustes de acordo com o padrão de desvio do relógio do dispositivo.

## <a name="early-arriving-events"></a>Eventos de chegada antecipada

Talvez você tenha notado outro conceito chamado janela de chegada antecipada, que parece o oposto da janela de tolerância de chegada tardia. Essa janela está fixada em 5 minutos e tem uma finalidade diferente daquela de chegada tardia.

Como o Azure Stream Analytics garante que ela sempre gere resultados completos, você pode especificar a **hora de início do trabalho** como a primeira hora de saída do trabalho, não a hora de entrada. A hora de início do trabalho é necessária para que a janela completa seja processada, não apenas metade da janela.

O Stream Analytics deriva a hora de início da especificação da consulta. No entanto, como o agente do evento de entrada é indexado apenas pela hora de chegada, o sistema precisa converter a hora de início do evento em hora de chegada. O sistema pode iniciar o processamento de eventos desse ponto no agente do evento de entrada. Com o limite da janela de chega antecipada, a conversão é direta. É a hora de início do evento menos os 5 minutos da janela de chegada antecipada. Esse cálculo também significa que o sistema descarta todos os eventos com a hora do evento 5 minutos posterior à hora de chegada.

Esse conceito é usado para garantir que o processamento seja repetível, independentemente de onde você começa a gerar a saída. Sem esse mecanismo, não seria possível garantir a repetibilidade, como muitos outros sistemas de streaming alegam que fazem.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efeitos colaterais das tolerâncias de tempo da ordenação de eventos

Os trabalhos do Stream Analytics têm várias opções de **Ordenação de eventos**. Duas podem ser configuradas no portal do Azure: a configuração **Eventos fora de ordem** (tolerância fora de ordem) e a configuração **Eventos que chegam com atraso** (tolerância de chegada tardia). A tolerância de **chegada antecipada** é fixa e não pode ser ajustada. Essas políticas de tempo são usadas pelo Stream Analytics para fornecer garantias robustas. No entanto, essas configurações às vezes têm algumas implicações inesperadas:

1. Envio acidental de eventos com muita antecedência.

   Eventos antecipados não devem ser enviados à saída normalmente. Entretanto, é possível que os eventos antecipados sejam enviados para a saída se o relógio do remetente estiver muito rápido. Todos os eventos de chegada antecipada são descartados, de modo que você não verá qualquer um deles na saída.

2. Envio de eventos antigos aos Hubs de Eventos para serem processados pelo Azure Stream Analytics.

   Embora os eventos antigos possam parecer inofensivos à primeira vista, por conta da aplicação da tolerância de chegada tardia, eles podem ser descartados. Se os eventos forem muito antigos, o valor de **System.Timestamp** será alterado durante a ingestão do evento. Devido a esse comportamento, o Azure Stream Analytics atualmente é mais adequado para cenários de processamento de eventos quase em tempo real do que cenários de processamento de eventos históricos. Você pode definir a hora dos **Eventos que chegam com atraso** para o maior valor possível (20 dias) para trabalhar em torno desse comportamento em alguns casos.

3. As saídas parecem estar atrasadas.

   A primeira marca-d'água é gerada na hora calculada: a **hora máxima do evento** que o sistema observou até então, menos o tamanho da janela de tolerância fora de ordem. Por padrão, a tolerância fora de ordem é configurada para zero (00 minutos e 00 segundos). Quando você a define para um número maior, valor temporal diferente de zero, a primeira saída do trabalho de streaming é atrasada de acordo com esse valor de tempo (ou maior) devido à primeira hora de marca-d'água que é calculada.

4. As entradas são esparsas.

   Quando não houver entrada em uma determinada partição, a hora de marca-d'água será calculada como a **hora de chegada** menos a janela de tolerância de chegada tardia. Como resultado, se os eventos de entrada forem infrequentes e esparsos, a saída poderá ser atrasada por esse período. O valor padrão dos **Eventos que chegam com atraso** é de 5 segundos. Você deve esperar ver algum atraso ao enviar eventos de entrada, um por vez, por exemplo. Os atrasos podem ficar piores quando você define a janela **Eventos que chegam com atraso** para um valor maior.

5. O valor de **System.Timestamp** é diferente da hora no campo de **hora do evento**.

   Conforme descrito anteriormente, o sistema ajusta a hora do evento pela tolerância fora de ordem ou pelas janelas de tolerância de chegada tardia. O valor de **System.Timestamp** do evento é ajustado, mas não o campo de **hora do evento**.

## <a name="metrics-to-observe"></a>Métricas a serem observadas

Você pode observar os vários efeitos da tolerância de tempo de ordenação de eventos por meio das [métricas de trabalho do Stream Analytics](stream-analytics-monitoring.md). As seguintes métricas são relevantes:

|Métrica  | DESCRIÇÃO  |
|---------|---------|
| **Eventos Fora de Ordem** | Indica o número de eventos recebidos fora de ordem, que foram descartados ou receberam um carimbo de data/hora ajustado. Essa métrica é afetada diretamente pela definição da configuração **Eventos fora de ordem** na página **Ordenação de eventos** do trabalho no portal do Azure. |
| **Eventos de Entrada Tardia** | Indica o número de eventos que chegam da fonte com atraso. Essa métrica inclui eventos que foram descartados ou tiveram seu carimbo de data/hora ajustado. Essa métrica é afetada diretamente pela definição da configuração **Eventos que chegam com atraso** na página **Ordenação de eventos** do trabalho no portal do Azure. |
| **Eventos de Entrada Antecipados** | Indica o número de eventos que chegam antecipadamente da fonte e que foram descartados ou cujo carimbo de data/hora foi ajustado, caso estejam com mais de 5 minutos de antecipação. |
| **Atraso de Marca-d'água** | Indica o atraso do trabalho de processamento de dados de streaming. Veja mais informações na seção a seguir.|

## <a name="watermark-delay-details"></a>Detalhes do Atraso de Marca-d'água

A métrica **Atraso de marca-d'água** é calculada como o tempo total do nó de processamento menos a maior marca-d'água já vista até então. Para saber mais, confira a [postagem no blog sobre o atraso de marca-d'água](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Pode haver vários motivos para que o valor dessa métrica seja maior que 0 em operações normais:

1. Atraso de processamento inerente do pipeline de streaming. Normalmente, esse atraso é nominal.

2. A janela de tolerância fora de ordem introduziu o atraso, pois a marca-d'água é reduzida pelo tamanho da janela de tolerância.

3. A janela de chegada tardia introduziu o atraso, pois a marca-d'água é reduzida pelo tamanho da janela de tolerância.

4. O desvio do relógio do nó de processamento está gerando a métrica.

Há inúmeras restrições de recursos que podem fazer com que o pipeline de streaming reduza a velocidade. A métrica de atraso de marca-d'água pode surgir devido a:

1. Falta de recursos de processamento no Stream Analytics para tratar de eventos de entrada. Para escalar verticalmente os recursos, confira [Noções básicas e ajuste das Unidades de Streaming](stream-analytics-streaming-unit-consumption.md).

2. Falta de taxa de transferência nos agentes do evento de entrada, de modo que ele são limitados. Para ver possíveis soluções, confira [Escalar verticalmente as unidade de produtividade dos Hubs de Eventos de maneira automática](../event-hubs/event-hubs-auto-inflate.md).

3. Os coletores de saída não são provisionados com capacidade suficiente, de modo que são limitados. As possíveis soluções variam amplamente de acordo com o tipo do serviço de saída que está sendo usado.

## <a name="output-event-frequency"></a>Frequência do evento de saída

O Azure Stream Analytics usa o progresso de marca-d'água como o gatilho somente para gerar eventos de saída. Como a marca-d'água é derivada dos dados de entrada, ela é repetível durante a recuperação de falha e também no reprocessamento iniciado pelo usuário.

Ao usar [agregações em janela](stream-analytics-window-functions.md), o serviço produz apenas saídas no final das janelas. Em alguns casos, os usuários talvez queiram ver agregações parciais geradas das janelas. As agregações parciais atualmente não têm suporte no Azure Stream Analytics.

Em outras soluções de streaming, os eventos de saída podem ser materializados em vários pontos de gatilho, dependendo das circunstâncias externas. É possível em algumas soluções que os eventos de saída para determinado período de tempo podem ser gerados várias vezes. Como os valores de entrada são refinados, os resultados agregados se tornam mais precisos. Os eventos poderiam ser especulados primeiro e revisados ao longo do tempo. Por exemplo, quando um determinado dispositivo estivesse offline da rede, um valor estimado poderia ser usado por um sistema. Mais tarde, o mesmo dispositivo ficaria online para a rede. Assim, os dados reais do evento poderiam ser incluídos no fluxo de entrada. Os resultados de processar essa janela de tempo produz uma saída mais precisa.

## <a name="illustrated-example-of-watermarks"></a>Exemplo ilustrado de marcas-d'água

As imagens a seguir ilustram o andamento das marcas-d'água em diferentes circunstâncias.

Esta tabela mostra os dados de exemplo que são colocados no gráfico abaixo. Observe que a hora do evento e a hora de chegada variam, às vezes correspondendo, às vezes, não.

| Hora do evento | Hora de chegada | deviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Nesta ilustração, as seguintes tolerâncias são usadas:

- A janelas de chegada antecipada é de 5 minutos
- A janelas de chegada tardia é de 5 minutos
- A janela de reordenação é de 2 minutos

1. Ilustração da marca-d'água progredindo por estes eventos:

   ![Ilustração da marca-d'água do Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processos importantes ilustrados no gráfico anterior:

   1. O primeiro evento (device1) e o segundo evento (device2) têm as horas alinhadas e são processados sem ajustes. A marca-d'água avança em cada evento.

   2. Quando o terceiro evento (device1) é processado, a hora de chegada (12:11) precede a hora do evento (12:17). O evento chegou 6 minutos mais cedo, de modo que o evento é descartado devido à tolerância de chegada antecipada de 5 minutos.

      A marca-d'água não avança nesse caso de um evento antecipado.

   3. O quarto evento (device3) e o quinto evento (device1) têm as horas alinhadas e são processados sem ajuste. A marca-d'água avança em cada evento.

   4. Quando o sexto evento (device3) é processado, a hora de chegada (12:17) e a hora do evento (12:12) estão abaixo do nível de marca-d'água. A hora do evento é ajustada para o nível da marca-d'água (12:17).

   5. Quando o nono evento (device3) é processado, a hora de chegada (12:27) está 6 minutos à frente da hora do evento (12:21). A política de chegada tardia é aplicada. A hora do evento é ajustada (12:22), que está acima da marca-d'água (12:21), de modo que nenhum ajuste é aplicado.

2. Segunda ilustração da marca-d'água progredindo sem uma política de chegada antecipada:

   ![Ilustração da marca-d'água sem política antecipada do Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-2.png)

   Neste exemplo, nenhuma política de chegada antecipada é aplicada. Os eventos de exceção que chegam antecipadamente elevam a marca-d'água consideravelmente. Observe que o terceiro evento (deviceId1 às 12:11) não é descartado nesse cenário, e a marca-d'água foi elevada para 12:15. Como resultado, a hora do quarto evento é ajustada 7 minutos para frente (12:08 para 12:15).

3. Na ilustração final, são usados subfluxos (SOBRE o DeviceId). Várias marcas d'água são controladas, uma por fluxo. Há menos eventos com suas horas de ajustada como resultado.

   ![Ilustração da marca-d'água de subfluxos do Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Próximas etapas

- [Considerações sobre a ordem dos eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Métricas de trabalho do Stream Analytics](stream-analytics-monitoring.md)
