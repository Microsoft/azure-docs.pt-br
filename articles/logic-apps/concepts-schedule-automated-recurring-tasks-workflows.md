---
title: Agendando tarefas e fluxos de trabalho recorrentes no aplicativo lógico do Azure
description: Uma visão geral sobre como agendar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7716c477cea2200e6fee901f7b5f63cd4b833bd7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232676"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure

Os aplicativos lógicos ajudam a criar e executar tarefas e processos recorrentes automatizados em um agendamento. Ao criar um fluxo de trabalho de aplicativo lógico que começa com um gatilho de recorrência interno ou um gatilho de janela deslizante, que são gatilhos de tipo agendado, você pode executar tarefas imediatamente, em um momento posterior ou em um intervalo recorrente. Você pode chamar serviços dentro e fora do Azure, como pontos de extremidade HTTP ou HTTPS, postar mensagens para serviços do Azure, como o armazenamento do Azure e o barramento de serviço do Azure, ou obter arquivos carregados em um compartilhamento de arquivos. Com o gatilho de recorrência, você também pode configurar agendas complexas e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações de agendamento internos, consulte [agendar gatilhos](#schedule-triggers) e [ações de agendamento](#schedule-actions). 

> [!TIP]
> Você pode agendar e executar cargas de trabalho recorrentes sem criar um aplicativo lógico separado para cada um dos trabalhos agendados e para a execução do [limite de fluxo de trabalho por região e assinatura](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Em vez disso, você pode usar o padrão de aplicativo lógico criado pelo [modelo de início rápido do Azure: Agendador de trabalhos dos](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)aplicativos lógicos.
>
> O modelo do Agendador de trabalhos dos aplicativos lógicos cria um aplicativo lógico CreateTimerJob que chama um aplicativo lógico TimerJob. Em seguida, você pode chamar o aplicativo lógico CreateTimerJob como uma API fazendo uma solicitação HTTP e passando uma agenda como entrada para a solicitação. Cada chamada para o aplicativo lógico CreateTimerJob também chama o aplicativo lógico TimerJob, que cria uma nova instância TimerJob que é executada continuamente com base na agenda especificada ou até atingir um limite especificado. Dessa forma, você pode executar tantas instâncias de TimerJob quanto desejar sem se preocupar com os limites de fluxo de trabalho porque as instâncias não são definições ou recursos de fluxo de trabalho de aplicativo lógico individual.

Esta lista mostra algumas tarefas de exemplo que você pode executar com os gatilhos internos de agendamento:

* Obter dados internos, como executar um procedimento armazenado SQL todos os dias.

* Obter dados externos, como os relatórios meteorológicos de pull do NOAA a cada 15 minutos.

* Envie dados de relatório, como enviar por email um resumo para todos os pedidos maiores que um valor específico na última semana.

* Processar dados, como compactar imagens carregadas hoje todos os dias da semana fora do horário de pico.

* Limpar dados, como excluir todos os tweets com mais de três meses.

* Arquive dados, como enviar faturas para um serviço de backup às 1:00, todos os dias para os próximos nove meses.

Você também pode usar as ações internas de agendamento para pausar o fluxo de trabalho antes da execução da próxima ação, por exemplo:

* Aguardar até um dia útil para enviar uma atualização de status por email.

* Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo para ser concluída antes da retomada e da recuperação do resultado.

Este artigo descreve os recursos para o agendamento de gatilhos e ações internas.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Agendar gatilhos

Você pode iniciar o fluxo de trabalho do aplicativo lógico usando o gatilho de recorrência ou o gatilho de janela deslizante, que não está associado a nenhum serviço ou sistema específico, por exemplo, Office 365 Outlook ou SQL Server. Esses gatilhos iniciam e executam o fluxo de trabalho com base na recorrência especificada em que você seleciona o intervalo e a frequência, como o número de segundos, minutos e horas para ambos os gatilhos, ou o número de dias, semanas ou meses para o gatilho de recorrência. Você também pode definir a data e a hora de início, bem como o fuso horário. Cada vez que um gatilho é acionado, os aplicativos lógicos criam e executam uma nova instância de fluxo de trabalho para seu aplicativo lógico.

Estas são as diferenças entre esses gatilhos:

* **Recorrência**: Executa o fluxo de trabalho em intervalos regulares de tempo com base em seu agendamento especificado. Se as recorrências forem perdidas, o gatilho de recorrência não processará as recorrências perdidas, mas reiniciará as recorrências com o próximo intervalo agendado. Você pode especificar uma data e hora de início, bem como o fuso horário. Se você selecionar "dia", poderá especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2:30. Se você selecionar "semana", também poderá selecionar dias da semana, como quarta-feira e sábado. Para obter mais informações, consulte [criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: Executa o fluxo de trabalho em intervalos de tempo regulares que manipulam dados em partes contínuas. Se as recorrências forem perdidas, o gatilho da janela deslizante voltará e processará as recorrências perdidas. Você pode especificar uma data e hora de início, fuso horário e uma duração para atrasar cada recorrência em seu fluxo de trabalho. Esse gatilho não tem opções para especificar dias, semanas e meses, horas do dia, minutos da hora e dias da semana. Para obter mais informações, consulte [criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Após qualquer ação no fluxo de trabalho do aplicativo lógico, você pode usar o atraso e o atraso até as ações para fazer seu fluxo de trabalho aguardar antes da execução da próxima ação.

* **Atraso**: Aguarde para executar a próxima ação para o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

* **Atraso até**: Aguarde para executar a próxima ação até a data e hora especificadas. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e hora de início e como o serviço de aplicativos lógicos executa essas recorrências:

| Hora de início | Recorrência sem agendamento | Recorrência com agendamento (somente gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base na última hora de execução. | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base no agendamento especificado. |
| Hora de início no passado | Gatilho de recorrência: Calcula os tempos de execução com base nos horários de início especificados e descarta as horas de execução anteriores. Executa a primeira carga de trabalho na próxima hora de execução no futuro. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. <p><p>Gatilho de **janela deslizante** : Calcula os tempos de execução com base na hora de início especificada e respeita os tempos de execução anteriores. <p>Executa cargas de trabalho futuras com base em cálculos da hora de início especificada. <p><p>Para obter mais explicações, consulte o exemplo após essa tabela. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
| Hora de início no presente ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
||||

*Exemplo de hora de início e recorrência após, mas sem agendamento*

Suponha que a data e a hora atuais sejam 8 de setembro de 2017 às 1:00. Você especifica a data e a hora de início como 7 de setembro de 2017 às 2:00, que está no passado e uma recorrência que é executada a cada dois dias.

| Hora de início | Hora atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** às 2:00 PM) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** às 1:00 PM) | A cada dois dias | {none} |
|||||

Para o gatilho de recorrência, o mecanismo de aplicativos lógicos calcula os tempos de execução com base na hora de início, descarta os tempos de execução anteriores, usa a próxima hora de início futura para a primeira execução e calcula as execuções futuras com base na hora da última execução.

Veja a aparência dessa recorrência:

| Hora de início | Primeira hora de execução | Horas de execução futuras |
|------------|----------------|------------------|
| 2017-09-**07** às 14h | 2017-09-**09** às 14h | 2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante... |
||||

Portanto, não importa o quanto antes você especifica a hora de início, por exemplo, 2017-09-**05** às 2:00 pm ou 2017-09-**01** às 2:00 PM, sua primeira execução sempre usará a próxima hora de início futura.

Para o gatilho de janela deslizante, o mecanismo de aplicativos lógicos calcula os tempos de execução com base na hora de início, honra os tempos de execução anteriores, usa a hora de início para a primeira execução e calcula as execuções futuras com base na hora de início.

Veja a aparência dessa recorrência:

| Hora de início | Primeira hora de execução | Horas de execução futuras |
|------------|----------------|------------------|
| 2017-09-**07** às 14h | 2017-09-**07** às 14h | 2017-09-**09** às 14h </br>2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante... |
||||

Portanto, não importa o quanto antes você especifica a hora de início, por exemplo, 2017-09-**05** às 2:00 pm ou 2017-09-**01** às 2:00 PM, sua primeira execução sempre usa a hora de início especificada.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exemplos de recorrências

Aqui estão várias recorrências de exemplo que você pode configurar para os gatilhos que dão suporte às opções:

| Disparador | Recorrência | Interval | Frequência | Hora de início | Nestes dias | A estas horas | Nestes minutos | Observação |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e, em seguida, calculará as recorrências futuras com base na última hora de execução. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, na hora cheia (com data e hora de início) | 1 | Hora | *startDate*Thh:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas a cada hora na marca de minuto "00", que é calculada a partir da hora de início. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e calculará as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos após a hora cheia, a cada hora (com data e hora de início) | 1 | Hora | *startDate*T00:15:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas na marca de "15" minutos, que é calculada a partir da hora de início, portanto às 00:15 A.M., 1:15 AM, 2:15 AM e assim por diante. |
| Recorrência | Executar a cada 15 minutos após a hora cheia, a cada hora (sem data e hora de início) | 1 | Day | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esse agendamento é executado às 00h15, 1h15, 2h15 e assim por diante. Além disso, esse agendamento é equivalente a uma frequência de "Hora" e uma hora de início com "15" minutos. |
| Recorrência | Executar a cada 15 minutos nas marcas de minuto especificadas (sem data e hora de início). | 1 | Day | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento não se inicia até a próxima marca de 15 minutos especificada. |
| Recorrência | Executar às 8h, todos os dias (sem data e hora de início) | 1 | Day | {none} | {não disponível} | 8 | {none} | Esse agendamento é executado às 8h, todos os dias, com base no agendamento especificado. |
| Recorrência | Executar às 8h, todos os dias (com data e hora de início) | 1 | Day | *startDate*T08:00:00Z | {não disponível} | {none} | {none} | Esse agendamento é executado às 8h, todos os dias, com base na hora de início especificada. | 
| Recorrência | Executar às 8h30, todos os dias (sem data e hora de início) | 1 | Day | {none} | {não disponível} | 8 | 30 | Esse agendamento é executado às 8h30, todos os dias, com base no agendamento especificado. |
| Recorrência | Executar às 8h30, todos os dias (com data e hora de início) | 1 | Day | *startDate*T08:30:00Z | {não disponível} | {none} | {none} | Esse agendamento se inicia na data de início especificada, às 8h30. |
| Recorrência | Executar às 8h30 e 16h30 todos os dias | 1 | Day | {none} | {não disponível} | 8, 16 | 30 | |
| Recorrência | Executar às 8h30, 8h45, 16h30 e 16h45 todos os dias | 1 | Day | {none} | {não disponível} | 8, 16 | 30, 45 | |
| Recorrência | Executar todos os sábados às 17h (sem data e hora de início) | 1 | Semana | {none} | "Sábado" | 17 | 00 | Esse agendamento é executado todos os sábados às 17h. |
| Recorrência | Executar todos os sábados às 17h (com data e hora de início) | 1 | Semana | *startDate*T17:00:00Z | "Sábado" | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas, nesse caso, 9 de setembro de 2017 às 17h. As recorrências futuras serão executadas todos os sábados às 17h. |
| Recorrência | Executar todas as terças e quintas-feiras às 17h | 1 | Semana | {none} | "Terça-feira", "Quinta-feira" | 17 | {none} | Esse agendamento é executado todas as terças-feiras e quintas-feiras às 17h. |
| Recorrência | Executado a cada hora durante o horário comercial | 1 | Semana | {none} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que você deseja. | Selecione os minutos da hora que você deseja. | Por exemplo, se o horário de trabalho é de 8h às 17h, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se o horário de trabalho é das 8h30 às 17h30, selecione as horas anteriores do dia e selecione também "30" como os minutos da hora. |
| Recorrência | Executar uma vez por dia aos finais de semana | 1 | Semana | {none} | "Sábado", "Domingo" | Selecione as horas do dia que você deseja. | Selecione os minutos da hora conforme apropriado. | Esse agendamento é executado aos sábados e domingos no agendamento especificado. |
| Recorrência | Executar a cada 15 minutos, quinzenalmente, somente às segundas-feiras | 2 | Semana | {none} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento é executado em todas as outras segundas-feiras em cada marca de 15 minutos. |
| Recorrência | Executar todos os meses | 1 | Mês | *startDate*T*startTime*Z | {não disponível} | {não disponível} | {não disponível} | Essa agenda não é iniciada *antes* da data e hora de início especificadas e calcula as recorrências futuras na data e hora de início. Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. |
| Recorrência | Executar a cada hora durante um dia por mês | 1 | Mês | {consulte a observação} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte a observação} | Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. Para controlar os minutos do agendamento da recorrência, especifique os minutos da hora, uma hora de início ou use o tempo de criação. Por exemplo, se a hora de início ou hora de criação for 8h25, esse agendamento será executado às 8h25, 9h25, 10h25 e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Executar apenas uma vez

Se você quiser executar seu aplicativo lógico somente de uma vez no futuro, poderá usar o **Agendador: executar trabalhos uma vez**. Depois de criar um novo aplicativo lógico, mas antes de abrir o designer de aplicativos lógicos, na seção **modelos** , na lista **categoria** , selecione **agenda**e, em seguida, selecione este modelo:

![Selecione "Agendador: executar trabalhos uma vez"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se você puder iniciar seu aplicativo lógico com o gatilho **quando uma solicitação HTTP é recebida-solicitação** e passar a hora de início como um parâmetro para o gatilho. Para a primeira ação, use a ação **atrasar até-agenda** e forneça o tempo para quando a próxima ação começar a ser executada.

## <a name="next-steps"></a>Próximas etapas

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md)
* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausar fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
