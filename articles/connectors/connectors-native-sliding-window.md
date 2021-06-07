---
title: Agendar tarefas para lidar com dados contíguos
description: Criar e executar tarefas recorrentes que lidam com dados contíguos usando janelas deslizantes em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010411"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Agendar e executar tarefas para dados contíguos usando o gatilho de janela deslizante nos aplicativos lógicos do Azure

Para executar tarefas, processos ou trabalhos regularmente que devem lidar com dados em partes contíguas, você pode iniciar o fluxo de trabalho do aplicativo lógico com o gatilho de **janela deslizante** . Você pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por qualquer motivo, por exemplo, devido a interrupções ou fluxos de trabalho desabilitados, esse gatilho processará essas recorrências perdidas. Por exemplo, durante a sincronização de dados entre o armazenamento de backup e banco de dado, use o gatilho de janela deslizante para que os dados sejam sincronizados sem incorrer em lacunas. Para obter mais informações sobre os gatilhos e ações de agendamento internos, consulte [agendar e executar tarefas automatizadas e automáticas e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões aos quais esse gatilho dá suporte:

* Executar imediatamente e repetir a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Inicie em uma data e hora específicas, em seguida, execute e repita a cada *n* número de segundos, minutos, horas, dias, semanas ou meses. Com esse gatilho, você pode especificar uma hora de início no passado, que executa todas as recorrências anteriores.

* Retardar cada recorrência por uma duração específica antes da execução.

Para obter as diferenças entre esse gatilho e o gatilho de recorrência ou para obter mais informações sobre como agendar fluxos de trabalho recorrentes, consulte [agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se você quiser disparar seu aplicativo lógico e executar apenas uma vez no futuro, consulte [executar trabalhos apenas uma vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se ainda não estiver familiarizado com aplicativos lógicos, aprenda [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Adicionar gatilho de janela deslizante

1. Entre no [portal do Azure](https://portal.azure.com). Criar um aplicativo lógico em branco.

1. Depois que o designer do aplicativo lógico for exibido, na caixa de pesquisa, insira `sliding window` como seu filtro. Na lista de gatilhos, selecione o gatilho de **janela deslizante** como a primeira etapa no fluxo de trabalho do aplicativo lógico.

   ![Selecione o gatilho "janela deslizante"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Defina o intervalo e a frequência da recorrência. Neste exemplo, defina essas propriedades para que executem o fluxo de trabalho toda semana.

   ![Definir o intervalo e a frequência](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriedade | Nome JSON | Obrigatório | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Integer | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses <br>-Semana: 1-71 semanas <br>–Dia: 1 a 500 dias <br>– Hora: 1 a 12.000 horas <br>– Minuto: 1 a 72.000 minutos <br>– Segundo: 1 a 9.999.999 segundos <p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo**, **Minuto**, **Hora**, **Dia**, **Semana** ou **Mês** |
   ||||||

   ![Opções de recorrência avançadas](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Para obter mais opções de recorrência, abra a lista **Adicionar novo parâmetro** . As opções que você selecionar aparecerão no gatilho após a seleção.

   | Propriedade | Obrigatório | Nome JSON | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Atrasar** | Não | atrasar | String | A duração para atrasar cada recorrência usando a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso horário** | Não | timeZone | String | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Hora de início** | Não | startTime | String | Forneça uma data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário <p>– ou – <p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e selecione um fuso horário como hora padrão do Pacífico. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** a hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para recorrências avançadas, o gatilho não é acionado antes da hora de início. [*Quais são as maneiras que posso usar a data e hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Agora, crie seu fluxo de trabalho restante com outras ações. Para obter mais ações que você pode adicionar, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definição de fluxo de trabalho – janela deslizante

Na definição de fluxo de trabalho subjacente de seu aplicativo lógico, que usa JSON, você pode exibir a definição de gatilho de janela deslizante com as opções escolhidas. Para exibir essa definição, na barra de ferramentas do designer, escolha **exibição de código**. Para retornar ao designer, escolha na barra de ferramentas do designer, **Designer**.

Este exemplo mostra como uma definição de gatilho de janela deslizante pode parecer em uma definição de fluxo de trabalho subjacente em que o atraso de cada recorrência é de cinco segundos para uma recorrência por hora:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md)
* [Conectores para Aplicativos Lógicos](../connectors/apis-list.md)
