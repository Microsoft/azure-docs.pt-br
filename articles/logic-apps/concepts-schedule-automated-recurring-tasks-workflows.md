---
title: Agendando tarefas e fluxos de trabalho recorrentes no aplicativo lógico do Azure
description: Uma visão geral sobre como agendar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: e9fbafa9f3c33d10496e84f61e1f2b97f6328d3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100581815"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas automatizadas, processos e fluxos de trabalho recorrentes com Aplicativos Lógicos do Azure

Os aplicativos lógicos ajudam a criar e executar tarefas e processos recorrentes automatizados em um agendamento. Ao criar um fluxo de trabalho de aplicativo lógico que começa com um gatilho de recorrência interno ou um gatilho de janela deslizante, que são gatilhos de tipo agendado, você pode executar tarefas imediatamente, em um momento posterior ou em um intervalo recorrente. Você pode chamar serviços dentro e fora do Azure, como pontos de extremidade HTTP ou HTTPS, postar mensagens para serviços do Azure, como o armazenamento do Azure e o barramento de serviço do Azure, ou obter arquivos carregados em um compartilhamento de arquivos. Com o gatilho de recorrência, você também pode configurar agendas complexas e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações de agendamento internos, consulte [agendar gatilhos](#schedule-triggers) e [ações de agendamento](#schedule-actions). 

> [!TIP]
> Você pode agendar e executar cargas de trabalho recorrentes sem criar um aplicativo lógico separado para cada um dos trabalhos agendados e para a execução do [limite de fluxo de trabalho por região e assinatura](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Em vez disso, você pode usar o padrão de aplicativo lógico que é criado pelo [modelo de início rápido do Azure: Agendador de trabalhos de aplicativos lógicos](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
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

Você pode iniciar o fluxo de trabalho do aplicativo lógico usando o gatilho de recorrência ou o gatilho de janela deslizante, que não está associado a nenhum serviço ou sistema específico. Esses gatilhos iniciam e executam o fluxo de trabalho com base na recorrência especificada em que você seleciona o intervalo e a frequência, como o número de segundos, minutos, horas, dias, semanas ou meses. Você também pode definir a data e a hora de início junto com o fuso horário. Cada vez que um gatilho é acionado, os aplicativos lógicos criam e executam uma nova instância de fluxo de trabalho para seu aplicativo lógico.

Estas são as diferenças entre esses gatilhos:

* **Recorrência**: executa o fluxo de trabalho em intervalos de tempo regulares com base no agendamento especificado. Se o gatilho errar recorrências, por exemplo, devido a interrupções ou fluxos de trabalho desabilitados, o gatilho de recorrência não processará as recorrências perdidas, mas reiniciará as recorrências com o próximo intervalo agendado.

  Se você selecionar **dia** como a frequência, poderá especificar as horas do dia e os minutos da hora, por exemplo, todos os dias às 2:30. Se você selecionar **semana** como a frequência, também poderá selecionar dias da semana, como quarta e sábado. Você também pode especificar uma data e hora de início junto com um fuso horário para sua agenda de recorrência.

  > [!TIP]
  > Se uma recorrência não especificar uma [data e hora de início](#start-time)específicas, a primeira recorrência será executada imediatamente quando você salvar ou implantar o aplicativo lógico, apesar da configuração de recorrência do gatilho. Para evitar esse comportamento, forneça uma data e hora de início para quando desejar que a primeira recorrência seja executada.
  >
  > Se uma recorrência não especificar outras opções avançadas de agendamento, como horários específicos para executar recorrências futuras, essas recorrências serão baseadas na última hora de execução. Como resultado, as horas de início para essas recorrências podem ser desligadas devido a fatores como latência durante chamadas de armazenamento. Para certificar-se de que seu aplicativo lógico não perca uma recorrência, especialmente quando a frequência estiver em dias ou mais, tente estas opções:
  >
  > * Forneça uma data e hora de início para a recorrência mais as vezes específicas quando executar recorrências subsequentes usando as propriedades nomeadas nessas **horas** e a **esses minutos**, que estão disponíveis apenas para as frequências de **dia** e **semana** .
  >
  > * Use o [gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

  Para obter mais informações, consulte [criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: executa o fluxo de trabalho em intervalos de tempo regulares que manipulam dados em partes contínuas. Se o gatilho errar recorrências, por exemplo, devido a interrupções ou fluxos de trabalho desabilitados, o gatilho de janela deslizante voltará e processará as recorrências perdidas.

  Você pode especificar uma data e hora de início, fuso horário e uma duração para atrasar cada recorrência em seu fluxo de trabalho. Esse gatilho não dá suporte a agendamentos avançados, por exemplo, horas específicas do dia, minutos da hora e dias da semana. Para obter mais informações, consulte [criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Após qualquer ação no fluxo de trabalho do aplicativo lógico, você pode usar o atraso e o atraso até as ações para fazer seu fluxo de trabalho aguardar antes da execução da próxima ação.

* **Atraso**: Aguarde para executar a próxima ação para o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

* **Atraso até**: aguardar para executar a próxima ação até a data e a hora especificadas. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

<a name="start-time"></a>

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

Aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e hora de início e como o serviço de aplicativos lógicos executa essas recorrências:

| Hora de início | Recorrência sem agendamento | Recorrência com agendamento (somente gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base na última hora de execução. | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base no agendamento especificado. |
| Hora de início no passado | Gatilho de **recorrência** : calcula os tempos de execução com base na hora de início especificada e descarta os tempos de execução anteriores. <p><p>Executa a primeira carga de trabalho na próxima hora de execução no futuro. <p><p>Executa futuras cargas de trabalho com base na última hora de execução. <p><p>Gatilho de **janela deslizante** : calcula os tempos de execução com base na hora de início especificada e respeita os tempos de execução anteriores. <p><p>Executa cargas de trabalho futuras com base na hora de início especificada. <p><p>Para obter mais explicações, consulte o exemplo após essa tabela. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p><p>Executa futuras cargas de trabalho com base no agendamento especificado. <p><p>**Observação:** Se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para o agendamento, os aplicativos lógicos calcularão os tempos de execução futuros usando as horas ou os minutos, respectivamente, do primeiro tempo de execução. |
| Hora de início agora ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p><p>Gatilho de **recorrência** : executa cargas de trabalho futuras com base na última hora de execução. <p><p>Gatilho de **janela deslizante** : executa cargas de trabalho futuras com base na hora de início especificada. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p><p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** Se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para o agendamento, os aplicativos lógicos calcularão os tempos de execução futuros usando as horas ou os minutos, respectivamente, do primeiro tempo de execução. |
||||

*Exemplo de hora de início e recorrência após, mas sem agendamento*

Suponha que a data e a hora atuais sejam 8 de setembro de 2017 às 1:00. Você especifica a data e a hora de início como 7 de setembro de 2017 às 2:00, que está no passado e uma recorrência que é executada a cada dois dias.

| Hora de início | Hora atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09-**07** às 2:00 PM) | 2017-09-**08** T13:00:00Z <br>(2017-09-**08** às 1:00 PM) | A cada dois dias | {none} |
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
| 2017-09-**07** às 14h | 2017-09 –**08** às 1:00 PM (hora atual) | 2017-09-**09** às 14h </br>2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante... |
||||

Portanto, não importa o quanto antes você especifica a hora de início, por exemplo, 2017-09-**05** às 2:00 pm ou 2017-09-**01** às 2:00 PM, sua primeira execução sempre usa a hora de início especificada.

<a name="daylight-saving-standard-time"></a>

## <a name="recurrence-for-daylight-saving-time-and-standard-time"></a>Recorrência para horário de verão e horário padrão

Os gatilhos internos recorrentes respeitam o agendamento que você definiu, incluindo qualquer fuso horário que você especificar. Se você não selecionar um fuso horário, o horário de Verão poderá afetar quando os gatilhos forem executados, por exemplo, deslocando a hora de início uma hora para a frente quando o horário de verão for iniciado e uma hora para trás quando o horário de verão terminar. Ao agendar trabalhos, os aplicativos lógicos colocam a mensagem para processamento na fila e especifica quando essa mensagem se torna disponível, com base na hora UTC em que o último trabalho foi executado e na hora UTC em que o trabalho seguinte está agendado para ser executado.

Para evitar essa mudança para que seu aplicativo lógico seja executado na hora de início especificada, certifique-se de selecionar um fuso horário. Dessa forma, a hora UTC para seu aplicativo lógico também muda para combater a alteração de horário sazonal.

<a name="dst-window"></a>

> [!NOTE]
> Os gatilhos que começam entre 2:00 AM-3:00 podem ter problemas porque as alterações de horário de verão acontecem às 2:00 AM, o que pode fazer com que a hora de início se torne inválida ou ambígua. Se você tiver vários aplicativos lógicos dentro do mesmo intervalo ambíguo, eles poderão se sobrepor. Por esse motivo, talvez você queira evitar horas de início entre 2:00 AM-3:00 AM.

Por exemplo, suponha que você tenha dois aplicativos lógicos que são executados diariamente. Um aplicativo lógico é executado às 1:30, hora local, enquanto o outro é executado uma hora depois às 2:30, hora local. O que acontece com os horários de início para esses aplicativos quando o horário de verão é iniciado e encerrado?

* Os gatilhos são executados quando o tempo alterna uma hora para frente?

* Os gatilhos são executados duas vezes quando o tempo alterna uma hora para trás?

Se esses aplicativos lógicos usarem a zona UTC-6:00 hora central (EUA & Canadá), essa simulação mostrará como as horas UTC mudaram em 2019 para combater as alterações de horário de verão, movendo uma hora para trás ou para frente, conforme necessário, para que os aplicativos continuem em execução nos horários locais esperados sem execuções ignoradas ou duplicadas.

* **03/10/2019: o DST começa às 2:00, horário de deslocamento uma hora para frente**

  Para compensar após a inicialização do DST, a hora UTC muda uma hora para trás para que seu aplicativo lógico continue em execução na mesma hora local:

  * #1 de aplicativo lógico

    | Data | Hora (local) | Hora (UTC) | Observações |
    |------|--------------|------------|-------|
    | 03/09/2019 | 1:30:00 AM | 7:30:00 AM | UTC antes do dia em que o DST entra em vigor. |
    | 03/10/2019 | 1:30:00 AM | 7:30:00 AM | O UTC é o mesmo porque o DST não teve efeito. |
    | 03/11/2019 | 1:30:00 AM | 6:30:00 AM | O UTC mudou uma hora para trás depois que o DST entrar em vigor. |
    |||||

  * #2 de aplicativo lógico

    | Data | Hora (local) | Hora (UTC) | Observações |
    |------|--------------|------------|-------|
    | 03/09/2019 | 2:30:00 AM | 8:30:00 AM | UTC antes do dia em que o DST entra em vigor. |
    | 03/10/2019 | 3:30:00 AM * | 8:30:00 AM | O horário de Verão já está em vigor, portanto, a hora local mudou uma hora para frente porque o fuso horário UTC-6:00 é alterado para UTC-5:00. Para obter mais informações, consulte [gatilhos que começam entre 2:00 am-3:00 am](#dst-window). |
    | 03/11/2019 | 2:30:00 AM | 7:30:00 AM | O UTC mudou uma hora para trás depois que o DST entrar em vigor. |
    |||||

* **11/03/2019: o horário de verão termina às 2:00 e muda a hora uma hora para trás**

  Para compensar, a hora UTC muda uma hora para a frente para que seu aplicativo lógico continue em execução na mesma hora local:

  * #1 de aplicativo lógico

    | Data | Hora (local) | Hora (UTC) | Observações |
    |------|--------------|------------|-------|
    | 11/02/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/03/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/04/2019 | 1:30:00 AM | 7:30:00 AM ||
    |||||

  * #2 de aplicativo lógico

    | Data | Hora (local) | Hora (UTC) | Observações |
    |------|--------------|------------|-------|
    | 11/02/2019 | 2:30:00 AM | 7:30:00 AM ||
    | 11/03/2019 | 2:30:00 AM | 8:30:00 AM ||
    | 11/04/2019 | 2:30:00 AM | 8:30:00 AM ||
    |||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Executar apenas uma vez

Se você quiser executar seu aplicativo lógico somente de uma vez no futuro, poderá usar o modelo **Agendador: executar uma vez para trabalhos** . Depois de criar um novo aplicativo lógico, mas antes de abrir o designer de aplicativos lógicos, na seção **modelos** , na lista **categoria** , selecione **agenda** e, em seguida, selecione este modelo:

![Selecione o modelo "Agendador: executar após trabalhos"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se você puder iniciar seu aplicativo lógico com o gatilho **quando uma solicitação HTTP é recebida-solicitação** e passar a hora de início como um parâmetro para o gatilho. Para a primeira ação, use a ação **atrasar até-agenda** e forneça o tempo para quando a próxima ação começar a ser executada.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exemplos de recorrências

Aqui estão várias recorrências de exemplo que você pode configurar para os gatilhos que dão suporte às opções:

| Gatilho | Recorrência | Intervalo | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Observação |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e, em seguida, calculará as recorrências futuras com base na última hora de execução. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *startDate* T *startTime* Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, na hora cheia (com data e hora de início) | 1 | Hora | *startDate* Thh:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas a cada hora na marca de minuto "00", que os aplicativos lógicos calculam a partir da hora de início. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e calculará as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *startDate* T *startTime* Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos após a hora cheia, a cada hora (com data e hora de início) | 1 | Hora | *startDate* T00:15:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas na marca de "15" minutos, que os aplicativos lógicos calculam a partir da hora de início, portanto às 00:15 A.M., 1:15 AM, 2:15 AM e assim por diante. |
| Recorrência | Executar a cada 15 minutos após a hora cheia, a cada hora (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esse agendamento é executado às 00h15, 1h15, 2h15 e assim por diante. Além disso, esse agendamento é equivalente a uma frequência de "Hora" e uma hora de início com "15" minutos. |
| Recorrência | Executar a cada 15 minutos nas marcas de minuto especificadas (sem data e hora de início). | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento não se inicia até a próxima marca de 15 minutos especificada. |
| Recorrência | Execute diariamente às 8:00 *mais* o minuto-Mark de quando você salva seu aplicativo lógico | 1 | Dia | {none} | {não disponível} | 8 | {none} | Sem uma data e hora de início, essa agenda é executada com base no tempo em que você salva o aplicativo lógico (operação PUT). |
| Recorrência | Executar diariamente às 8:00 (com data e hora de início) | 1 | Dia | *startDate* T08:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As ocorrências futuras são executadas diariamente às 8:00. | 
| Recorrência | Executar diariamente às 8:00 AM (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 8 | 00 | Essa agenda é executada às 8:00, todos os dias. |
| Recorrência | Executar diariamente às 8:00 AM e 4:00 PM | 1 | Dia | {none} | {não disponível} | 8, 16 | 0 | |
| Recorrência | Executar diariamente às 8:30, 8:45 AM, 4:30 PM e 4:45 PM | 1 | Dia | {none} | {não disponível} | 8, 16 | 30, 45 | |
| Recorrência | Executar a cada sábado às 5:00 (sem data e hora de início) | 1 | Semana | {none} | "Sábado" | 17 | 0 | Esse agendamento é executado todos os sábados às 17h. |
| Recorrência | Executar todos os sábados às 5:00 PM (com data e hora de início) | 1 | Semana | *startDate* T17:00:00Z | "Sábado" | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas, nesse caso, 9 de setembro de 2017 às 17h. As recorrências futuras serão executadas todos os sábados às 17h. |
| Recorrência | Execute todas as terças-feiras às 17:00, *mais* o minuto-Mark de quando você salva seu aplicativo lógico| 1 | Semana | {none} | "Terça-feira", "Quinta-feira" | 17 | {none} | |
| Recorrência | Executar a cada hora durante o horário de trabalho. | 1 | Semana | {none} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que você deseja. | Selecione os minutos da hora que você deseja. | Por exemplo, se seu horário de trabalho for de 8:00 a 5:00 PM, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia, *mais* "0" como os minutos da hora. |
| Recorrência | Executar uma vez por dia aos finais de semana | 1 | Semana | {none} | "Sábado", "Domingo" | Selecione as horas do dia que você deseja. | Selecione os minutos da hora conforme apropriado. | Esse agendamento é executado aos sábados e domingos no agendamento especificado. |
| Recorrência | Executar a cada 15 minutos, quinzenalmente, somente às segundas-feiras | 2 | Semana | {none} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento é executado em todas as outras segundas-feiras em cada marca de 15 minutos. |
| Recorrência | Executar todos os meses | 1 | Month | *startDate* T *startTime* Z | {não disponível} | {não disponível} | {não disponível} | Essa agenda não é iniciada *antes* da data e hora de início especificadas e calcula as recorrências futuras na data e hora de início. Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. |
| Recorrência | Executar a cada hora durante um dia por mês | 1 | Month | {consulte a observação} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte a observação} | Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. Para controlar os minutos do agendamento da recorrência, especifique os minutos da hora, uma hora de início ou use o tempo de criação. Por exemplo, se a hora de início ou hora de criação for 8h25, esse agendamento será executado às 8h25, 9h25, 10h25 e assim por diante. |
|||||||||

## <a name="next-steps"></a>Próximas etapas

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md)
* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausar fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
