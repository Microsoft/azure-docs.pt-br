---
title: "Agendar tarefas e fluxos de trabalho de execução regular – Aplicativo Lógico do Azure| Microsoft Docs"
description: "Criar e agendar tarefas, ações, fluxos de trabalho, processos e cargas de trabalho de execução regular com aplicativos lógicos"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dd1e22cd538d9113062b77ea2d7ecc18d63fe528
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="schedule-tasks-and-workflows-that-run-regularly-with-logic-apps"></a>Agendar tarefas e fluxos de trabalho que são executados regularmente com aplicativos lógicos

Para agendar tarefas, ações, cargas de trabalho ou processos que são executados regularmente, você pode criar um fluxo de trabalho de aplicativo lógico que começa com o [gatilho](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) **Agenda – Recorrência**. Com esse gatilho, você pode definir uma data e hora para iniciar a recorrência, bem como uma agenda de recorrência para a realização das tarefas, como esses exemplos e muito mais:

* Obter dados internos: [executar um procedimento armazenado de SQL](../connectors/connectors-create-api-sqlazure.md) todos os dias.
* Obter dados externos: efetuar pull de relatórios de previsão do tempo do NOAA a cada 15 minutos.
* Relatar dados: enviar por email um resumo de todos os pedidos que sejam acima de uma quantidade específica na última semana.
* Processar dados: compactar as imagens do dia, todos os dias da semana, fora do horário de pico.
* Limpar dados: excluir todos os tweets com mais de três meses.
* Arquivar dados: enviar faturas por push para um serviço de backup todos os meses.

Esse gatilho oferece suporte a vários padrões, por exemplo:

* Executar imediatamente e repetir a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.
* Iniciar em um momento específico e, então, executar e repetir a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.
* Executar e repetir uma ou mais vezes por dia, por exemplo, às 8h e 17h.
* Executar e repetir a cada semana, mas apenas em dias específicos, como sábado e domingo.
* Executar e repetir a cada semana, mas somente em determinados dias e horas, como de segunda a sexta-feira, às 8h e às 17h.

A cada vez que o gatilho de recorrência é disparado, os Aplicativos Lógicos criam e executam uma nova instância do seu fluxo de trabalho de aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/logic-apps-create-a-logic-app.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Adicionar um gatilho de recorrência em seu aplicativo lógico

1. Entre no [Portal do Azure](https://portal.azure.com). Crie um aplicativo lógico em branco ou saiba [como criar um aplicativo lógico em branco](../logic-apps/logic-apps-create-a-logic-app.md).

2. Depois que o Designer de Aplicativos Lógicos aparecer, na caixa Pesquisar, digite "recorrência" como filtro. Selecione o gatilho **Agenda – Recorrência**. 

   ![Gatilho Agenda – Recorrência](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Esse gatilho já é a primeira etapa no seu aplicativo lógico.

3. Defina o intervalo e a frequência da recorrência. Neste exemplo, defina essas propriedades para que executem o fluxo de trabalho toda semana. 

   ![Definir o intervalo e a frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Para obter mais opções de agendamento, escolha **Mostrar opções avançadas**. 

   ![Mais opções](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Agora você pode definir essas opções: 

   * Defina a data e a hora de início para disparar o gatilho. 
   Se você especificar uma data e hora de início, também poderá aplicar um fuso horário. 

   * Se selecionar "Dia" ou "Semana" como a frequência, você poderá selecionar horários específicos para a recorrência. 

   * Se você selecionar "Semanas", você poderá também selecionar dias específicos da semana.
   
   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Por exemplo, suponha que hoje é segunda-feira, 4 de setembro de 2017. 
   O seguinte gatilho de recorrência não será disparado *antes* da data e hora de início, que é segunda-feira, 18 de setembro de 2017 às 8h PST. 
   No entanto, o agendamento da recorrência está definido para 10h30, 12h30 e 14h30, somente às segundas-feiras. Portanto, a primeira vez que o gatilho será acionado e criará uma instância de fluxo de trabalho do aplicativo lógico será às 10h30. 
   Para saber mais sobre como as horas de início funcionam, consulte estes [exemplos de hora de início](#start-time).
   As execuções futuras acontecerão às 12h30 e às 14h30 no mesmo dia. 
   Cada recorrência cria sua própria instância de fluxo de trabalho. Depois disso, todo o agendamento se repete novamente na próxima segunda-feira. 
   [*Quais são outras ocorrências de exemplo?*](#example-recurrences)

   ![Exemplo de agentamento avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O gatilho mostra uma visualização da recorrência especificada somente quando você seleciona "Dia" ou "Semana" como a frequência.
   
6. Agora crie o restante do seu fluxo de trabalho com as ações ou as instruções de controle do fluxo. Para conhecer mais ações que você pode adicionar, consulte [Conectores](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Detalhes do gatilho

Você pode configurar essas propriedades para o gatilho de recorrência.

| Nome | Obrigatório | Nome da propriedade | Tipo | Descrição | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frequência** | Sim | frequência | Cadeia de caracteres | A unidade de tempo para a recorrência: **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** | 
| **Intervalo** | Sim | intervalo | Número inteiro | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. <p>O intervalo padrão é 1. Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
| **Fuso horário** | Não | timeZone | Cadeia de caracteres | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. | 
| **Hora de início** | Não | startTime | Cadeia de caracteres | Forneça uma hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14h, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como Hora do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** a hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. [*Quais são as maneiras que posso usar a data e hora de início?*](#start-time) | 
| **Nestes dias** | Não | weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você selecionar "Semana", poderá selecionar um ou mais dias nos quais deseja executar o fluxo de trabalho: **segunda-feira**, **terça-feira**, **quarta-feira**, **quinta-feira**, **Sexta-feira**, **sábado** e **domingo** | 
| **A estas horas** | Não | hours | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana", poderá selecionar um ou mais números inteiros, de 0 a 23, como as horas do dia nas quais você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", você obterá 10h, 12h e 14h como as marcas de hora. | 
| **A estes minutos** | Não | minutes | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana", poderá selecionar um ou mais números inteiros, de 0 a 59, como os minutos da hora em que você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. | 
||||| 

## <a name="json-example"></a>Exemplo de JSON

Aqui está uma definição de gatilho de recorrência de exemplo:

``` json
{
    "triggers": {
        "Recurrence": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

## <a name="faq"></a>Perguntas frequentes

<a name="example-recurrences"></a>

**P:** quais são outros agendamentos de recorrência de exemplo? </br>
**R:** veja mais alguns exemplos:

| Recorrência | Intervalo | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Observação |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e, em seguida, calculará as recorrências futuras com base na última hora de execução. | 
| Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. | 
| Executar a cada hora, na hora cheia (com data e hora de início) | 1 | Hora | *startDate*Thh:00:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas. As recorrências futuras são executadas a cada hora na marca de minuto "00". <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. | 
| Executar a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | {none} | {não disponível} | {none} | {none} | Esse agendamento será iniciado imediatamente e calculará as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. | 
| Executar a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *startDate*T*startTime*Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificada e, em seguida, calcula as recorrências futuras com base na última hora de execução. <p>Se a frequência for "Semana" ou "Mês", esse agendamento será executado, respectivamente, somente um dia por semana ou um dia por mês. | 
| Executar a cada 15 minutos após a hora cheia, a cada hora (com data e hora de início) | 1 | Hora | *startDate*T00:15:00Z | {não disponível} | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas, sendo executado às 00h15, 1h15, 2h15 e assim por diante. | 
| Executar a cada 15 minutos após a hora cheia, a cada hora (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esse agendamento é executado às 00h15, 1h15, 2h15 e assim por diante. Além disso, esse agendamento é equivalente a uma frequência de "Hora" e uma hora de início com "15" minutos. | 
| Executar a cada 15 minutos na marca de 15 minutos (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento não se inicia até a próxima marca de 15 minutos especificada. | 
| Executar às 8h, todos os dias (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 8 | {none} | Esse agendamento é executado às 8h, todos os dias, com base no agendamento especificado. | 
| Executar às 8h, todos os dias (com data e hora de início) | 1 | Dia | *startDate*T08:00:00Z | {não disponível} | {none} | {none} | Esse agendamento é executado às 8h, todos os dias, com base na hora de início especificada. | 
| Executar às 8h30, todos os dias (sem data e hora de início) | 1 | Dia | {none} | {não disponível} | 8 | 30 | Esse agendamento é executado às 8h30, todos os dias, com base no agendamento especificado. | 
| Executar às 8h30, todos os dias (com data e hora de início) | 1 | Dia | *startDate*T08:30:00Z | {não disponível} | {none} | {none} | Esse agendamento se inicia na data de início especificada, às 8h30. | 
| Executar às 8h30 e 16h30 todos os dias | 1 | Dia | {none} | {não disponível} | 8, 16 | 30 | | 
| Executar às 8h30 e 16h45 todos os dias | 1 | Dia | {none} | {não disponível} | 8, 16 | 30, 45 | | 
| Executar todos os sábados às 17h (sem data e hora de início) | 1 | Semana | {none} | "Sábado" | 17 | 00 | Esse agendamento é executado todos os sábados às 17h. | 
| Executar todos os sábados às 17h (com data e hora de início) | 1 | Semana | *startDate*T17:00:00Z | "Sábado" | {none} | {none} | Esse agendamento não se inicia *antes* da data e hora de início especificadas, nesse caso, 9 de setembro de 2017 às 17h. As recorrências futuras serão executadas todos os sábados às 17h. | 
| Executar todas as terças e quintas-feiras às 17h | 1 | Semana | {none} | "Terça-feira", "Quinta-feira" | 17 | {none} | Esse agendamento é executado todas as terças-feiras e quintas-feiras às 17h. | 
| Executado a cada hora durante o horário comercial | 1 | Semana | {none} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que você deseja. | Selecione os minutos da hora que você deseja. | Por exemplo, se o horário de trabalho é de 8h às 17h, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se o horário de trabalho é das 8h30 às 17h30, selecione as horas anteriores do dia e selecione também "30" como os minutos da hora. | 
| Executar uma vez por dia aos finais de semana | 1 | Semana | {none} | "Sábado", "Domingo" | Selecione as horas do dia que você deseja. | Selecione os minutos da hora conforme apropriado. | Esse agendamento é executado aos sábados e domingos no agendamento especificado. | 
| Executar a cada 15 minutos, quinzenalmente, somente às segundas-feiras | 2 | Semana | {none} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esse agendamento é executado em todas as outras segundas-feiras em cada marca de 15 minutos. | 
| Executar a cada hora durante um dia por mês | 1 | Mês | {consulte a observação} | {não disponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte a observação} | Se você não especificar uma data e hora de início, esse agendamento usará a data e a hora de criação. Para controlar os minutos do agendamento da recorrência, especifique os minutos da hora, uma hora de início ou use o tempo de criação. Por exemplo, se a hora de início ou hora de criação for 8h25, esse agendamento será executado às 8h25, 9h25, 10h25 e assim por diante. | 
||||||||| 

<a name="start-time"></a>

**P:** quais são as maneiras que posso usar a data e a hora de início? </br>
**R:** aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e hora de início e como o mecanismo dos Aplicativos Lógicos executa essas recorrências:

| Hora de início | Recorrência sem agendamento | Recorrência com agendamento | 
| ---------- | --------------------------- | ------------------------ | 
| {none} | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base na última hora de execução. | Executa a primeira carga de trabalho imediatamente. <p>Executa futuras cargas de trabalho com base no agendamento especificado. | 
| Hora de início no passado | Calcula os tempos de execução com base nos horários de início especificados e descarta as horas de execução anteriores. Executa a primeira carga de trabalho na próxima hora de execução no futuro. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. <p>Para obter mais explicações, consulte o exemplo após essa tabela. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. | 
| Hora de início no presente ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa futuras cargas de trabalho com base em cálculos da última hora de execução. | Executa a primeira carga de trabalho *não antes* da hora de início, com base no agendamento calculado com base na hora de início. <p>Executa futuras cargas de trabalho com base no agendamento especificado. <p>**Observação:** se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para esse agendamento, as horas de execução futuras serão calculadas usando as horas ou os minutos, respectivamente, da primeira hora de execução. | 
||||

**Exemplo de uma hora de início no passado com recorrência, mas sem agendamento** 

| Hora de início | Hora atual | Recorrência | Agenda |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | A cada 2 dias | {none} | 
||||| 

Nesse cenário, o mecanismo dos Aplicativos Lógicos calcula as horas de execução com base na hora de início, descarta as horas de execução passadas e usa as próximas hora de início no futuro para a primeira execução. Após essa primeira execução, as execuções futuras serão baseadas no agendamento calculado a partir da hora de início. Veja a aparência dessa recorrência:

| Hora de início | Primeira hora de execução | Horas de execução futuras | 
| ---------- | ------------ | ---------- | 
| 2017-09-**07** às 14h | 2017-09-**09** às 14h | 2017-09-**11** às 14h </br>2017-09-**13** às 14h </br>2017-09-**15** às 14h </br>e assim por diante...
||||| 

Portanto, neste cenário, não importa o quanto no passado você especifica a hora de início, por exemplo, 2017-09-**05** às 14h ou 2017-09-**01** às 14h, a primeira hora de execução será a mesma.

## <a name="next-steps"></a>Próximas etapas

* [Gatilhos e ações do fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Conectores](../connectors/apis-list.md)
