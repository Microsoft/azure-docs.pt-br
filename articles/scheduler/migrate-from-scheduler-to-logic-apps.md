---
title: Migrar do Agendador do Azure para Aplicativos Lógicos do Azure
description: Saiba como é possível substituir trabalhos no Agendador do Azure, que está sendo desativado, com os Aplicativos Lógicos do Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531686"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrar trabalhos do Agendador do Azure para Aplicativos Lógicos do Azure

> [!IMPORTANT]
> Os Aplicativos Lógicos do Azure estão substituindo o Agendador do Azure, que está sendo desativado. Para agendar trabalhos, siga este artigo para migrar para os Aplicativos Lógicos do Azure.

Este artigo mostra como é possível agendar trabalhos únicos e recorrentes criando fluxos de trabalho automatizados com os Aplicativos Lógicos do Azure, em vez de com o Agendador do Azure. Quando você cria trabalhos agendados com Aplicativos Lógicos, você tem estes benefícios:

* Não é necessário se preocupar sobre o conceito de uma *coleção de trabalhos*, porque cada aplicativo lógico é um recurso do Azure separado.

* É possível executar vários trabalhos únicos usando um único aplicativo lógico.

* O serviço dos Aplicativos Lógicos do Azure é compatível com fuso horário e DST (horário de verão).

Para saber mais, confira [O que são Aplicativos Lógicos do Azure?](../logic-apps/logic-apps-overview.md) ou tente criar seu primeiro aplicativo lógico neste início rápido: [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Para disparar seu aplicativo lógico enviando solicitações HTTP, use uma ferramenta como o [aplicativo da área de trabalho Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Agendar trabalhos únicos

É possível executar trabalhos únicos criando apenas um único aplicativo lógico. 

### <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no Designer de Aplicativo Lógico. 

   Para obter as etapas básicas, siga [guia de início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, insira "quando uma solicitação http" como o filtro. Na lista de gatilhos, selecione este gatilho: **Quando uma solicitação HTTP é recebida** 

   ![Adicionar gatilho de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Para o gatilho de solicitação, é possível fornecer opcionalmente um esquema JSON, que ajuda o Designer de Aplicativo Lógico a entender a estrutura das entradas da solicitação de entrada e facilita as saídas para você selecionar posteriormente em seu fluxo de trabalho.

   Para especificar um esquema, insira o esquema na caixa **Esquema JSON do Corpo da Solicitação**, por exemplo: 

   ![Esquema de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se você não tem um esquema, mas tem um conteúdo de exemplo no formato JSON, você pode gerar um esquema com base nesse conteúdo.

   1. No gatilho de Solicitação, escolha **Usar o conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um conteúdo JSON de exemplo**, forneça um conteúdo de exemplo e, em seguida, escolha **Concluído**, por exemplo:

      ![Carga de exemplo](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. No gatilho, escolha **Próxima etapa**. 

1. Na caixa de pesquisa, insira "atrasar até" como seu filtro. Na lista de ações, selecione esta ação: **Atrasar até**

   Essa ação pausa seu fluxo de trabalho do aplicativo lógico até uma data e hora especificadas.

   ![Adicionar ação "Atrasar até"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Insira o carimbo de data/hora para quando você desejar iniciar o fluxo de trabalho d aplicativo lógico. 

   Quando você clica dentro da caixa **Carimbo de data/hora**, a lista de conteúdo dinâmico é exibida; assim, é possível selecionar opcionalmente uma saída do gatilho.

   ![Forneça detalhes de "Atrasar até"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Adicione quaisquer outras ações que você deseja executar selecionando de [Aproximadamente mais de 200 conectores](../connectors/apis-list.md). 

   Por exemplo, é possível incluir uma ação HTTP que envia uma solicitação para uma URL ou ações que funcionam com Filas de Armazenamento, filas ou tópicos de Barramento de Serviço: 

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Quando terminar, salve o aplicativo lógico.

   ![Salve seu aplicativo lógico](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quando você salva seu aplicativo lógico pela primeira vez, a URL do ponto de extremidade do Gatilho de solicitação do seu aplicativo lógico é exibido na caixa **URL DE HTTP POST**. 
   Quando você desejar chamar seu aplicativo lógico e enviar entradas para ele processar, use esta URL como o destino da chamada.

   ![Salvar URL do ponto de extremidade do Gatilho de solicitação](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copie e salve esta URL de ponto de extremidade para poder enviar posteriormente uma solicitação manual que dispara seu aplicativo lógico. 

## <a name="start-a-one-time-job"></a>Iniciar um trabalho único

Para executar manualmente ou disparar um trabalho único, envie uma chamada para a URL de ponto de extremidade para o Gatilho de solicitação do seu aplicativo lógico. Nessa chamada, especifique a entrada ou o conteúdo a ser enviado, que talvez você tenha descrito anteriormente especificando um esquema. 

Por exemplo, usando o aplicativo Postman, é possível criar uma solicitação POST com as configurações semelhantes a este exemplo e, em seguida, escolher **Enviar** para fazer a solicitação.

| Método de solicitação | URL | Corpo | Cabeçalhos |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(aplicativo/json)** <p>Na caixa **raw**, insira o conteúdo que você deseja enviar na solicitação. <p>**Observação**: Essa configuração automaticamente configura a **cabeçalhos** valores. | **Chave**: Tipo de conteúdo <br>**Valor**: aplicativo/json
 |||| 

![Enviar solicitação para disparar manualmente seu aplicativo lógico](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Após enviar a chamada, a resposta do seu aplicativo lógico será exibida na caixa **raw** na guia **Corpo**. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se desejar cancelar o trabalho posteriormente, escolha a guia **Cabeçalhos**. Localize e copie o valor de cabeçalho **x-ms-workflow-run-id** na resposta. 
>
> ![Response](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Cancelar um trabalho único

Nos Aplicativos Lógicos, cada trabalho único é executado como uma instância de execução de aplicativo lógico individual. Para cancelar um trabalho único, é possível usar [Execuções de fluxo de trabalho – Cancelar](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) na API REST dos Aplicativos Lógicos. Quando você envia uma chamada para o gatilho, forneça a [ID de execução do fluxo de trabalho](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Agendar trabalhos recorrentes

### <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco no Designer de Aplicativo Lógico. 

   Para obter as etapas básicas, siga [guia de início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, insira "recorrência" como filtro. Na lista de gatilhos, selecione este gatilho: **Recorrência** 

   ![Adicionar gatilho "Recorrência"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Configure um agendamento mais avançado se desejar.

   ![Agendamento avançado](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Para saber mais sobre opções de agendamento avançadas, confira [Criar e executar tarefas recorrentes e fluxos de trabalho com os Aplicativos Lógicos do Azure](../connectors/connectors-native-recurrence.md)

1. Adicione outras ações desejadas selecionando entre [mais de 200 conectores](../connectors/apis-list.md). No gatilho, escolha **Próxima etapa**. Localize e selecione as ações desejadas.

   Por exemplo, é possível incluir uma ação HTTP que envia uma solicitação para uma URL ou ações que funcionam com Filas de Armazenamento, filas ou tópicos de Barramento de Serviço: 

   ![Ação HTTP](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Quando terminar, salve o aplicativo lógico.

   ![Salve seu aplicativo lógico](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configuração avançada

Veja outras maneiras como você pode personalizar seus trabalhos.

### <a name="retry-policy"></a>Política de repetição

Para controlar a maneira como uma ação tenta ser executada novamente em seu aplicativo lógico quando ocorrem falhas intermitentes, é possível definir a [política de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies) nas configurações de cada ação, por exemplo:

1. Abra o menu (**...**) da ação e selecione **Configurações**.

   ![Abrir configurações da ação](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecione a política de repetição desejada. Para obter informações sobre cada política, confira [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selecionar política de repetição](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Manipular exceções e erros

No Agendador do Azure, se a ação padrão não for executada, será possível executar uma ação alternativa que resolva a condição de erro. Nos Aplicativos Lógicos do Azure, também é possível realizar a mesma tarefa.

1. No Designer de Aplicativo Lógico, em cima da ação que você deseja manipular, mova seu ponteiro sobre a seta entre as etapas e selecione **Adicionar um branch paralelo**. 

   ![Adicionar branch paralelo](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Localize e selecione a ação que você deseja executa como a ação alternativa.

   ![Adicionar ação paralela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Na ação alternativa, abra o menu (**...**) e selecione **Configurar execução depois**.

   ![Configurar execução depois](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Desmarque a caixa da propriedade **for bem-sucedida**. Selecione estas propriedades: **tiver falhado**, **for ignorada** e **tiver atingido o tempo limite**

   ![Configure as propriedades "executar depois"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Quando tiver terminado, escolha **Concluído**.

Para saber mais sobre a manipulação da exceção, confira [Lidar com erros e exceções – Propriedade RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Perguntas frequentes

<a name="retire-date"></a> 

**P**: Quando está desativando o Agendador do Azure? <br>
**R**: O Agendador do Azure está programado para desativação em 30 de setembro de 2019.

**P**: O que acontece com Meus coleções de trabalhos do Agendador e trabalhos após desativa o serviço? <br>
**R**: Todos os trabalhos e coleções de trabalhos do Agendador serão excluídos do sistema.

**P**: É necessário que fazer backup ou executar outras tarefas antes de migrar Meus trabalhos do Agendador para aplicativos lógicos? <br>
**R**: Como prática recomendada, sempre fazer o seu trabalho. Verifique se os aplicativos lógicos criados estão sendo executados conforme o esperado antes de excluir ou desabilitar seus trabalhos do Agendador. 

**P**: Há uma ferramenta que pode me ajudar a migrar Meus trabalhos do Agendador para aplicativos lógicos? <br>
**R**: Cada trabalho do Agendador é exclusivo, portanto, uma ferramenta única não existe. No entanto, vários scripts estarão disponíveis para modificação de acordo para suas necessidades. Para obter disponibilidade de scripts, volte novamente mais tarde.

**P**: Onde posso obter suporte para a migração de Meus trabalhos do Agendador? <br>
**R**: Aqui estão algumas maneiras de obter suporte: 

**Portal do Azure**

Se a assinatura do Azure tiver um plano de suporte pago, será possível criar uma solicitação de suporte técnico no portal do Azure. Caso contrário, será possível selecionar uma opção de suporte diferente.

1. No menu principal do [Portal do Azure](https://portal.azure.com), selecione **Ajuda + suporte**.

1. Em **Suporte**, selecione **Nova solicitação de suporte**. Forneça estes detalhes para sua solicitação:

   | Configuração | Value |
   |---------|-------|
   | **Tipo de problema** | **Técnico** | 
   | **Assinatura** | <*sua-assinatura-Azure*> | 
   | **Serviço** | Em **Monitoramento e gerenciamento**, selecione **Agendador**. | 
   ||| 

1. Selecione a opção de suporte desejada. Se você tiver um plano de suporte pago, escolha **Próximo**.

**Comunidade**

* [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Próximas etapas

* [Crie tarefas e fluxos de trabalho de execução regular com os Aplicativos Lógicos do Azure](../connectors/connectors-native-recurrence.md)
* [Tutorial: Verificar o tráfego com um aplicativo lógico baseado em agendamento](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)